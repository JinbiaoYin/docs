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

			var controls, camera, scene, renderer;
			var cameraCube, sceneCube;
			var textureEquirec, textureCube;
			var cubeMesh, sphereMesh;
			var sphereMaterial;

			init();
			animate();

			function init() {

				// 摄像机

				camera = new THREE.PerspectiveCamera( 70, window.innerWidth / window.innerHeight, 1, 100000 );
				camera.position.set( 0, 0, 1000 );
				cameraCube = new THREE.PerspectiveCamera( 70, window.innerWidth / window.innerHeight, 1, 100000 );

				// 场景

				scene = new THREE.Scene();
				sceneCube = new THREE.Scene();

				// Lights

				var ambient = new THREE.AmbientLight( 0xffffff );
				scene.add( ambient );

				// 文件加载器

				var r = 'picture/';
				var urls = [ r + '1.left.jpg', r + '1.right.jpg',
							 r + '1.top.jpg', r + '1.bottom.jpg',
							 r + '1.back.jpg', r + '1.front.jpg' ];

				textureCube = new THREE.CubeTextureLoader().load( urls );
				textureCube.encoding = THREE.sRGBEncoding;

				// Materials

				var equirectShader = THREE.ShaderLib[ 'equirect' ];

				var equirectMaterial = new THREE.ShaderMaterial( {
					uniforms: THREE.UniformsUtils.clone( equirectShader.uniforms ),
					fragmentShader: equirectShader.fragmentShader,
					vertexShader: equirectShader.vertexShader,
					depthWrite: false,
					side: THREE.BackSide
				} );

				equirectMaterial.uniforms[ 'tEquirect' ].value = textureEquirec;

				// enable code injection for non-built-in material
				Object.defineProperty( equirectMaterial, 'map', {

					get: function () {

						return this.uniforms.tEquirect.value;

					}

				} );

				var cubeShader = THREE.ShaderLib[ 'cube' ];

				var cubeMaterial = new THREE.ShaderMaterial( {
					uniforms: THREE.UniformsUtils.clone( cubeShader.uniforms ),
					fragmentShader: cubeShader.fragmentShader,
					vertexShader: cubeShader.vertexShader,
					depthWrite: false,
					side: THREE.BackSide
				} );

				cubeMaterial.envMap = textureCube;

				// Skybox

				cubeMesh = new THREE.Mesh( new THREE.BoxBufferGeometry( 100, 100, 100 ), cubeMaterial );
				sceneCube.add( cubeMesh );

				//

				var geometry = new THREE.SphereBufferGeometry( 0, 0, 0 );
				sphereMaterial = new THREE.MeshLambertMaterial( { envMap: textureCube } );
				sphereMesh = new THREE.Mesh( geometry, sphereMaterial );

				scene.add( sphereMesh );

				//

				renderer = new THREE.WebGLRenderer();
				renderer.autoClear = false;
				renderer.setPixelRatio( window.devicePixelRatio );
				renderer.setSize( window.innerWidth, window.innerHeight );
				document.body.appendChild( renderer.domElement );

				renderer.outputEncoding = THREE.sRGBEncoding;

				// 方向控制

				controls = new OrbitControls( camera, renderer.domElement );
				controls.minDistance = 500;
				controls.maxDistance = 2500;

				// 自适应监听
				window.addEventListener( 'resize', onWindowResize, false );

			}

			// 根据屏幕大小自适应
			function onWindowResize() {

				camera.aspect = window.innerWidth / window.innerHeight;
				camera.updateProjectionMatrix();

				cameraCube.aspect = window.innerWidth / window.innerHeight;
				cameraCube.updateProjectionMatrix();

				renderer.setSize( window.innerWidth, window.innerHeight );

			}

			//

			function animate() {

				requestAnimationFrame( animate );

				render();

			}

			function render() {

				camera.lookAt( scene.position );
				cameraCube.rotation.copy( camera.rotation );

				renderer.render( sceneCube, cameraCube );
				renderer.render( scene, camera );

			}

		</script>

	</body>
</html>
```

> 主要关心的代码就是，文件加载器的部分，将分割后的图片，按照左，右，上，下，后，前的顺序放在数组中即可。
