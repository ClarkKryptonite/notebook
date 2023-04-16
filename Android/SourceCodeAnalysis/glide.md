# Glide 4.11源码

1. Glide构建
2. 给每一个RequestManager绑定一个空白的Fragment来管理声明周期
3. Request对象的构建(请求的宽 高 采样 ...)
4. 请求之前先检测缓存
5. Engine缓存机制的检测：先检测活动缓存ActivityResources，再检测内存缓存LruResourcesCache，如果命中上面的缓存，就会直接回调反馈
6. 上面缓存都没有，就在EngineJob构建一个新的一部任务
7. 执行Request之前，先检测DiskCache中有没有本地磁盘缓存
8. 如果没有磁盘缓存，就通过网络进行请求，返回输入流InputStream
9. 解析输入流InputStream进行采样压缩 最终拿到Bitmap
10. 解析输入流InputStream进行采样压缩，最终拿到Bitmap
11. 对Bitmap进行转换成Drawable
12. 构建磁盘缓存DiskCache
13. 构建内存缓存ActiveResources
14. 无论走多远，最终一定会回到ImageViewTarget，子类显示图片DrawableImageViewTarget