# webservcie
[webservice服务](http://www.webxml.com.cn/zh_cn/index.aspx)

webservice就是两个系统之间的远程调用技术，可以跨语言，使用的是http协议，传输数据格式为xml。

## 入门实例

以手机号归属地为例，使用cmd，输入
```
wsimport -s . http://ws.webxml.com.cn/WebServices/MobileCodeWS.asmx?wsdl
```
地址为查询手机号归属地服务的WSDL地址。

此时会生成 java 文件和编译之后的 class 文件。


将生成 java 文件放入我们的工程，根据需求调用就可以。例如
```java
@RestController
@RequestMapping("/webservice")
public class WebserviceController {
	
	@GetMapping("/phone")
	public String queryPhone(String phone) {
		MobileCodeWS ss = new MobileCodeWS();
		MobileCodeWSSoap soap = ss.getMobileCodeWSSoap();
		String result = soap.getMobileCodeInfo(phone, null);
		return result;
	}

}
```

## SOAP
SOAP协议本质上是http协议的POST 方式。只是传输的数据格式发生了变化，数据的传输全部以xml形式。

实际上SOAP是对http协议的基础上增加了一层约束，这个约束就是对请求体数据传输格式的要求。

请求如下：
```
POST /WebServices/MobileCodeWS.asmx HTTP/1.1
Host: ws.webxml.com.cn
Content-Type: text/xml; charset=utf-8
Content-Length: length
SOAPAction: "http://WebXml.com.cn/getMobileCodeInfo"

<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <getMobileCodeInfo xmlns="http://WebXml.com.cn/">
      <mobileCode>string</mobileCode>
      <userID>string</userID>
    </getMobileCodeInfo>
  </soap:Body>
</soap:Envelope>
```

响应如下：
```
HTTP/1.1 200 OK
Content-Type: text/xml; charset=utf-8
Content-Length: length

<?xml version="1.0" encoding="utf-8"?>
<soap:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <getMobileCodeInfoResponse xmlns="http://WebXml.com.cn/">
      <getMobileCodeInfoResult>string</getMobileCodeInfoResult>
    </getMobileCodeInfoResponse>
  </soap:Body>
</soap:Envelope>
```

## WSDL（web服务描述语言）
一个xml文档，用于描述当前服务的信息（服务名称，服务发布地址，服务提供的方法，参数类型和返回值类型等）。

可以根据WSDL文档来查看该服务提供的接口。

使用jdk自带的`wsimport`可以根据wsdl文档生成客户端调用代码，无论服务器端的webservice是用什么语言写的，都将在客户端生成Java代码。

## 发布一个服务
在要发布的类上使用`@WebServcie`注解。
```
@WebService
public class HelloService{

    public String sayHello(String name){
        System.out.println("服务端的sayHello方法被调用了。");
        return "hello" + name;
    }

    public static void main(String[] args){
        String address = "http://192.168.3.187:8080/hello";
        Object implementor = new HelloService();
        Endpoint.publish(address, implementor);
    }
}
```

查看这个服务的WSDL，http://192.168.3.187:8080/hello?wsdl



## CXF框架调用服务
不仅可以使用 webservice 的原始方式调用远程服务，还可以使用CXF进行远程调用。

使用 wsimport 或者是 CXF 提供的 wsdl2java 工具来根据 wsdl 文档生成 java 代码。例如：
```
wsdl2java -d . -p top.yinjinbiao.video http://192.168.3.187:8080/hello?wsdl
```

这时，生成的 Java 文件，只需要将其中的接口文件放到我们的项目中。

在 cxf.xml配置文件中，注册我们的代理服务配置。
```xml
<jaxws:client id="myClient" address="http://192.168.3.187:8080/hello" serviceClass="top.yinjinbiao.video.HelloService">
</jaxws:client>
```