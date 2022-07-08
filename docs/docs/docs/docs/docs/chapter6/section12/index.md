# DispatcherServlet解析请求过程

![](media/1.png)

在doDispatcher的第二步是通过handlerMapping找到handler进而获取[handlerAdapter](../section13/index.md):
```
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
```