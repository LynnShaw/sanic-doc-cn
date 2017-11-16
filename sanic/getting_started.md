# 快速开始

开始之前你需要确保自己使用 Python3.5 或更高的版本，并已经安装了[pip](https://pip.pypa.io/en/stable/installing/)。Sanic 使用新的语法 `async`/`await`，所以早期版本无法使用。

1. 安装 Sanic: `python3 -m pip install sanic`
2. 新建一个名为 `main.py` 的文件，写入下面的代码：

  ```python
  from sanic import Sanic
  from sanic.response import text

  app = Sanic(__name__)

  @app.route("/")
  async def test(request):
      return text('Hello world!')

  app.run(host="0.0.0.0", port=8000, debug=True)
  ```
  
3. 启动服务器: `python3 main.py`
4. 在你的浏览器中打开网址 `http://0.0.0.0:8000` 。 你会看到浏览器显示 *Hello world!*。

现在你有了一个正在工作的 Sanic 服务器！
