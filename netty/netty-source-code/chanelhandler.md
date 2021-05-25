# ChanelHandler

![](../../.gitbook/assets/image%20%283%29.png)

## ChannelHandler

Handles an **I/O event** or intercepts an **I/O operation**, and forwards it to its next handler in its ChannelPipeline.

ChannelHandler itself does not provide many methods, but you usually have to implement one of its subtypes: 

* **ChannelInboundHandler** to handle inbound I/O events,  
* **ChannelOutboundHandler** to handle outbound I/O operations. 

Alternatively, the following adapter classes are provided for your convenience: 

* **ChannelInboundHandlerAdapter** to handle inbound I/O events, 
* **ChannelOutboundHandlerAdapter** to handle outbound I/O operations, 
* **ChannelDuplexHandler** to handle both inbound and outbound events

A `ChannelHandler` is provided with a `ChannelHandlerContext` object. A `ChannelHandler` is supposed to interact with the `ChannelPipeline` it belongs to via a context object. Using the context object, the `ChannelHandler` can pass events upstream or downstream, modify the pipeline dynamically, or store the information \(using `AttributeKeys`\) which is specific to the handler.



