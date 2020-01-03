 https://www.jianshu.com/p/29868fb82890 

# TCP三次握手、四次挥手及状态转换详解



 客户端和服务端通信前要进行连接，“3次握手”的作用就是`双方都能明确自己和对方的收、发能力是正常的`。 



## 为什么建立连接是三次握手

 第一、二次握手后，服务端并不知道客户端的接收能力以及自己的发送能力是否正常。而在第三次握手时，服务端收到了客户端对第二次握手作的回应。从服务端的角度，我在第二次握手时的响应数据发送出去了，客户端接收到了。所以，我的发送能力是正常的。而客户端的接收能力也是正常的。 



其实3次握手的目的并不只是让通信双方都了解到一个连接正在建立，还在于利用数据包的选项来传输特殊的信息，交换初始序列号ISN。

3次握手是指发送了3个报文段，4次挥手是指发送了4个报文段。注意，SYN和FIN段都是会利用重传进行可靠传输的





## 为什么建立连接是三次握手，而关闭连接却是四次挥手呢？

这是因为服务端在LISTEN状态下，收到建立连接请求的SYN报文后，把ACK和SYN放在一个报文里发送给客户端。而关闭连接时，当收到对方的FIN报文时，仅仅表示对方不再发送数据了但是还能接收数据，己方是否现在关闭发送数据通道，需要上层应用来决定，因此，己方ACK和FIN一般都会分开发送。





### TCP/IP 通信的的三次握手图解

![img](https:////upload-images.jianshu.io/upload_images/1446087-a270ce1e41ec8520.png?imageMogr2/auto-orient/strip|imageView2/2/w/875/format/webp)

TCP 通信握手图解

TCP是主机对主机层的传输控制协议，提供可靠的连接服务：

位码即tcp标志位，有6种标示：

- SYN(synchronous建立联机)
- ACK(acknowledgement 确认)
- PSH(push传送)
- FIN(finish结束)
- RST(reset重置)
- URG(urgent紧急)
- Sequence number(seq 顺序号码)
- Acknowledge number(ack 确认号码)

### TCP握手详解

##### 建立连接协议（三次握手）：

1. 第一次握手：客户端发送syn包（syn=x）的数据包到服务器，并进入`SYN_SEND`状态，等待服务器确认；
2. 第二次握手：服务器收到syn包，必须确认客户的SYN（ack=x+1），同时自己也发送一个SYN包（syn=y），即SYN+ACK包，此时服务器进入`SYN_RECV`状态；
3. 第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=y+1)，此包发送完毕，客户端和服务器进入`ESTABLISHED`状态，完成三次握手。

**握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始传送数据**。理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连接之前，TCP连接都将被一直保持下去。

##### 连接终止协议（四次握手）

**由于TCP连接是全双工的，因此每个方向都必须单独进行关闭。这原则是当一方完成它的数据发送任务后就能发送一个FIN来终止这个方向的连接。**收到一个 FIN只意味着这一方向上没有数据流动，一个TCP连接在收到一个FIN后仍能发送数据。首先进行关闭的一方将执行主动关闭，而另一方执行被动关闭。

1. 第一次挥手：主动关闭方发送一个FIN，用来关闭主动方到被动关闭方的数据传送，也就是主动关闭方告诉被动关闭方：我已经不会再给你发数据了(当然，在fin包之前发送出去的数据，如果没有收到对应的ack确认报文，主动关闭方依然会重发这些数据)，**但是，此时主动关闭方还可以接受数据。** 
2. 第二次挥手：被动关闭方收到FIN包后，发送一个ACK给对方，确认序号为收到序号+1**（与SYN相同，一个FIN占用一个序号， SYN 和 FIN 都有seq序号）**。
3. 第三次挥手：被动关闭方发送一个FIN，用来关闭被动关闭方到主动关闭方的数据传送，也就是告诉主动关闭方，我的数据也发送完了，不会再给你发数据了。
4. 第四次挥手：主动关闭方收到FIN后，发送一个ACK给被动关闭方，确认序号为收到序号+1，至此，完成四次挥手。

### TCP状态转换图

