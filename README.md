# computePlanarShadow Demo
A simple demo showing use of the THREE.Mesh.computePlanarShadow() method. Click on the link below: <br>

[computePlanarShadowDemo](http://erichlof.github.io/computePlanarShadow-Demo/computePlanarShadow-Demo.html)  <br>

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
Now create and define a THREE.Plane() object.  This is the plane in which the shadow will appear.  A THREE.Plane() is made up of 2 components: a THREE.Vector3 normal that points away from the plane's surface, and a numerical constant.  The constant can be thought of as the plane's 'distance' from the origin.  Here's how to define it in Three.js: 
```javascript
var upVector = new THREE.Vector3( 0, 1, 0 );
var groundPlane = new THREE.Plane( upVector, 0 );
```
The above code defines a plane with a normal vector that points straight up from the plane's surface and a distance constant of 0 units from the origin.  This defines a plane that is like the floor or ground beneath us.  This assumes that the floor or ground's position is located at y = 0 units from the scene origin.  If your floor had a y component of -5, then you would supply -5 instead of 0 as the second argument in the plane's constructor function.  The matching of these numbers ensures that the shadow will appear level with the plane, and not incorrectly above or below it. <br>
The last initialization step is setting up the 4D vector that defines the light source.  The first 3 components are the familiar x, y, and z position coordinates of the lightsource.  The 4th component, or w, is a value between 0.0 and 1.0 that indicates the amount of divergence that the light rays have from each other.  A value slightly greater than 0.0 specifies no divergence, or parallel rays, like sunlight.  A value of 1.0 specifies maximum divergence, like from a lightbulb or candle.
```javascript
var lightPosition4D = new THREE.Vector4();
lightPosition4D.x = light.position.x;
lightPosition4D.y = light.position.y;
lightPosition4D.z = light.position.z;
lightPosition4D.w = 0.000001; // must be slightly > 0, due to 0.0 causing matrixInverse errors
```
The above 'w' value indicates minimum divergence, like sunlight.  For a pointLight however, set the w value like this:
```javascript
lightPosition4D.w = 1.0;
```
<br>
Example usage is detailed in the demo's .html source.
