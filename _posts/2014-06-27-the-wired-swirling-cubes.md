---
layout: post
title: The Wired Swirling Cubes
tags:
    - demoscene
excerpt: "This time our aim is to replace the dull triangle with three wireframe cubes placed one next to another, all in our current view. They should slowly rotate on all axes around their center geometrical center with different amounts and the application should close once 5 seconds pass."
---
### Target

This time our aim is to replace the dull triangle with three wireframe cubes placed one next to another, all in our current view. They should slowly rotate on all axes around their center geometrical center with different amounts and the application should close once 5 seconds pass.

### Design

This time we deal with something more than a simple triangle; with structured objects which in 3D parlance are called _meshes_. Welcome our newest discovered entity, the humble `Mesh`. A mesh consists of _vertices_ and _facets_ which are used for storing geometrical data.

One more fact that yields from the requirement is that the mesh should also have an orientation. But what do all cubes have in common? 
Let's start again by writing the client code so we'll get a clearer image on 

{% highlight c++ %}
Mesh[3] meshes;
void createCubes() {
    for (auto& mesh : meshes)
        mesh = MeshBuilder::cube();
}

void updateCubes(double secondsSinceLastFrame) {
    for (auto& mesh : meshes)
        mesh.rotate(rotationAmount * secondsSinceLastFrame);
}

void renderCubes() {
    for (auto& mesh : meshes)
        mesh.orientation.rotate(rotationAmount * secondsSinceLastFrame);
}
{% endhighlight %}
