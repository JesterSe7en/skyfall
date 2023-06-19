---
title: "Vulkan Loader Problems with Dear ImGui"
description: "Interfacing our custom Vulkan Loader with Dear ImGui"
pubDate: "June 19 2023"
---

So from previous post, I have removed most of the singleton implementations of my Vulkan Renderer API. It is a bit more cleaner.

However, there is a problem with the Vulkan Loader I have written in the Aria Engine. As of right now, DearImgui loads in vulkan-1 statically by default.

You must define the "No prototypes" compiler definition if you want DearImgui to also dynamically link vulkan-1.
This can be done in the ImGuiconfig.h file or in your build process.

I have chosen the later as I don't need to modify incoming changes from the "docking" branch of ImGui if I decided to update the submodule.

<u>_Background on current build process:_</u> <br>
My existing build script for the DearImgui will use a text file I have created and copy it into a directory called "AriaImGui"

<div style="text-align: center;">
  <img src="/aria-build-script.png" alt="AriaImGui build script" />
</div>

<p style="text-align:center;font-size: smaller;">setup_imgui.py will copy & create AriaImGui directory and its contents.  This is then statically referenced in the Aria Game Engine</p>

AriaImGuiCMakeList.txt contains the actual CMakeList.txt contents. Here is where the "No prototypes" special compiler defintion wil reside.

The current problem is that vulkan-1.dll is getting unloaded whenever Imgui is in the middle of loading functions for its purposes. I need to restructure VulkanLib and/or Vulkan's implementation of DearImgui to fix this.
I am unsure how to do this. I'll need to investigate how to approach this.

Signing off, <br>
-Alex
