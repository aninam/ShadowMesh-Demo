# computePlanarShadow Demo
A simple demo showing use of the THREE.Mesh.computePlanarShadow() method. <br>
The function signature is THREE.Mesh.computePlanarShadow( parentMesh, plane, lightPosition4D ); <br>
<br>
This method must take 3 arguments: <br> 
1. A THREE.Mesh() object - the shadow-casting parent mesh <br>
2. A THREE.Plane() object - the plane in which the shadow will appear <br>
3. A THREE.Vector4() object - a 4D vector describing the light's position and whether it is a pointLight or directionalLight. <br>
<br>
Example usage is detailed in the demo's source.
