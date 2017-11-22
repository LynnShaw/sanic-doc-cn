Sanic
=====

Sanic 是一个和 Flask 很像的Python 3.5+ 的 web 服务器，特点是速度很快。它基于 magicstack 团队里那些了不起的人所做的工作，灵感来源于 `这篇文章 <https://magic.io/blog/uvloop-blazing-fast-python-networking/>`_。

除了和 Flask 很像以外，Sanic 支持异步请求处理。这意味着你可以使用 Python 3.5 中的新语法 async/await ，编写非阻塞的快速的代码。

Sanic 在 `GitHub <https://github.com/channelcat/sanic/>`_ 上开发。欢迎大家参与！

文档
-------------

`英文文档 Readthedocs <http://sanic.readthedocs.io/>`_.
`中文文档 Readthedocs <http://http://sanic-doc-cn.readthedocs.io/>`_.

Hello World 例子
-------------------

.. code:: python

    from sanic import Sanic
    from sanic.response import json

    app = Sanic()

    @app.route("/")
    async def test(request):
        return json({"hello": "world"})

    if __name__ == "__main__":
        app.run(host="0.0.0.0", port=8000)

安装
------------

-  ``pip install sanic``

使用 bash 可以安装不包含 uvloop 或 json 的 sanic，你可以为他们的环境变量提供类似 `'y', 'yes', 't', 'true', 'on', '1'` 这种表示“真”的字符串并设置 NO_X 为真，就会停止安装相关特性。

- ``SANIC_NO_UVLOOP=true SANIC_NO_UJSON=true pip install sanic``


例子
--------
`非核心例子 <https://github.com/channelcat/sanic/wiki/Examples/>`_. Sanic 核心功能以外以及插件的例子。

`扩展 <https://github.com/channelcat/sanic/wiki/Extensions/>`_. 社区开发的 Sanic 扩展。

`项目 <https://github.com/channelcat/sanic/wiki/Projects/>`_. Sanic 在项目中使用。


TODO
----
 * http2

限制
-----------
* uvloop 和 httptools 没有 Windows 版本，也没有可替代的轮子 :(

最终目标
--------------

::

                     ▄▄▄▄▄
            ▀▀▀██████▄▄▄       _______________
          ▄▄▄▄▄  █████████▄  /                 \
         ▀▀▀▀█████▌ ▀▐▄ ▀▐█ |   Gotta go fast!  |
       ▀▀█████▄▄ ▀██████▄██ | _________________/
       ▀▄▄▄▄▄  ▀▀█▄▀█════█▀ |/
            ▀▀▀▄  ▀▀███ ▀       ▄▄
         ▄███▀▀██▄████████▄ ▄▀▀▀▀▀▀█▌
       ██▀▄▄▄██▀▄███▀ ▀▀████      ▄██
    ▄▀▀▀▄██▄▀▀▌████▒▒▒▒▒▒███     ▌▄▄▀
    ▌    ▐▀████▐███▒▒▒▒▒▐██▌
    ▀▄▄▄▄▀   ▀▀████▒▒▒▒▄██▀
              ▀▀█████████▀
            ▄▄██▀██████▀█
          ▄██▀     ▀▀▀  █
         ▄█             ▐▌
     ▄▄▄▄█▌              ▀█▄▄▄▄▀▀▄
    ▌     ▐                ▀▀▄▄▄▀
     ▀▀▄▄▀
