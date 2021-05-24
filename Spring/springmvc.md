#### springmvc处理流程

* dispatchserverlet 接收请求
* 发送给handlemapping，找到具体的handle
* 使用handleadapt执行这个handle
* 返回modleandview
* 视图解析器，找到具体的视图
* 填装modle数据在视图里
* 最后返回客户端

