# computePlanarShadow Demo
A simple demo showing use of the THREE.Mesh.computePlanarShadow() method. Click on the link below: <br>

[computePlanarShadowDemo](http://erichlof.github.io/computePlanarShadow-Demo/computePlanarShadow-Demo.html)  <br>

The function signature is: <br>
```javascript
THREE.Mesh.computePlanarShadow( parentMesh, plane, lightPosition4D );
```
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
Next create a THREE.Mesh object that will be the actual shadow object.  This shadow Mesh will be a copy of its parent Mesh, but its geometry will get squashed down into the specified plane. Use the same geometry as the parent object's geometry. However, the shadow Mesh will use a different special material. Make the shadow material Basic (no lighting) and black in color, but slightly transparent so you can see the plane below the shadow a little bit. Also disable depthWrite and turn off the shadowMesh's frustumCulled property:
```javascript
var shadowMaterial = new THREE.MeshBasicMaterial( { 
		  color: 0x000000,
		  transparent: true,
		  opacity: 0.6,
		  depthWrite: false
} );
var cubeShadow = new THREE.Mesh( cubeGeometry, shadowMaterial );
cubeShadow.frustumCulled = false;
scene.add( cubeShadow );
```
Now create and define a THREE.Plane() object.  This is the plane in which the shadow will appear.  A THREE.Plane() is made up of 2 components: a THREE.Vector3 normal that points away from the plane's surface, and a numerical constant.  This constant can be thought of as the plane's 'distance' from the origin.  Here's how to define a plane in Three.js: 
```javascript
var normalVector = new THREE.Vector3( 0, 1, 0 );
var planeConstant = 0; // this value must match the floor/ground's y position
var groundPlane = new THREE.Plane( normalVector, planeConstant );
```
The above code defines a plane with a normal vector that points straight up from the plane's surface and a distance constant of 0 units from the origin.  This defines a plane that is like the floor or ground beneath us.  This assumes that the floor or ground's position is located at y = 0 units from the scene origin.  If your floor had a y position of -5, then you would also supply -5 as the planeConstant.  The matching of these numbers ensures that the shadow will appear level with the plane, and not incorrectly above or below it. <br>
<br>
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
Although there was quite a bit of setup, the actual usage of the shadow method is just 1 line of code!  Inside the animation loop, call the 'computePlanarShadow' function on the shadowMesh.  This will do all the math magic to squash the shadowMesh's geometry flat and place it flat on the plane:
```javascript
cubeShadow.computePlanarShadow( cubeParent, groundPlane, lightPosition4D );
```
The above method automatically rotates, positions, and scales the shadow to match its parent object, like shadows in the real world do.
<br>
More example usage can be found in the demo's .html source.
