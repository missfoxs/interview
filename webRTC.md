在webRTC中主要使用的接口有三个

* navigator.mediaDevices.getUserMedia   获取可用的媒体，产生一个MediaStream 
* RTCPeerConnection   在浏览器间建立连接，并传递实时的视频、音频数据流
* RTCDataChannel   在浏览器之间传输任意数据，是建立在RTCPeerConnection上的，不能单独使用

```javascript
// navigator.mediaDevices对象
// 属性
ondevicechange
// 方法
enumerateDevices()   //  请求一个可用的媒体输入和输出设备的列表，例如麦克风，摄像机，耳机设备等。 返回的 Promise 完成时，会带有一个描述设备的 MediaDeviceInfo 的数组。
getUserMedia()   // 提示用户给予使用媒体输入的许可，媒体输入会产生一个MediaStream


// MediaStrean
// MediaStream 接口是一个媒体内容的流。一个流包含几个轨道，比如视频和音频轨道。
// 事件处理：
MediaStream.onaddtrack     // 这是addtrack事件在这个对象上触发时调用的事件处理器[EventHandler]，这时一个MediaStreamTrack对象被添加到这个流。
MediaStream.onended   //终止时触发的事件
MediaStream.onremovetrack
// 方法
MediaStream.addTrack()  // 存储传入参数 MediaStreamTrack 的一个副本。如果这个轨道已经被添加到了这个媒体流，什么也不会发生; 
MediaStream.clone()
MediaStream.getTracks()
MediaStream.removeTrack()

// RTCPeerConnection 接口代表一个由本地计算机到远端的WebRTC连接。该接口提供了创建，保持，监控，关闭连接的方法的实现。
// 属性
RTCPeerConnection.iceGatheringState // 返回一个iceGatheringState类型的结构体，它描述了这条连接的ICE收集状态
RTCPeerConnection.signalingState   // 返回一个RTC通信状态的结构体，这个结构体描述了本地连接的通信状态。
// 事件处理
RTCPeerConnection.onaddstream   //是收到addstream 事件时调用的事件处理器。
RTCPeerConnection.ondatachannel  //是收到datachannel 事件时调用的事件处理器。 当一个 RTCDataChannel 被添加到连接时，这个事件被触发。
// 方法
RTCPeerConnection.getLocalStreams() // 返回连接的本地媒体流数组。这个数组可能是空数组。

// RTCDataChannel 代表在两者之间建立了一个双向数据通道的连接。建立在peerconnection上，不能单独使用。
// 事件处理
RTCDataChannel.onmessage //当接收到message事件时的事件处理器。当有数据被接收的时候会触发该事件。
// 方法
RTCDataChannel.close()
RTCDataChannel.send()  //将参数中的数据通过channel发送。这个数据可以是DOMString, Blob, ArrayBuffer或者是 ArrayBufferView类型。
```

**信令**：WebRTC是一个完全对等技术，用于实时交换音频、视频和数据，同时提供一个中心警告。如其他地方所讨论的，必须进行一种发现和媒体格式协商，以使不同网络上的两个设备相互定位。这个过程被称为信令，并涉及两个设备连接到第三个共同商定的服务器。通过这个第三方服务器，这两台设备可以相互定位，并交换协商消息。

**信令服务器**：信令服务器的作用是作为一个中间人帮助双方在尽可能少的暴露隐私的情况下建立连接。

**信令过程**

1. 每个Peer创建一个RTCPeerConnection对象，用来表示其WebRTC会话端。
2. 每个Peer建立一个icecandidate事件的响应程序，用来在监测到该事件时将这些candidates通过信令通道发送给另一个Peer。
3. 每个Peer建立一个track事件的响应程序，这个事件会在远程Peer添加一个track到其stream上时被触发。这个响应程序应将tracks和其消受者联系起来，例如`<video>`元素。
4. 呼叫者创建并与接收者共享一个唯一的标识符或某种令牌，使得它们之间的呼叫可以由信令服务器上的代码来识别。此标识符的确切内容和形式取决于您。
5. 每个Peer连接到一个约定的信令服务器，如WebSocket服务器，他们都知道如何与之交换消息。
6. 每个Peer告知信令服务器他们想加入同一WebRTC会话（由步骤4中建立的令牌标识）。
7. 描述，候选地址等 -- 之后会有更多

