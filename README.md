# Flasgger
## Easy Swagger UI for your Flask API
forked from：https://github.com/rochacbruno/flasgger

[![Build Status](https://travis-ci.org/JKyanbing/flasgger.svg?branch=master)](https://travis-ci.org/JKyanbing/flasgger)
[![Code Health](https://landscape.io/github/JKyanbing/flasgger/master/landscape.svg?style=flat)](https://landscape.io/github/JKyanbing/flasgger/master)
[![Coverage Status](https://coveralls.io/repos/github/JKyanbing/flasgger/badge.svg?branch=master)](https://coveralls.io/github/JKyanbing/flasgger?branch=master)
[![PyPI](https://img.shields.io/pypi/v/flasgger.svg)](https://pypi.python.org/pypi/flasgger)
 <a target="_blank" href="https://www.paypal.com/cgi-bin/webscr?cmd=_donations&amp;business=rochacbruno%40gmail%2ecom&amp;lc=BR&amp;item_name=Flasgger&amp;no_note=0&amp;currency_code=USD&amp;bn=PP%2dDonationsBF%3abtn_donate_SM%2egif%3aNonHostedGuest"><img alt='Donate with Paypal' src='http://www.paypalobjects.com/en_US/i/btn/btn_donate_SM.gif' /></a>

# Installation

dev version

```
pip install https://github.com/JKyanbing/flasgger/tarball/master
```

# Getting started

创建一个app.py文件
```python
from flask import Flask,jsonify
from flask.views import MethodView
from flasgger import Swagger
from flask_restful import Resource
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
        
        
if __name__ == "__main__":
    app.run()
```

在根目录下新建api_docs文件夹，创建文件api.yml
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
consumes: ["application/json; charset=utf-8",
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
      operationId: app.MethodViewExample
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
          $ref: "#definitions/MethodviewExample"


      responses:
        default:
          description: "successful operation"

  /methodview_example/{view_path}:
    get:
      tags:
      - "methodview_example"
      summary: "get methodview example"
      description: ""
      operationId: app.PathViewExample
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
      operationId: app.ResourceExample
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
      operationId: app.ResourceExample
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

