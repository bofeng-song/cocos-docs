# MeshRenderer Component Reference

The __MeshRenderer__ component is used to display a static 3D model. Set the model mesh with the `Mesh` property, and change the appearance of the model with the `Materials` property.

The component interfaces for the common model are described in [MeshRenderer API](__APIDOC__/en/classes/model.meshrenderer.html).

The component interfaces for the skinning model are described in [SkinnedMeshRenderer API](__APIDOC__/en/classes/model.skinnedmeshrenderer.html).

## MeshRenderer Properties

![meshrenderer properties](meshrenderer-properties.png)

| Property | Description
| :--- | :---
| **materials** | The material used to render the model, one material corresponds to one submesh in the mesh.
| **LightmapSettings** | For baking Lightmap, please refer to [Lightmapping](../../concepts/scene/light/lightmap.md) for details.
| **shadowCastingMode** | Specifies whether the current model will cast shadows, which needs to [enable shadow effect](../../concepts/scene/shadow.md#enable-shadow-effect) in the scene first
| **ReceiveShadow** | Specifies whether the current model will receive and display shadow effects generated by other objects, which needs to [enable shadow effect](../../concepts/scene/shadow.md#enable-shadow-effect) in the scene first. This property takes effect only when the shadow type is __ShadowMap__.
| **mesh** | 3D model assets for rendering.

## Model group rendering

The group rendering function is determined by the [Visibility property](../../editor/components/camera-component.md#set-the-visibility-property) of the camera component and the [Layer property](../../concepts/scene/node-component.md#set-the-layer-property-of-the-node) of the node. Users can set the `Visibility` value through code to complete the group rendering. All nodes belong to the `DEFAULT` layer by default and are visible on all cameras.

> **Note**: please review the [Camera Component](../../editor/components/camera-component.md) documentation for additional information, if required.

## Static batching

The current static batching scheme is static batching at run time. Static batching can be performed by calling `BatchingUtility.batchStaticModel`. This function receives a node, and then merges all `Mesh` in `MeshRenderer` under that node into one, and hangs it under another node.

After batching, the original transform of `MeshRenderer` cannot be changed, but the transform of the root node after batching can be changed. Only nodes that meet the following conditions can be statically batched:

- The child node can only contain `MeshRenderer`.
- The vertex data structure of `Mesh` of `MeshRenderer` under child nodes must be consistent.
- The material of `MeshRenderer` under child nodes must be the same.

## About dynamic batching

The engine currently provides two sets of dynamic batching systems, **instancing batching** and **VB-merging batching**. The two methods cannot coexist, and the priority of instancing is greater than that of dynamic merging VB. To enable batching, simply check the `USE_INSTANCING` or `USE_BATCHING` switch in the material used in the model.

> **Note**: batching can participate in the frustum culling process normally, but transparents model cannot be sorted, which will lead to incorrect blending results. The engine does not explicitly prohibit the approval of transparent objects, and developers can control the trade-offs.

### Instancing batching

The batch through **instancing** is suitable for drawing a large number of dynamic models with the same vertex data. When enabled, drawing will be grouped according to the material and vertex data, and the instanced attributes information will be organized in each group, and then complete the drawing at one time.

> **Note**: for the support and related settings of the skinning model, refer to the [Skeletal Animation Component](../animation/skeletal-animation.md#AboutDynamic-Instancing) documentation.

In addition, inside each group, the instanced attributes supports custom additional instanced attributes, which can pass more per-instance data between different instances (such as the difference in appearance of a diffuse color between different characters, or the influence of wind in a large grass field). This requires the support of custom effects. For more detailed instructions, please refer to the [Syntax Guide](../../material-system/effect-syntax.md#Custom-Instanced-Properties) documentation.

### VB-merging batching

__VB-merging batching__ is suitable for drawing a large number of non-skinned dynamic models with low number of faces and different vertex data. When enabled, drawing will be grouped according to the material, and then the vertex and world transformation information will be merged in each frame of each group, and then completed in batches <sup id="a1">[1](#f1)</sup>.

Operations such as merging vertices per frame introduce a portion of CPU overhead, which is particularly expensive in JavaScript. In addition, it is necessary to remind that the number of __draw calls__ is not as low as possible. Although, it is important to note, minimizing the number of draw calls to the extreme doesn't necessarily lead to the best (or even good) performance <sup id="a2">[2](#f2)</sup>. Optimal performance is often the result of CPU and GPU load balancing, so when using batch functions, be sure to do more tests to identify performance bottlenecks and do targeted optimization.

## Batch best practices

Generally speaking, the priority of the batch system is: **static batching** -> **instancing batching** -> **VB-merging batching**.

The material must be insured that it is consistent, under this premise:

- If you are certain that certain models will remain completely static during the game cycle, use **static batching**.
- If there are a large number of the same model repeated drawing, there is only a relatively controllable small difference between each other, use **instancing batching**.
- If there are a large number of models with very low number of triangles but different vertex data, consider trying **VB-merging batching**.

> **Notes**:
> 1. <b id="f1">[1]</b> Currently use uniforms to upload the batched world transformation matrix, taking into account the WebGL standard uniform quantity limit, the current batch draws up to 10 models, so for a large number of same For the material model, the number of drawcalls is expected to be reduced by up to 10 times after enabling __VB-merging batching__. [↩](#a1)
> 2. <b id="f2">[2]</b> There have been many discussions in the industry on the topic of batching and performance, you can refer to this [nVidia slide](https://www.nvidia.com/docs/IO/8228/BatchBatchBatch.pdf). [↩](#a2)