Sanic
=================================

Sanic 是一个和 Flask 很像的Python 3.5+ 的 web 服务器，特点是速度很快。它基于 magicstack 团队里那些了不起的人所做的工作，灵感来源于 `这篇文章 <https://magic.io/blog/uvloop-blazing-fast-python-networking/>`_。

除了和 Flask 很像以外，Sanic 支持异步请求处理。这意味着你可以使用 Python 3.5 中的新语法 async/await ，编写非阻塞的快速的代码。

Sanic 在 `GitHub <https://github.com/channelcat/sanic/>`_ 上开发。欢迎大家参与！

Sanic 崇尚简洁
---------------

.. code:: python

    from sanic import Sanic
    from sanic.response import json

    app = Sanic()

    @app.route("/")
    async def test(request):
        return json({"hello": "world"})

    if __name__ == "__main__":
        app.run(host="0.0.0.0", port=8000)