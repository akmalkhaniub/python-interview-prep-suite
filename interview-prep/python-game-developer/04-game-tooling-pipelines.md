# 04 - Game Tooling & Pipelines

## Pipeline Engineering

### 1. What are "Content Pipelines"?
**Answer:**
The automated process of converting raw assets (FBX models, WAV audio, high-res PNGs) into engine-ready formats (compressed textures, optimized meshes). Python is the industry standard for scripting these pipelines.

### 2. How do you script for Blender or Maya using Python?
**Answer:**
Using the `bpy` (Blender) or `maya.cmds` modules. You can automate tasks like batch exporting models, checking for topology errors, or generating procedural levels.

### 3. Importance of "Internal Tools" for Designers?
**Answer:**
Designers need to iterate quickly. Custom Python tools (e.g., a Level Editor, Item Database Manager, or Dialogue Tree Builder) allow them to change game balance or content without touching the core C++ engine code.

## Tool Development

### 4. Why use PyQt or PySide for game tools?
**Answer:**
They provide a robust, cross-platform framework for building complex desktop UIs. Most professional game studios use these for their custom internal editors.

### 5. What is a "Plugin" architecture in game engines?
**Answer:**
Designing the engine (or tool) so that new functionality can be added by loading external Python scripts without recompiling the main application.

### 6. How do you implement "Automated Asset Validation"?
**Answer:**
A script that runs in the CI/CD pipeline and checks assets for common errors before they are committed:
- Is the polygon count too high?
- Are texture dimensions a power of 2?
- Are naming conventions followed?
- Does the animation contain all required bones?