![img](https:////upload-images.jianshu.io/upload_images/1446087-dce2449a800fa763.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/731/format/webp)

TCP状态转换图

状态说明：
 `CLOSED`: 这个表示初始状态。

`LISTEN`（服务器）: 这个也是非常容易理解的一个状态，表示服务器端的某个SOCKET处于监听状态，可以接受连接了。

`SYN_RCVD`（服务器）: 这个状态表示接受到了SYN报文，在正常情况下，这个状态是服务器端的SOCKET在建立TCP连接时的三次握手会话过程中的一个中间状态，很短暂，基本上用netstat你是很难看到这种状态的，除非你特意写了一个客户端测试程序，故意将三次TCP握手过程中最后一个ACK报文不予发送。因此这种状态时，当收到客户端的ACK报文后，它会进入到ESTABLISHED状态。

`SYN_SENT`: 这个状态与`SYN_RCVD`呼应，当客户端SOCKET执行CONNECT连接时，它首先发送SYN报文，因此也随即它会进入到了SYN_SENT状态，并等待服务端的发送三次握手中的第2个报文。SYN_SENT状态表示客户端已发送SYN报文。

`ESTABLISHED`：这个容易理解了，表示连接已经建立了。

`FIN_WAIT_1`: 这个状态要好好解释一下，其实FIN_WAIT_1和FIN_WAIT_2状态的真正含义都是表示等待对方的FIN报文。而这两种状态的区别是：FIN_WAIT_1状态实际上是当SOCKET在ESTABLISHED状态时，它想主动关闭连接，向对方发送了FIN报文，此时该SOCKET即进入到FIN_WAIT_1状态。而当对方回应ACK报文后，则进入到FIN_WAIT_2状态，当然在实际的正常情况下，无论对方何种情况下，都应该马上回应ACK报文，所以FIN_WAIT_1状态一般是比较难见到的，而FIN_WAIT_2状态还有时常常可以用netstat看到。

`FIN_WAIT_2`：上面已经详细解释了这种状态，实际上FIN_WAIT_2状态下的SOCKET，表示半连接，也即有一方要求close连接，但另外还告诉对方，我暂时还有点数据需要传送给你，稍后再关闭连接。

`TIME_WAIT`: 表示收到了对方的FIN报文，并发送出了ACK报文，就等2MSL后即可回到CLOSED可用状态了。如果FIN_WAIT_1状态下，收到了对方同时带FIN标志和ACK标志的报文时，可以直接进入到TIME_WAIT状态，而无须经过FIN_WAIT_2状态。

**注:MSL(最大分段生存期)指明TCP报文在Internet上最长生存时间,每个具体的TCP实现都必须选择一个确定的MSL值.RFC 1122建议是2分钟,但BSD传统实现采用了30秒.TIME_WAIT 状态最大保持时间是2 \* MSL,也就是1-4分钟**

结论：在TIME_WAIT下等待2MSL，只是为了尽最大努力保证四次握手正常关闭。确保老的报文段在网络中消失，不会影响新建立的连接.

`CLOSING`: 这种状态比较特殊，实际情况中应该是很少见，属于一种比较罕见的例外状态。正常情况下，当你发送FIN报文后，按理来说是应该先收到（或同时收到）对方的ACK报文，再收到对方的FIN报文。但是CLOSING状态表示你发送FIN报文后，并没有收到对方的ACK报文，反而却也收到了对方的FIN报文。什么情况下会出现此种情况呢？其实细想一下，也不难得出结论：那就是如果双方几乎在同时close一个SOCKET的话，那么就出现了双方同时发送FIN报文的情况，也即会出现CLOSING状态，表示双方都正在关闭SOCKET连接。

`CLOSE_WAIT`: 这种状态的含义其实是表示在等待关闭。怎么理解呢？当对方close一个SOCKET后发送FIN报文给自己，你系统毫无疑问地会回应一个ACK报文给对方，此时则进入到CLOSE_WAIT状态。**接下来呢，实际上你真正需要考虑的事情是察看你是否还有数据发送给对方，如果没有的话，那么你也就可以close这个SOCKET，发送FIN报文给对方，也即关闭连接。所以你在CLOSE_WAIT状态下，需要完成的事情是等待你去关闭连接。**

`LAST_ACK`: 这个状态还是比较容易好理解的，它是被动关闭一方在发送FIN报文后，最后等待对方的ACK报文。当收到ACK报文后，也即可以进入到CLOSED可用状态了。

### 思考

思考：

##### 1、 为什么建立连接协议是三次握手，而关闭连接却是四次握手呢？

这是因为服务端的LISTEN状态下的SOCKET当收到SYN报文的建连请求后，**它可以把ACK和SYN（ACK起应答作用，而SYN起同步作用**）放在一个报文里来发送。但关闭连接时，当收到对方的FIN报文通知时，它仅仅表示对方没有数据发送给你了；但未必你所有的数据都全部发送给对方了，所以你可能未必会马上会关闭SOCKET,也即你可能还需要发送一些数据给对方之后，再发送FIN报文给对方来表示你同意现在可以关闭连接了，**所以它这里的ACK报文和FIN报文多数情况下都是分开发送的。**

##### 2. 为什么不能用两次握手进行连接？

我们知道，3次握手完成两个重要的功能，既要双方做好发送数据的准备工作(双方都知道彼此已准备好)，也要允许双方就初始序列号进行协商，这个序列号在握手过程中被发送和确认。
 现在把三次握手改成仅需要两次握手，死锁是可能发生的。作为例子，考虑计算机S和C之间的通信，假定C给S发送一个连接请求分组，S收到了这个分组，并发送了确认应答分组。按照两次握手的协定，S认为连接已经成功地建立了，可以开始发送数据分组。可是，C在  S的应答分组在传输中被丢失的情况下，将不知道S是否已准备好，不知道S建立什么样的序列号，C甚至怀疑S是否收到自己的连接请求分组。在这种情况下，C认为连接还未建立成功，将忽略S发来的任何数据分组，只等待连接确认应答分组。而S在发出的数据分组超时后，重复发送同样的数据分组。这样就形成了死锁。
 就好比打电话， A打给B， A要告诉B ，我打给你了， B要回应给A听，OK，连接成功了；然后B也要得到A的确认，才能开始正式通话

##### 3. 为什么TIME_WAIT状态还需要等2MSL后才能返回到CLOSED状态？

什么是2MSL？MSL即Maximum Segment Lifetime，也就是报文最大生存时间，引用《TCP/IP详解》中的话：“**它(MSL)是任何报文段被丢弃前在网络内的最长时间**。”那么，2MSL也就是这个时间的2倍，当TCP连接完成四个报文段的交换时，主动关闭的一方将继续等待一定时间(2-4分钟)，即使两端的应用程序结束。

###### 1）为什么需要这个2MSL呢？

- 1. 虽然双方都同意关闭连接了，而且握手的4个报文也都协调和发送完毕，按理可以直接回到CLOSED状态（就好比从SYN_SEND状态到ESTABLISH状态那样）；但是因为我们必须要假想网络是不可靠的，你无法保证你最后发送的ACK报文会一定被对方收到，因此对方处于LAST_ACK状态下的SOCKET可能会因为超时未收到ACK报文，而重发FIN报文，**所以这个TIME_WAIT状态的作用就是用来重发可能丢失的ACK报文。** 
- 1. 报文可能会被混淆，意思是说，其他时候的连接可能会被当作本次的连接。直接引用《The TCP/IP Guide》的说法：The second is to provide a “buffering period” between the end of this connection and any subsequent ones. If not for this period, it is possible that packets from different connections could be mixed, creating confusion.

###### 2）TIME_WAIT状态存在的理由：

- 1.  **可靠地实现TCP全双工连接的终止**
      在进行关闭连接四路握手协议时，最后的ACK是由主动关闭端发出的，如果这个最终的ACK丢失，服务器将重发最终的FIN，因此客户端必须维护状态信息允许它重发最终的ACK。**如果不维持这个状态信息，那么客户端将响应RST分节**，服务器将此分节解释成一个错误（在java中会抛出connection reset的SocketException)。因而，要实现TCP全双工连接的正常终止，必须处理终止序列四个分节中任何一个分节的丢失情况，主动关闭 的客户端必须维持状态信息进入TIME_WAIT状态。
