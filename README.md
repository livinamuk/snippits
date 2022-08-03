## DAY 1 - Getting refamiliar

Today I reaquired the Ragdoll codebase, Magnum and its dependancies, and started poking around the Maya renderer from within the FootPrintNode template. 

![alt text](https://www.principiaprogrammatica.com/magnum/mag1.jpg)

I started by spawning my own GL window in the plugin constructor and seeing what information I could pull out of the `MDrawContext`. Above you see a custom triangle rendered with raw GL and my own shader using the Maya projection and modelView matrices into a viewport the same size. I soon realised this wasn't too helpful because no Maya meshes rendered after switching to my own GL context and I could not find a way to cache a handle to the Maya context or renable it. So I began trying to render the same triangle data into the Maya context directly.

![alt text](https://www.principiaprogrammatica.com/magnum/mag2.jpg)

This worked somewhat but I'd lost a lot of control. It was now using whatever shader maya had currently bound (which seems to be a closed box) and the world matrices of whatever mesh it last rendered. 

This is all still pretty unclear to me but I suspect the solution will be to perform our own render pass right before the ImGUI stuff, rendering each mesh individually with raw draw calls using our own custom shader.

```
MStatus status;
M3dView view = M3dView::active3dView(&status);

MMatrix mayaProjMatrix;
MMatrix mayaModelViewMatrix;

view.projectionMatrix(mayaProjMatrix);
view.modelViewMatrix(mayaModelViewMatrix);
```

The projection matrix was easy to get but I'm yet to find the view matrix and I need this badly. Calling `view.mayaModelViewMatrix()` does retrieve it but it's premultiplied with the transform of the last rendered entity and we're not going to know what that was.

Tomorrow I'm going to jump over to the Ragdoll codebase and try to pick apart what ImGUI is doing and hopefully get a triangle rendering somewhere on screen using my own shader. Once that's working it does seem like just a matter of plugging in the Maya projection and view matrices. I'm hoping the depth buffer from the geometry pass is just magically still bound from the Maya render passes but I'll deal with that when I get there.

The ultimate goal is to use Magnum for our draw calls but I'm going to get this working using raw GL calls for now because the less layers of complexitiy the better.    

Over and out.
