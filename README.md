# ShadowMesh Demo
A simple demo showing use of the THREE.ShadowMesh() object. Click on the link below: <br>
[ShadowMesh-Demo](http://erichlof.github.io/ShadowMesh-Demo/ShadowMesh-Demo.html)  <br>

A demo showing use of ShadowMeshes for groups of objects (which are not children of the Scene, but children of a THREE.Group parent). Click on the link below: <br>
[ShadowMeshGroup-Demo](http://erichlof.github.io/ShadowMesh-Demo/ShadowMeshGroup-Demo.html)  <br>

A more advanced Demo with multiple shadow instances for each wall: <br>
[ShadowMesh-MultipleInstancesDemo](http://erichlof.github.io/ShadowMesh-Demo/ShadowMesh-MultipleInstancesDemo.html)  <br>

An example of creating a ShadowMesh: <br>
```javascript
var shadow = new THREE.ShadowMesh( parentMesh );
```
This constuctor must take 1 parameter: <br> 
1. A THREE.Mesh() object - the shadow-casting parent mesh <br>
<br>
An example of updating the newly created ShadowMesh: <br>
```javascript
shadow.update( groundPlane, lightPosition4D );
```
This method must take 2 parameters: <br> 
1. A THREE.Plane() object - the plane in which the shadow will appear <br>
2. A THREE.Vector4() object - a 4D vector describing the light's position and whether it is a pointLight or directionalLight. <br>
<br>
Example setup and usage: <br>
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
Next create a THREE.ShadowMesh object that will follow its parent.  Then add the shadow mesh to the scene:
```javascript
var cubeShadow = new THREE.ShadowMesh( cubeParent );
scene.add( cubeShadow );
```
We need to make a ground mesh so we can see the shadows on it.  Create a very flat box mesh.  Disable the ground material's depthTest so that shadows will always appear on top of the ground:
```javascript
  // the height of this box must be very thin = 0.01, so it can receive shadows correctly
var groundGeometry = new THREE.BoxGeometry( 20, 0.01, 40 );
var groundMaterial = new THREE.MeshLambertMaterial( { 
	color: 0x00aa00,
	depthTest: false
} );
groundMesh = new THREE.Mesh( groundGeometry, groundMaterial );
groundMesh.position.y = 0.0; // this value must be slightly lower the planeConstant parameter (0.01) below
scene.add( groundMesh );
```
Now create and define a THREE.Plane() object.  This is the plane in which the shadow will appear.  A THREE.Plane() is made up of 2 components: a THREE.Vector3 normal that points away from the plane's surface, and a numerical constant.  This constant can be thought of as the plane's 'distance' from the origin.  Here's how to setup and define a plane in Three.js: 
```javascript
var normalVector = new THREE.Vector3( 0, 1, 0 ); // this normal vector points straight up
var planeConstant = 0.01; // this value must be slightly higher than the groundMesh's y position (0.0) above
var groundPlane = new THREE.Plane( normalVector, planeConstant );
```
The above code defines a plane with a normal vector that points straight up from the plane's surface and a distance constant of 0 units from the origin.  This defines a plane that is like the floor or ground beneath us.  This assumes that the groundMesh's position is located at y = 0 units from the scene origin.  The planeConstant value must be slightly higher than the groundMesh's y position.  If your groundMesh had a y position of 5, then you would supply 5.01 as the planeConstant. If your groundMesh had a y position of -5, then you would supply -4.99 as the planeConstant.  The correct setting of these numbers ensures that the shadow will appear on the ground, and not incorrectly above or below it. <br>
<br>
The last initialization step is setting up the 4D vector that defines the light source.  The first 3 components are the familiar x, y, and z position coordinates of the lightsource.  The 4th component, or w, is a value between 0.0 and 1.0 that indicates the amount of divergence that the light rays have from each other.  A value slightly greater than 0.0 specifies no divergence, or parallel rays, like sunlight.  A value of 1.0 specifies maximum divergence, like from a lightbulb or candle.
```javascript
var lightPosition4D = new THREE.Vector4();
lightPosition4D.x = light.position.x;
lightPosition4D.y = light.position.y;
lightPosition4D.z = light.position.z;
lightPosition4D.w = 0.001; // must be slightly > 0, due to 0.0 causing matrixInverse errors
```
The above 'w' value indicates minimum divergence, like sunlight.  For a pointLight however, set the w value like this:
```javascript
lightPosition4D.w = 1.0;
```
Although that was quite a bit of setup, the actual usage of the update method is just 1 line of code!  Inside the animation loop, call the 'update' function on the ShadowMesh:
```javascript
cubeShadow.update( groundPlane, lightPosition4D );
```
The above method takes 2 parameters - the groundPlane (THREE.Plane) that was defined earlier, and the lightPosition4D vector (THREE.Vector4) that was also defined earlier.  The update method automatically flattens the shadow down onto the specified groundPlane THREE.Plane object, then skews it using calculations from the supplied lightPosition4D vector.  It then automatically rotates, positions, and scales the shadow to match its parent object, like shadows in the real world do! <br>
<br>
More example usage can be found in the demo's .html source.