- 1. 允许老的重复分节在网络中消逝
      TCP分节可能由于路由器异常而“迷途”，在迷途期间，TCP发送端可能因确认超时而重发这个分节，迷途的分节在路由器修复后也会被送到最终目的地，这个 原来的迷途分节就称为lost duplicate。在关闭一个TCP连接后，马上又重新建立起一个相同的IP地址和端口之间的TCP连接，后一个连接被称为前一个连接的化身 （incarnation)，那么有可能出现这种情况，前一个连接的迷途重复分组在前一个连接终止后出现，从而被误解成从属于新的化身。为了避免这个情 况，TCP不允许处于TIME_WAIT状态的连接启动一个新的化身，因为TIME_WAIT状态持续2MSL，就可以保证当成功建立一个TCP连接的时 候，来自连接先前化身的重复分组已经在网络中消逝。
-  **对第二点的补充说明**. 防止lost duplicate对后续新建正常链接的传输造成破坏。lost duplicate在实际的网络中非常常见，经常是由于路由器产生故障，路径无法收敛，导致一个packet在路由器A，B，C之间做类似死循环的跳转。IP头部有个TTL，限制了一个包在网络中的最大跳数，因此这个包有两种命运，要么最后TTL变为0，在网络中消失；要么TTL在变为0之前路由器路径收敛，它凭借剩余的TTL跳数终于到达目的地。但非常可惜的是TCP通过超时重传机制在早些时候发送了一个跟它一模一样的包，并先于它达到了目的地，因此它的命运也就注定被TCP协议栈抛弃。另外一个概念叫做incarnation connection，指跟上次的socket pair一摸一样的新连接，叫做incarnation of previous connection。lost duplicate加上incarnation connection，则会对我们的传输造成致命的错误。大家都知道TCP是流式的，所有包到达的顺序是不一致的，依靠序列号由TCP协议栈做顺序的拼接；假设一个incarnation connection这时收到的seq=1000, 来了一个lost duplicate为seq=1000, len=1000, 则tcp认为这个lost duplicate合法，并存放入了receive buffer，导致传输出现错误。通过一个2MSL TIME_WAIT状态，确保所有的lost duplicate都会消失掉，避免对新连接造成错误。

