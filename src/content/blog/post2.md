---
title: "Vulkan Integration with Dear ImGui Support"
description: "Problems with current architecture"
pubDate: "June 16 2023"
---

So the architecture of the current Vulkan implementation for the Aria game engine hits a snag.

As of right now, Aria applications ( the umbrella class to hold all things a game developer wants to do in the Aria Engine), has no reference to the renderer API.

This causes problems as the DearImGui needs references to vulkan objects such as vkInstance, vkDevice, etc. Since there was no object to hold the renderer api, all initalziations of vulkan objects disappeared after creating an Aria application.
This was fine with OpenGl implementation as there was no need to create seperate objects. It was simply a state machine you call into/send commands to manipulate and ultimalty render a frame.

Static functions in the Render class would be calling OpenGL functions such as glDrawElements() to render a frame. There was no need to hold a reference to the renderer api.

```C++
void OpenGlRendererApi::Clear() { glClear(GL_COLOR_BUFFER_BIT); }
void OpenGlRendererApi::SetClearColor(const glm::vec4 color) { glClearColor(color.r, color.g, color.b, color.a); }
void OpenGlRendererApi::DrawIndexed(const Ref<VertexArray> &p_vertex_array) {
  glDrawElements(GL_TRIANGLES, p_vertex_array->GetIndexBuffer()->GetCount(), GL_UNSIGNED_INT, nullptr);
}
```

However, this is not the case with Vulkan. Vulkan required either vkDevice, or vkInstance to be called. So in an effort to retain the vulkan object, I had to create a seperate object to hold the renderer api in the Application class.
So all Aria applications are assoicated with some kind of renderer api; OpenGlRendererApi and VulkanRendererApi sepcifically.

Now comes the problem of the way I structured the Vulkan Renderer Api. There are too many singletons as that how I thought it should be structured. However, reading a few books such as Vulkan Proramming Guide by Sellers and Kessenich, showed me a better overview of how to structure the api.

![Vulkan Hierarchy](/04_sellers_fig01_01.jpg)

<p style="text-align: center;">(Source: Sellers & Kessenich, 2017)</p>

It was better to NOT do singletons everywhere. I made a mistake in the structure of my API wrapper. I'm currently in the process of removing singletons mostly everywhere. The VulkanRendererApi will be composed of vkInstance, vkDevice, and any other vulkan object that are needed to render a frame.
I'm pretty close to getting the DearImgui gui layer to work. Just need to structure the api more appropriatly.

Signing off, <br>
-Alex

Reference:
Sellers, G., &amp; Kessenich, J. M. (2017). Vulkan Programming Guide: The official guide to learning vulkan. Addison-Wesley.
