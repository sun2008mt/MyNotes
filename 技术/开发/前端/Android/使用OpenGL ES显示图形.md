# 缩写

**CPU（Center Processing Unit）**：中央处理单元

**GPU（Graphics Processing Unit）**：图形处理单元

# 构建OpenGL ES环境

要使用OpenGL ES在Android应用绘制图形，首先需要创建一个视图容器。非常直接的一个方法是实现GLSurfaceView和GLSurfaceView.Renderer类：GLSurfaceView便是一个视图容器，而GLSurfaceView.Renderer则是控制在视图容器中绘制的内容。
另外的实现方式还有TextureView和SurfaceView等。