###### 3）该状态为什么设计在主动关闭这一方：

（1）发最后ack的是主动关闭一方
 （2）只要有一方保持TIME_WAIT状态，就能起到避免incarnation connection在2MSL内的重新建立，不需要两方都有

###### 4）对服务器的影响

当某个连接的一端处于TIME_WAIT状态时，该连接将不能再被使用。事实上，对于我们比较有现实意义的是，这个端口将不能再被使用。某个端口处于TIME_WAIT状态(其实应该是这个连接)时，这意味着这个TCP连接并没有断开(完全断开)，那么，如果你bind这个端口，就会失败。对于服务器而言，如果服务器突然crash掉了，那么它将无法在2MSL内重新启动，因为bind会失败。**解决这个问题的一个方法就是设置socket的SO_REUSEADDR选项。这个选项意味着你可以重用一个地址。**

###### 5）如何正确对待2MSL TIME_WAIT

RFC要求socket pair在处于TIME_WAIT时，不能再起一个incarnation connection。但绝大部分TCP实现，强加了更为严格的限制。在2MSL等待期间，socket中使用的本地端口在默认情况下不能再被使用。若A 10.234.5.5:1234和B 10.55.55.60:6666建立了连接，A主动关闭，那么在A端只要port为1234，无论对方的port和ip是什么，都不允许再起服务。显而易见这是比RFC更为严格的限制，RFC仅仅是要求socket pair不一致，而实现当中只要这个port处于TIME_WAIT，就不允许起连接。这个限制对主动打开方来说是无所谓的，因为一般用的是临时端口；但对于被动打开方，一般是server，就悲剧了，因为server一般是熟知端口。比如http，一般端口是80，不可能允许这个服务在2MSL内不能起来。解决方案是给服务器的socket设置SO_REUSEADDR选项，这样的话就算熟知端口处于TIME_WAIT状态，在这个端口上依旧可以将服务启动。当然，虽然有了SO_REUSEADDR选项，但sockt pair这个限制依旧存在。比如上面的例子，A通过SO_REUSEADDR选项依旧在1234端口上起了监听，但这时我们若是从B通过6666端口去连它，TCP协议会告诉我们连接失败，原因为Address already in use.

### Time_await 详解

