# Restful 接口


设计项目资源的 URL

 **资源定位**

资源集合用一个 URL ，具体某个资源用一个 URL。


```bash
/employees         # 资源集合的URL
/employees/56      # 具体某个资源的URL
```

 **资源请求**

使用四种 HTTP 方法可以提供CRUD功能：

- 获取：使用GET方法获取资源。
- 创建：使用POST创建新的资源。
- 更新：使用PUT更新现有资源。（和 POST 近似）
- 删除：使用DELETE删除现有资源。（和 GET 近似）

```bash
[GET] /employees           # 获取全部雇员信息
[GET] /employees/56        # 获取指定雇员信息
[POST] /employees          # 提交雇员信息
[PUT] /employees/56        # 修改指定雇员信息
```

**请求参数**


可选的、复杂的参数用查询字符串表示。
使用小驼峰命名法作为属性标识符。


```bash
GET /employees?state=external
GET /employees?state=internal&maturity=senior
```



**返回状态码**


RESTful Web服务应使用合适的HTTP状态码来响应客户端请求

2xx - 成功 - 一切都很好
4xx - 客户端错误 - 如果客户端发生错误（例如客户端发送无效请求或未被授权）
5xx – 服务器错误 - 如果服务器发生错误（例如，尝试处理请求时出错）

除了合适的状态码之外，还应该在HTTP响应正文中提供有用的错误提示和详细的描述。这是一个例子。请求：