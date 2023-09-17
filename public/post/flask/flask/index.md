
<h4>输入一条URL，Flask会如何解析</h4>
利用flask写应用也有一段时间了，这里尝试分析下flask的官网最简单的 hello world 的demo来加深对flask的理解。
```python 
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()
```
上边的这段代码就是flask官网给出的hello world的应用，具体可以参照[这里](http://docs.jinkan.org/docs/flask/quickstart.html#a-minimal-application)。仔细观察一下以上代码，可以看出来，其实对于像flask的这种Web框架，其本质上的任务是建立URL到业务处理函数之间的mapping关系。具体到这个hello world的例子当中建立的就是 URL`\` 到 `hello_world`的映射关系。

  说到这种mapping关系，对数据结构稍微熟悉些的人都会想到hash table.事实上flask中确实使用了hash table作为映射的机制。

```python 
def route(self, rule, **options):
  def decorator(f):
      endpoint = options.pop('endpoint', None)
      self.add_url_rule(rule, endpoint, f, **options)
      return f
  return decorator
```

 这里先看一看`app.route`的代码,这里可以看出来这个其实实际上就是这个Python最常用的装饰器，实际起作用的代码在add_url_rule当中。

```python 
def add_url_rule(self, rule, endpoint=None, view_func=None,provide_automatic_options=None, **options):
  self.url_map.add(rule) #1
  if view_func is not None:
      old_func = self.view_functions.get(endpoint) 
      if old_func is not None and old_func != view_func:
          raise AssertionError('View function mapping is overwriting an ''existing endpoint function: %s' % endpoint)
      self.view_functions[endpoint] = view_func #2
```
点入app_url_rule当中后，发到实际工作的代码处。其中大部分代码是具体一些具体的参数设置，而映射的主要工作如上图的代码所示。在具体的阐明各个代码的具体工作原理之前，首先我们需要知道Flask是基于werkzeug实现的。此文的重点在于Flask，所以不会过多的涉及werkzeug的内容，仅仅将某些是werkzeug实现的功能进行带过。

 前文一直在说Flask实现的功能是将URL对相应的处理函数的映射。其实是不精确的，因为实际上Flask中有三个层次的东西，相应的映射关系为URL->EndPoint->ViewFunction。其中URL即为请求中的网址，而viewfunction 则为相应的逻辑处理函数。至于为什么要在中间加一次endpoint，答案很简单，这样的处理更加灵活。具体可以参见stackoverflow的这篇[回答](https://stackoverflow.com/questions/19261833/what-is-an-endpoint-in-flask)。

  回到上边的代码，其中#1 处实现了url到endpoint的映射的设置(werkzeug来实现)。而#2则是实现了Endpoint->ViewFunction.其中Viewfunction即函数`hello_world`.而对于app.run的这种函数，分析其代码可以发现其相应的处理逻辑。

```python 
def run(self, host=None, port=None, debug=None,load_dotenv=True, **options):
  from werkzeug.serving import run_simple

  try:
      run_simple(host, port, self, **options) #1
  finally:
      # reset the first request information if the development server
      # reset normally.  This makes it possible to restart the server
      # without reloader and that stuff from an interactive shell.
          self._got_first_request = False
```
其中#1即为Werkzeug中相应的逻辑处理，它负责调用self的`__call__`函数，而app的`__call__`则会调用 `wsgi_app`函数。 而wsgi_app即为处理相应request的框架函数，

```python 
def wsgi_app(self, environ, start_response):
  ctx = self.request_context(environ)
  error = None
  try:
    try:
        ctx.push()
        response = self.full_dispatch_request() #1
    except Exception as e:
        error = e
        response = self.handle_exception(e)
    except:
        error = sys.exc_info()[1]
        raise
    return response(environ, start_response)
  finally:
      if self.should_ignore_error(error):
          error = None
      ctx.auto_pop(error)
```

其中 environ为相应的request的请求上下文信息(由WerkZeug)进行保证。 #1出对其进行相应的逻辑处理，然后返回response。而这个response又会交由 werkzeug返还给客服端。



<!--more-->
