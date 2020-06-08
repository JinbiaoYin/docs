# 排序

## 冒泡排序
每一次比较，如果大的数据在前，将相邻的数据做交换。

顶层每次遍历，都会将大的数据排到最后。（大的气泡首先排上来）
```java
public static void main(String[] args) {
    int[] array = {4,2,1,5,3};
    for(int i = 0;i<array.length-1;i++){
        for(int j = 0;j<array.length-1-i;j++){
            if(array[j]>array[j+1]){
                int temp = array[j];
                array[j] = array[j+1];
                array[j+1] = temp; 
            }
        }
    }		
    for (int i = 0;i<array.length;i++) {
        System.out.println(array[i]);
    }		
}
```