**连接到信令服务器的方式**

使用websocket，其实使用http也是可以的。

在demo程序中通过用户注册时候连接到websocket服务器，每个用户可以看到所有连接到该服务器的其他用户，然后可以通过点击和他们进行通信。每个用户有一条和信令服务器的专属通道，当A用户向B发送消息时，先到信令服务器，然后服务器通过专属通道（demo中通过用户名识别）将信息发送给B

**ice服务器的作用，如何实现**

使用NAT穿透技术，实现内网穿透

**sdp中的主要内容**

会话描述格式，是关于会话的的说明，包括开始时间，网络协议，媒体信息等。

**createoffer**

创建一个请求来查找远程节点

**setlocaldescription**

保存本地的offer信息

**onicecandidate**

当连接的任意一方成功连接之后，就会触发该事件，发送candidate

**addIceCandidate**

为ice代理提供远程候选

### 调用描述

- ClientA首先创建PeerConnection对象，然后打开本地音视频设备，将音视频数据封装成MediaStream添加到PeerConnection中，添加之后会触发pc的onnegotiationneeded事件，开始创建offer并发送给B。
- ClientA调用PeerConnection的CreateOffer方法创建一个用于offer的SDP对象，SDP对象中保存当前音视频的相关参数。ClientA通过PeerConnection的SetLocalDescription方法将该SDP对象保存起来，并通过Signal服务器发送给ClientB。(在setLocalDescription之后可以修改SDP信息，限制带宽)
- ClientB接收到ClientA发送过的offer SDP对象，通过PeerConnection的SetRemoteDescription方法将其保存起来，并调用PeerConnection的CreateAnswer方法创建一个应答的SDP对象，通过PeerConnection的SetLocalDescription的方法保存该应答SDP对象并将它通过Signal服务器发送给ClientA。
- ClientA接收到ClientB发送过来的应答SDP对象，将其通过PeerConnection的SetRemoteDescription方法保存起来。
- 在SDP信息的offer/answer流程中，ClientA和ClientB已经根据SDP信息创建好相应的音频Channel和视频Channel并开启Candidate数据的收集，Candidate数据可以简单地理解成Client端的IP地址信息（本地IP地址、公网IP地址、Relay服务端分配的地址）。
- 当ClientA收集到Candidate信息后，PeerConnection会通过OnIceCandidate接口给ClientA发送通知，ClientA将收到的Candidate信息通过Signal服务器发送给ClientB，ClientB通过PeerConnection的AddIceCandidate方法保存起来。同样的操作ClientB对ClientA再来一次。
- 这样ClientA和ClientB就已经建立了音视频传输的P2P通道，ClientB接收到ClientA传送过来的音视频流，会通过PeerConnection的OnAddStream（ontrack）回调接口返回一个标识ClientA端音视频流的MediaStream对象，在ClientB端渲染出来即可。同样操作也适应ClientB到ClientA的音视频流的传输。

##### node事件机制

#### nodejs架构

v8: 解决内存栈的问题

事件循环在libuv中，和浏览器中的不同

过程：

1. 执行全局script的代码
2. 执行微任务，优先执行Next Trik队列中的任务，再执行 other microtask 队列中的任务
3. 开始执行宏任务，共6个阶段，依次执行每个阶段的宏任务队列中的所有任务

**宏任务队列执行过程中setTimeout先于setImmediate,  但是setTimeout第二个参数有一个最小值，这个值大于0，所有会出现setTimeout后执行的情况**

node中的require模块



#### webrtc如何修改SDP达到修改音视频码率

sdp的修改要在setLocalDescription之后，sdp中有一个m开头的key，在m之后有i,c两行，这两行是我们需要忽略掉的，再下一行的b = 带宽是我们要设置的

![](C:\Users\xiaoMzz\Documents\修改sdp.png)

#### NAT：用同一个IP实现多个用户的接入
