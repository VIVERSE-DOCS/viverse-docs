---
description: Covers information on how to export your Godot project to web and VIVERSE.
---

# Exporting Godot to VIVERSE

### Project  Settings for Godot

Web support for Godot currently requires the engine version to be Godot 4.1 or higher for successful exports. **Currently using Godot without C# is essential**. However, web export support for C# is expected soon from a recent [announcement](https://godotengine.org/article/live-from-godotcon-boston-web-dotnet-prototype/).\
\
\*Godot 3 web exports are technically supported but not focused on future support.

<figure><img src="../.gitbook/assets/image (714).png" alt="" width="366"><figcaption></figcaption></figure>

***

### Export Settings for Godot

&#x20;Select the desired platform for export. For web exporting, choose `HTML5`. Also **make sure that the export path is set to Build/index.html**. This will lower your packaged build size and rename project exported .html to index.html so VIVERSE can easily find and run the project.&#x20;

<figure><img src="../.gitbook/assets/image (713).png" alt=""><figcaption></figcaption></figure>

From here you can [publish the project using the CLI tool](../javascript-sdk/publishing-to-viverse.md) just like any other platform.&#x20;

<figure><img src="../.gitbook/assets/image (715).png" alt="" width="563"><figcaption></figcaption></figure>

***

### General Limitations of Godot and Web Export

List of Godot WebGL rendering issues: [https://github.com/godotengine/godot/issues/66458](https://github.com/godotengine/godot/issues/66458)