因为TCP连接是双向的，所以在关闭连接的时候，两个方向各自都需要关闭。先发FIN包的一方执行的是主动关闭；后发FIN包的一方执行的是被动关闭。主动关闭的一方会进入TIME_WAIT状态，并且在此状态停留两倍的[MSL](http://en.wikipedia.org/wiki/Maximum_Segment_Lifetime)时长。

穿插一点MSL的知识：MSL指的是报文段的最大生存时间，如果报文段在网络活动了MSL时间，还没有被接收，那么会被丢弃。关于MSL的大小，[RFC 793](http://tools.ietf.org/search/rfc793)协议中给出的建议是两分钟，不过实际上不同的操作系统可能有不同的设置，以Linux为例，通常是半分钟，两倍的MSL就是一分钟，也就是60秒，并且这个数值是硬编码在[内核](https://github.com/torvalds/linux/blob/master/include/net/tcp.h)中的，也就是说除非你重新编译内核，否则没法修改它：

如果每秒的连接数是一千的话，那么一分钟就可能会产生六万个TIME_WAIT。

为什么主动关闭的一方不直接进入CLOSED状态，而是进入TIME_WAIT状态，并且停留两倍的MSL时长呢？这是因为TCP是建立在不可靠网络上的可靠的协议。例子：主动关闭的一方收到被动关闭的一方发出的FIN包后，回应ACK包，同时进入TIME_WAIT状态，但是因为网络原因，主动关闭的一方发送的这个ACK包很可能延迟，从而触发被动连接一方重传FIN包。极端情况下，这一去一回，就是两倍的MSL时长。如果主动关闭的一方跳过TIME_WAIT直接进入CLOSED，或者在TIME_WAIT停留的时长不足两倍的MSL，那么当被动关闭的一方早先发出的延迟包到达后，就可能出现类似下面的问题：

- 旧的TCP连接已经不存在了，系统此时只能返回RST包
- 新的TCP连接被建立起来了，延迟包可能干扰新的连接

不管是哪种情况都会让TCP不再可靠，所以TIME_WAIT状态有存在的必要性。

如何控制TIME_WAIT的数量？

##### 如何控制TIME_WAIT的数量？

从前面的描述我们可以得出这样的结论：TIME_WAIT这东西没有的话不行，不过太多可能也是个麻烦事。下面让我们看看有哪些方法可以控制TIME_WAIT数量，这里只说一些常规方法，另外一些诸如SO_LINGER之类的方法太过偏门，略过不谈。

-  **tcp_tw_recycle**：

顾名思义就是回收TIME_WAIT连接。可以说这个内核参数已经变成了大众处理TIME_WAIT的万金油，如果你在网络上搜索TIME_WAIT的解决方案，十有八九会推荐设置它，不过这里隐藏着一个不易察觉的[陷阱](http://www.litrin.net/2013/03/01/android之网络丢包事件/)：

当多个客户端通过NAT方式联网并与服务端交互时，服务端看到的是同一个IP，也就是说对服务端而言这些客户端实际上等同于一个，可惜由于这些客户端的时间戳可能存在差异，于是乎从服务端的视角看，便可能出现时间戳错乱的现象，进而直接导致时间戳小的数据包被丢弃。参考：[tcp_tw_recycle和tcp_timestamps导致connect失败问题](http://blog.sina.com.cn/s/blog_781b0c850100znjd.html)。

-  **tcp_tw_reuse**：

顾名思义就是复用TIME_WAIT连接。当创建新连接的时候，如果可能的话会考虑复用相应的TIME_WAIT连接。通常认为「tcp_tw_reuse」比「tcp_tw_recycle」安全一些，这是因为一来TIME_WAIT创建时间必须超过一秒才可能会被复用；二来只有连接的时间戳是递增的时候才会被复用。[官方文档](https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt)里是这样说的：如果从协议视角看它是安全的，那么就可以使用。这简直就是外交辞令啊！按我的看法，如果网络比较稳定，比如都是内网连接，那么就可以尝试使用。

不过需要注意的是在哪里使用，既然我们要复用连接，那么当然应该在连接的发起方使用，而不能在被连接方使用。举例来说：客户端向服务端发起HTTP请求，服务端响应后主动关闭连接，于是TIME_WAIT便留在了服务端，此类情况使用「tcp_tw_reuse」是无效的，因为服务端是被连接方，所以不存在复用连接一说。让我们延伸一点来看，比如说服务端是PHP，它查询另一个MySQL服务端，然后主动断开连接，于是TIME_WAIT就落在了PHP一侧，此类情况下使用「tcp_tw_reuse」是有效的，因为此时PHP相对于MySQL而言是客户端，它是连接的发起方，所以可以复用连接。

**说明：如果使用tcp_tw_reuse，请激活tcp_timestamps，否则无效。**

- tcp_max_tw_buckets

名思义就是控制TIME_WAIT总数。[官网文档](https://www.kernel.org/doc/Documentation/networking/ip-sysctl.txt)说这个选项只是为了阻止一些简单的DoS攻击，平常不要人为的降低它。如果缩小了它，那么系统会将多余的TIME_WAIT删除掉，日志里会显示：「TCP: time wait bucket table overflow」。

##### 优化配置

用vim打开配置文件：#vim /etc/sysctl.conf
 在这个文件中，加入下面的几行内容：

```undefined
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_fin_timeout = 30
```

输入下面的命令，让内核参数生效：#sysctl -p

简单的说明上面的参数的含义：

```shell
net.ipv4.tcp_syncookies = 1
#表示开启SYN Cookies。当出现SYN等待队列溢出时，启用cookies来处理，可防范少量SYN攻击，默认为0，表示关闭；
net.ipv4.tcp_tw_reuse = 1
#表示开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接，默认为0，表示关闭；
net.ipv4.tcp_tw_recycle = 1
#表示开启TCP连接中TIME-WAIT sockets的快速回收，默认为0，表示关闭；
net.ipv4.tcp_fin_timeout
#修改系統默认的 TIMEOUT 时间。
```

在经过这样的调整之后，除了会进一步提升服务器的负载能力之外，还能够防御小流量程度的DoS、CC和SYN攻击。

此外，如果你的连接数本身就很多，我们可以再优化一下TCP的可使用端口范围，进一步提升服务器的并发能力。依然是往上面的参数文件中，加入下面这些配置：

```shell
net.ipv4.tcp_keepalive_time = 1200
net.ipv4.ip_local_port_range = 10000 65000
net.ipv4.tcp_max_syn_backlog = 8192
net.ipv4.tcp_max_tw_buckets = 5000
#这几个参数，建议只在流量非常大的服务器上开启，会有显著的效果。一般的流量小的服务器上，没有必要去设置这几个参数。
```

### 案例详解

1、问题描述：高负载下，系统响应变慢，并出现超时或失误失败情况，TIME_WAIT积压
 2、问题影响：系统设置的自动回收时间为60s,但在压测中如果涉及的服务较多的情况下，比如这次以100TPS压力单测1个接口，涉及4-6个服务，每秒就会创建400+的连接，1分钟就是2.4万的连接，系统无法及时回收，压测两分钟后，新的请求过来，无法创建连接或无法及时创建连接，导致请求失败，严重时会出现整个服务器挂死（新来的请求无法创建连接）。
 3、问题原因：Linux环境配置存在问题（后check所有测试环境均未配置，包括生产环境）
 4、问题背景：在对A接口进行负载测试时，在一定压力下，出现了比较诡异的现象：在100TPS压力下，开始响应时间很快，都在0.3s左右，但压测不到一分钟开始出现大量事务失败，响应时间倍增，最后系统甚至出现“挂死”。
 5、问题解决：增加以下，

```csharp
[root@aaa1 ~]# vim /etc/sysctl.conf

net.ipv4.tcp_tw_reuse = 1
```

这个是重点，表示开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接，默认为0，表示关闭；
 问题定位及验证：

```ruby
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
TIME_WAIT 41735
CLOSE_WAIT 145
FIN_WAIT2 3
ESTABLISHED 413
```

不到两分钟，time_wait积压到4万，最后导致无法创建新的连接，事务全部失败。
 添加参数后，再次验证，以下是压测十分钟中的time_wait数据，一致保持在5000以下，而且由于不需要重新创建连接，直接用已存在的，减少了资源开销，120TPS比之前压测100TPS的性能要好很多。

```ruby
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
TIME_WAIT 4500
CLOSE_WAIT 7
ESTABLISHED 642
```

[TIME_WAIT状态(2MSL)的作用](https://blog.csdn.net/tennysonsky/article/details/48680197)
 [深入浅出TCP协议的2MSL TIME_WAIT状态](https://blog.csdn.net/xiaofei0859/article/details/24621487)
 https://www.cnblogs.com/wujing-hubei/p/5699773.html
 http://blog.sina.com.cn/s/blog_8e5d24890102w9yi.html
 https://www.cnblogs.com/qlee/archive/2011/07/12/2104089.html
 [性能案例-Linux下解决time_wait连接过多（Linux内核优化）](https://zhuanlan.zhihu.com/p/29334504)
 [优化Linux的内核参数来提高服务器并发处理能力](http://www.ha97.com/4396.html)
 )