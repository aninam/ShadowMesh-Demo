# computePlanarShadow Demo
A simple demo showing use of the THREE.Mesh.computePlanarShadow() method. <br>
The function signature is: <br>
THREE.Mesh.computePlanarShadow( parentMesh, plane, lightPosition4D ); <br>
<br>
This method must take 3 arguments: <br> 
1. A THREE.Mesh() object - the shadow-casting parent mesh <br>
2. A THREE.Plane() object - the plane in which the shadow will appear <br>
3. A THREE.Vector4() object - a 4D vector describing the light's position and whether it is a pointLight or directionalLight. <br>
<br>
Example usage: <br>
First create a THREE.js Light object and place it in the THREE.Scene. If it is a directional light, aim it accordingly using lookAt( scene.position ):
```javascript
var light = new THREE.DirectionalLight( 'rgb(255,255,255)', 1 );
light.position.set( 400, 600, 0 );
light.lookAt( scene.position );
scene.add( light );
```
Next create a THREE.Mesh object that will partly block the light, thus creating a shadow:
```javascript
var cubeGeometry = new THREE.BoxGeometry( 1, 1, 1 );
var cubeMaterial = new THREE.MeshLambertMaterial( { color: 0xff0000 } );
var cubeParent = new THREE.Mesh( cubeGeometry, cubeMaterial );
cubeParent.position.y = 5;
scene.add( cubeParent );
```
Next create a THREE.Mesh object that will be the actual shadow object.  This Mesh will be a copy of its parent Mesh, but its geometry will get squashed down into the specified plane.  Make the material Basic and black in color, but transparent so you can see the plane below the shadow a little bit. Also disable depthWrite and turn off the shadowMesh's frustumCulled property:
```javascript
var cubeShadowGeometry = cubeParent.geometry.clone();
var shadowMaterial = new THREE.MeshBasicMaterial( { 
		  color: 0x000000,
		  transparent: true,
		  opacity: 0.6,
		  depthWrite: false
} );
var cubeShadow = new THREE.Mesh( cubeShadowGeometry, shadowMaterial );
cubeShadow.frustumCulled = false;
scene.add( cubeShadow );
```
<br>
Example usage is detailed in the demo's .html source.
