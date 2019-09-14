**HTTPS**

- 常称为HTTP over TLS、HTTP over SSL或HTTP Secure

  SSL/TLS建立在tcp和http之间（属于OSI七层模型中的会话层）

- 流程：

  - 建立tcp连接
  - 客户端发送请求
  - 服务端返回证书
  - 客户端校验证书的有效性，然后从中获得公钥，随机生成一个密钥，将该密钥用公钥加密发给服务端
  - 服务端使用私钥解密，拿出密钥
  - 接下来就使用该密钥进行对称加密





**HTTP常见状态码**

- 200 OK 服务器成功处理了请求（这个是我们见到最多的）
- 301/302 Moved Permanently（重定向）请求的URL已移走。Response中应该包含一个Location URL, 说明资源现在所处的位置
- 304 Not Modified（未修改）客户的缓存资源是最新的， 要客户端使用缓存
- 404 Not Found 未找到资源
- 501 Internal Server Error服务器遇到一个错误，使其无法对请求提供服务