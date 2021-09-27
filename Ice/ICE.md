初始化ice

```
Ice::InitializationData initData;
initData.properties = ICE::createProperties();
initData.properties->setProperty("Ice.MessageSizeMax", "102400");
Ice::Communicator pCommunicator = Ice::initialize(initData);
```

- 直连方式获取服务代理

```
/* 传入远程服务单元的名称、网络协议、IP及端口，获取远程代理，使用stringToProxy方式   */
Ice::ObjectPrx base = pCommnicator->stringToProxy("");
/* 通过checkedCast向下转换，获取service接口的远程，并同时检测根据传入的名称获取的服务单元是否Printer的代理接口 */
ServicePrx servicePrx = ProxyHandle::checkedCast(base);
```

- `iceGrid`方式获取代理

使用`iceGrid`的方式获取服务代理时需要按客户端ice初始化时添加属性

