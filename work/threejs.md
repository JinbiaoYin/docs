# three.js VR
使用 three.js 实现一个类似于 VR 看房的页面

## 下载
[three.js](https://gitee.com/shuaibiao/three.js)
[PTGui](https://gitee.com/shuaibiao/software/blob/master/ptguipro-zh.rar)
[全景图片](https://blog-gitbook.oss-cn-beijing.aliyuncs.com/work/ibms/1.jpg)

> three.js 是制作vr效果的插件。PTGui是将全景图片切割成6张独立的图片，以便渲染成3D效果。这里提供了一张全景图。

## demo
```html
<!DOCTYPE html>
<html lang="en">
	<head>
		<title>three.js webgl - materials - environment maps</title>
		<meta charset="utf-8">
		<meta name="viewport" content="width=device-width, user-scalable=no, minimum-scale=1.0, maximum-scale=1.0">
		<link type="text/css" rel="stylesheet" href="three.js/examples/main.css">
	</head>
	<body>
		<script type="module">

			import * as THREE from './three.js/build/three.module.js';

			import { GUI } from './three.js/examples/jsm/libs/dat.gui.module.js';
			import { OrbitControls } from './three.js/examples/jsm/controls/OrbitControls.js';
 

			// 场景
            var scene = new THREE.Scene();
			// 摄像机
			var camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000 );
			// 渲染器
			var renderer = new THREE.WebGLRenderer();
            renderer.setSize( window.innerWidth, window.innerHeight );
            document.body.appendChild( renderer.domElement );
			
			// 自定义 ----------------------------------
			var r = 'picture/';
				var urls = [ r + '1.left.jpg', r + '1.right.jpg',
							 r + '1.top.jpg', r + '1.bottom.jpg',
							 r + '1.back.jpg', r + '1.front.jpg' ];

			var textureCube = new THREE.CubeTextureLoader().load( urls );
			scene.background = textureCube;
			var controls = new OrbitControls( camera, renderer.domElement );
			controls.minDistance = 500;
			controls.maxDistance = 2500;
			controls.autoRotate = true;
			// 设置镜头默认位置，如果不设置，移动镜头时，会闪到默认坐标 0，0，0
			camera.position.set( 0, 0, 1000 );
			
			// 自定义 ----------------------------------

			// 自适应监听
			window.addEventListener( 'resize', onWindowResize, false );

			
			// 根据屏幕大小自适应
			function onWindowResize() {
				camera.aspect = window.innerWidth / window.innerHeight;	
				camera.updateProjectionMatrix();
				renderer.setSize( window.innerWidth, window.innerHeight );
			}

			// 渲染场景
			function animate() {
                requestAnimationFrame( animate );
                renderer.render( scene, camera );
            }

			animate();

		</script>

	</body>
</html>
```

> 主要关心的代码就是，文件加载器的部分，将分割后的图片，按照左，右，上，下，后，前的顺序放在数组中即可。
