---
description: >-
  The document provides a guide for setting up a 3D model for GPU Mesh
  Instancing.
hidden: true
---

# GPU Mesh Instancing

***

**GPU Mesh Instancing** is a method of leveraging a GPU to render duplicated instances of a mesh with the same material in a single draw call. This is useful for optimizing draw calls when populating a scene with multiple trees, bushes, grass or other duplicated objects.

{% embed url="https://files.gitbook.com/v0/b/gitbook-x-prod.appspot.com/o/spaces%2F4pMiThqqrBzfvP8uy5am%2Fuploads%2F0sGbGGScrHUR7WdP8uJk%2F2025-04-15%2013-31-18.mp4?alt=media&token=f70862fe-9a06-4473-9e46-6687403d8831" %}

In this example, a 3D model of grass is created in Blender and then exported into PlayCanvas where it's used for GPU Mesh Instancing. You can download the **Grass\_Grp.glb** file below for testing. The model has already been exported from Blender and is ready for importing into PlayCanvas.

{% file src="../../../.gitbook/assets/Grass_Grp.glb" %}

{% stepper %}
{% step %}
### Prepare the model

A. In a 3D modeling application, such as Blender, create an **Empty**. Give the **Empty** a name. In this example, the **Empty** has been given the name **Grass\_Grp**.

B. Duplicate the instances of the object so that every object uses the same mesh and material. All of the instances need to parented under the **Empty**.

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Export the model

A. Export the model as a **GLTF** **(.glft/.glb)** file. Give the file the same name as the name given to the **Empty.** In this example, the file is named **Grass\_grp.glb**.

B. Expand **Scene Graph** and place checkmark by **GPU Instances**.

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Confirm exported model is configured correctly

A. Visit the site [https://modelviewer.dev/editor/](https://modelviewer.dev/editor/)

B. Drag the 3d model file into the browser and click on the **magnifying glass** icon to view the data.

C. Confirm the **EXT\_mesh\_gpu\_instancing** extension was added. If **EXT\_mesh\_gpu\_instancing** is not visible, then the model was not configured properly in the 3D modeling application. Retry steps 1-2.

<figure><img src="../../../.gitbook/assets/image (653).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Import the model into PlayCanvas

A. Add the model to PlayCanvas by dragging the **Grass\_grp.glb** file to the **Assets** window. The model will unpackage itself, producing multiple files.

B. Drag the **Grass\_grp Template** file to the **Hierarchy**.

C. Click the **Edit Viverse Extension** button.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Add the **GPUMeshInstancing** module

A. In the VIVERSE extension, select the **GPUMeshInstancing** plugin for the **Select plugins** dropdown.

B. Add the **Grass\_Grp.glb Container** to the **GLB container asset id** field.

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
{% endstep %}

{% step %}
### Test project in VIVERSE

A. Publish the project to VIVERSE to see the GPU mesh instancing results.

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>
{% endstep %}
{% endstepper %}
