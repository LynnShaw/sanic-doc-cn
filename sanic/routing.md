# 路由

路由允许用户对不同的URL端点指定不同的处理函数

一个基本的路由看起来像下面这样，其中 `app` 是 `Sanic` 类的一个实例：

```python
from sanic.response import json

@app.route("/")
async def test(request):
    return json({ "hello": "world" })
```

当 url `http://server.url/` 被访问(服务器基础 url )，末尾的 `/` 会被路由系统匹配到处理函数 `test`, 然后返回一个JSON对象。

Sanic 定义处理函数必须使用 `async def` 语法， 因为它们是异步函数。

## 请求参数

Sanic 有一个基本的路由系统支持请求参数。

要指定一个参数，需要在参数周围加上尖括号，像这样： `<PARAM>`。请求参数将会作为关键字参数被传入到路由处理函数中。

```python
from sanic.response import text

@app.route('/tag/<tag>')
async def tag_handler(request, tag):
	return text('Tag - {}'.format(tag))
```

要为参数指定类型，需要在括号里的参数名后面加上 `:type` 。 如果参数和指定的类型不匹配， Sanic 将会抛出一个 `NotFound` 异常， 最终会生成一个 `404: Page not found` 错误的 URL。

```python
from sanic.response import text

@app.route('/number/<integer_arg:int>')
async def integer_handler(request, integer_arg):
	return text('Integer - {}'.format(integer_arg))

@app.route('/number/<number_arg:number>')
async def number_handler(request, number_arg):
	return text('Number - {}'.format(number_arg))

@app.route('/person/<name:[A-z]+>')
async def person_handler(request, name):
	return text('Person - {}'.format(name))

@app.route('/folder/<folder_id:[A-z0-9]{0,4}>')
async def folder_handler(request, folder_id):
	return text('Folder - {}'.format(folder_id))

```

## HTTP 请求类型

默认情况下，一个URL的路由定义为只能使用GET请求。然而， `@app.route` 装饰器接受一个可选参数 `methods`，允许处理函数以 HTTP 请求方法列表中任意一种请求方式工作。

```python
from sanic.response import text

@app.route('/post', methods=['POST'])
async def post_handler(request):
	return text('POST request - {}'.format(request.json))

@app.route('/get', methods=['GET'])
async def get_handler(request):
	return text('GET request - {}'.format(request.args))

```

还有一个可选的参数 `host` （可以是一个列表或一个字符串）。这个参数用提供的 host(s) 限制了路由（请求头中的host参数需要和提供的 host 匹配网页才可以被访问到）。如果还有一个不带 `host` 参数的路由，默认会使用这个路由。

```python
@app.route('/get', methods=['GET'], host='example.com')
async def get_handler(request):
	return text('GET request - {}'.format(request.args))

# if the host header doesn't match example.com, this route will be used
@app.route('/get', methods=['GET'])
async def get_handler(request):
	return text('GET request in default - {}'.format(request.args))
```

这里还有装饰器的简写方式：

```python
from sanic.response import text

@app.post('/post')
async def post_handler(request):
	return text('POST request - {}'.format(request.json))

@app.get('/get')
async def get_handler(request):
	return text('GET request - {}'.format(request.args))

```
## `add_route` 方法

就像我们看到的，路由一般是由 `@app.route` 装饰器指定的。然而，这个装饰器只是将 `app.add_route` 方法进行了包装， 这个方法这样使用：

```python
from sanic.response import text

# Define the handler functions
async def handler1(request):
	return text('OK')

async def handler2(request, name):
	return text('Folder - {}'.format(name))

async def person_handler2(request, name):
	return text('Person - {}'.format(name))

# Add each handler function as a route
app.add_route(handler1, '/test')
app.add_route(handler2, '/folder/<name>')
app.add_route(person_handler2, '/person/<name:[A-z]>', methods=['GET'])
```

## 用 `url_for` 构造URL

Sanic 提供了 `url_for` 方法用来基于处理器方法名称生成URL。如果你想避免在你的 app 里对 url 路径进行硬编码，这将很有用；作为替代，你可以引用处理器名称，例如：

```python
@app.route('/')
async def index(request):
    # generate a URL for the endpoint `post_handler`
    url = app.url_for('post_handler', post_id=5)
    # the URL is `/posts/5`, redirect to it
    return redirect(url)


@app.route('/posts/<post_id>')
async def post_handler(request, post_id):
    return text('Post - {}'.format(post_id))
```

使用 `url_for` 还需要注意：

- 传入 `url_for` 的关键字参数that are not request parameters will be included in the URL's query string. For example:
```python
url = app.url_for('post_handler', post_id=5, arg_one='one', arg_two='two')
# /posts/5?arg_one=one&arg_two=two
```
- Multivalue argument can be passed to `url_for`. For example:
```python
url = app.url_for('post_handler', post_id=5, arg_one=['one', 'two'])
# /posts/5?arg_one=one&arg_one=two
```
- Also some special arguments (`_anchor`, `_external`, `_scheme`, `_method`, `_server`) passed to `url_for` will have special url building (`_method` is not support now and will be ignored). For example:
```python
url = app.url_for('post_handler', post_id=5, arg_one='one', _anchor='anchor')
# /posts/5?arg_one=one#anchor

url = app.url_for('post_handler', post_id=5, arg_one='one', _external=True)
# //server/posts/5?arg_one=one
# _external requires passed argument _server or SERVER_NAME in app.config or url will be same as no _external

url = app.url_for('post_handler', post_id=5, arg_one='one', _scheme='http', _external=True)
# http://server/posts/5?arg_one=one
# when specifying _scheme, _external must be True

# you can pass all special arguments one time
url = app.url_for('post_handler', post_id=5, arg_one=['one', 'two'], arg_two=2, _anchor='anchor', _scheme='http', _external=True, _server='another_server:8888')
# http://another_server:8888/posts/5?arg_one=one&arg_one=two&arg_two=2#anchor
```
- All valid parameters must be passed to `url_for` to build a URL. If a parameter is not supplied, or if a parameter does not match the specified type, a `URLBuildError` will be thrown.

## WebSocket routes

Routes for the WebSocket protocol can be defined with the `@app.websocket`
decorator:

```python
@app.websocket('/feed')
async def feed(request, ws):
    while True:
        data = 'hello!'
        print('Sending: ' + data)
        await ws.send(data)
        data = await ws.recv()
        print('Received: ' + data)
```

Alternatively, the `app.add_websocket_route` method can be used instead of the
decorator:

```python
async def feed(request, ws):
    pass

app.add_websocket_route(my_websocket_handler, '/feed')
```

Handlers for a WebSocket route are passed the request as first argument, and a
WebSocket protocol object as second argument. The protocol object has `send`
and `recv` methods to send and receive data respectively.

WebSocket support requires the [websockets](https://github.com/aaugustin/websockets)
package by Aymeric Augustin.


## About `strict_slashes`

You can make `routes` strict to trailing slash or not, it's configurable.

```python

# provide default strict_slashes value for all routes
app = Sanic('test_route_strict_slash', strict_slashes=True)

# you can also overwrite strict_slashes value for specific route
@app.get('/get', strict_slashes=False)
def handler(request):
    return text('OK')

# It also works for blueprints
bp = Blueprint('test_bp_strict_slash', strict_slashes=True)

@bp.get('/bp/get', strict_slashes=False)
def handler(request):
    return text('OK')

app.blueprint(bp)
```
