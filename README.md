# Flasgger
## Easy Swagger UI for your Flask API
forked from：https://github.com/rochacbruno/flasgger

# 安装

```
pip install https://github.com/JKyanbing/flasgger/tarball/master
```

# 开始
pycharm 创建一个flask项目（http://www.jetbrains.com/pycharm/documentation/）

编辑app.py文件
```python
from flask import Flask
from flasgger import Swagger
import os

app = Flask(__name__)
app.config['SWAGGER'] = {
    'title': 'My API',
    'uiversion': 2
}
swag = Swagger(app=app,
               template_file=os.path.join(
                   os.getcwd(), 'api_docs', 'api.yml'),
               parse=True,
               automatic_route=True,
               validate=True)
        
        
if __name__ == "__main__":
    app.run()
```

新建一个restfulapi包，编辑__init__.py文件
```python
from flask import jsonify
from flask.views import MethodView
from flask_restful import Resource

class ResourceExample(Resource):
    def get(self):
        return {'method': "get"}, 200

    def post(self):
        return {'method': "post"}, 200

        
class MethodViewExample(MethodView):
    def post(self):
        return jsonify({'method': "post"})



class PathViewExample(MethodView):
    def get(self, view_path):
        return jsonify({"view_path": view_path})
```

新建api_docs文件夹，创建文件api.yml
```yaml
swagger: "2.0"
info:
  description: "This is a sample server Petstore server.  You can find out more about     Swagger at [http://swagger.io](http://swagger.io) or on [irc.freenode.net, #swagger](http://swagger.io/irc/).      For this sample, you can use the api key `special-key` to test the authorization     filters."
  version: "1.0.0"
  title: "Flask api"
  termsOfService: "http://swagger.io/terms/"
  contact:
    email: "apiteam@swagger.io"
  license:
    name: "Apache 2.0"
    url: "http://www.apache.org/licenses/LICENSE-2.0.html"
basePath: "/v1"
produces: ["application/json; charset=utf-8"]
consumes: ["application/json",
            "application/json; charset=utf-8",
            "text/xml; charset=utf-8"]

tags:
- name: "methodview_example"
  description: "继承Flask MethodView类模板"

- name: "resource_example"
  description: "继承Flask_Restful Resource类模板"

paths:
  /methodview_example:
    post:
      tags:
      - "methodview_example"
      summary: "create a methodview example"
      description: ""
      operationId: restfulapi.MethodViewExample
      parameters:
      - name: "api_key"
        in: "header"
        description: "authenticated api_key"
        required: true
        type: "string"
      - in: "body"
        name: "object"
        description: "Created example object"
        required: true
        schema:
          $ref: "#/definitions/MethodviewExample"

      responses:
        default:
          description: "successful operation"

  /methodview_example/{view_path}:
    get:
      tags:
      - "methodview_example"
      summary: "get methodview example"
      description: ""
      operationId: restfulapi.PathViewExample
      parameters:
      - name: "api_key"
        in: "header"
        description: "authenticated api_key"
        required: true
        type: "string"
      - name: "view_path"
        in: "path"
        description: "path params"
        required: true
        type: "string"
      - name: "example_id"
        in: "query"
        description: "example id"
        required: true
        type: "string"
      responses:
        200:
          description: "successful operation"
          schema:
            type: "object"
            properties:
              parameter:
                type: "string"

  /resource_example:
    post:
      tags:
      - "resource_example"
      summary: "Create resource example"
      description: ""
      operationId: restfulapi.ResourceExample
      produces:
      - "application/json"
      parameters:
      - name: "additionalMetadata"
        in: "formData"
        description: "Additional data to pass to server"
        required: false
        type: "string"
      - name: "file"
        in: "formData"
        description: "file to upload"
        required: false
        type: "file"

      responses:
        default:
          description: "successful operation"

    get:
      tags:
      - "resource_example"
      summary: "Get example by example_id"
      description: ""
      operationId: restfulapi.ResourceExample
      parameters:
      - name: "example_id"
        in: "query"
        description: "query params"
        required: true
        type: "string"
      responses:
        200:
          description: "successful operation"
          schema:
            type: "object"
            properties:
              parameter:
                type: "string"

definitions:
  MethodviewExample:
    required:
    - id
    - name
    - remark
    type: "object"
    properties:
      id:
        type: "integer"
        format: "int64"
      name:
        type: "string"
      remark:
        type: "string"
```

运行项目，打开：http://localhost:5000/apidocs/

# 简单说明

目前不支持使用装饰器route修饰的路由
```
@app.rout('/index')
def index():
    pass
```

*参数automatic_route=True时，根据api.yml中paths的operationId自动注册路由，所以，
    operationId必须指向一个flask.views.MethodView 或者 flask_restful.Resource的子类，
    并且是类相对所在包的完整路径。
  例如：restfulapi包中的MethodViewExample
```
operationId: restfulapi.MethodViewExample
```
*参数validate=True时，flasgger会根据api.yml中的parameters规则，对实际请求的参数进行校验。
    header、path、query中的参数，当required: true时校验参数；
    body中的参数，则要提供required列表；
    写法查考：
    
 ```
 parameters:
    - name: "api_key"
      in: "header"
      description: "authenticated api_key"
      required: true
      type: "string"
    
    - name: "view_path"
      in: "path"
      description: "path参数"
      required: true
      type: "string"
    
    - name: "example_id"
      in: "query"
      description: "目标example id"
      required: true
      type: "string"
    
    - in: "body"
      name: "object"
      description: "Created user object"
      required: true
      schema:
        required:
        - id
        - name
        - remark
        type: "object"
        properties:
          id:
            type: "integer"
            format: "int64"
          name:
            type: "string"
          remark:
            type: "string"
```
*当前不对formData中的参数进行校验，如果需要使用formData，请写required: false；否则，可能产生无法预测的bug


