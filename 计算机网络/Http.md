### HTTP状态码
1. 1XX（通知，很少使用）
2. 2XX
    * 200   服务器成功处理、请求并提供了请求的网页
    * 204   服务器成功处理了请求，但没有返回任何内容
3. 3XX
    * 301   请求的网页己永久移动到新位置，URLs发生变， 就要有301重定向
    * 302   请求的网页临时移动到新位置，搜索引擎索引中保存原来的URL
    * 304   如果网页自请求者上次请求后没有更新，304代码会告诉搜索引擎
4. 4XX
    * 400   服务器不理解请求的语法
    * 403   服务器拒绝请求
    * 404   服务器找不到请求的网页
    * 410   请求的资源永久删除后，服务器返回此响应
5. 5XX
    * 500   服务器遇到错误，无法完成请求
    * 503   服务器目前无法使用