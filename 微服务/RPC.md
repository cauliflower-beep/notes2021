# 一、概念

在微服务结构中，服务之间的通信往往采用RPC接口进行通信。

**RPC(Remote Procedure Call Protocol)**，是远程过程调用协议的缩写，通俗的说就是调用远处的一个函数。通俗来讲，就是调用远处的一个函数。与之相对应的就是调用一个本地函数。

需要跟IPC通信做一个区别：

IPC：进程间通信

RPC：远程间通信

# 二、为什么需要RPC？

使用微服务的一个好处是技术异构性，也就是说，不限定服务的提供方法使用什么技术选型，能够实现公司跨团队的技术解耦（算法部门和业务部门可以使用不同的语言），如下图：

![1564373553013](img\1564373553013.png)

这样的话，**如果没有统一的服务框架，RPC框架，**各个团队的服务提供方就需要各自实现一套序列化、反序列化、网络框架、连接池、收发线程、超时处理、状态机等“业务之外”的重复技术劳动，造成整体的低效。所以，**统一RPC框架**把上述“业务之外”的技术劳动统一处理，是服务化首要解决的问题。

# 三、应用

在互联网时代，RPC已经和IPC(进程间通信)一样成为一个不可或缺的基础构件。因此Go语言的标准库也提供了一个简单的RPC实现，我们将以此为入口学习RPC的常见用法。

**如无意外说明，本章节提供的例子，服务端代码均部署在VM中，客户端则在主机windows中。**

## 3.1"hello world！"

Go语言的RPC包的路径为net/rpc，也就是放在了net包目录下面。因此我们可以猜测该RPC包是建立在net包基础之上的。接着我们尝试基于rpc实现一个类似的例子。我们先构造一个robot类型，模拟远端的一个机器人AI。它的Hello方法用于实现打印功能：

`````go
type Robot struct{}
//打印
func(r *Robot)Hello(request string,reply *string)error{
*reply = "hello:" + request
return	nil
}

func(r *Robot)Sing(request string,reply *string)error{
*reply = "为您播放：" + request
return	nil
}
`````

> Hello、Sing方法方法必须满足Go语言的RPC规则：方法只能有两个可序列化的参数，其中第二个参数是指针类型，并且返回一个error类型，同时必须是公开的方法。
>
> golang 中的类型比如：channel（通道）、complex（复数类型）、func（函数）均不能进行 序列化

满足以上规则的方法，就可以将HelloService类型的对象注册成为一个RPC服务：

```go
func main(){
	//rpc注册服务  
    //注册rpc服务，维护一个hash表，key值是服务名称，value值是服务的地址
	rpc.RegisterName("Robot",new(Robot))

	//设置服务监听
	listener,err := net.Listen("tcp","192.168.137.25:1234")
	if err != nil {
		panic(err)
	}

	//接受传输的数据
	conn,err := listener.Accept()
	if err != nil {
		panic(err)
	}

	//rpc调用,并返回执行后的数据
    //1.read，获取服务名称和方法名，获取请求数据
    //2.调用对应服务里面的方法，获取传出数据
    //3.write，把数据返回给client
	rpc.ServeConn(conn)

}
```

> 其中rpc.Register函数调用会将对象类型中所有满足RPC规则的对象方法注册为RPC函数，所有注册的方法会放在“HelloService”服务空间之下。然后我们建立一个唯一的TCP链接，并且通过rpc.ServeConn函数在该TCP链接上为对方提供RPC服务。

将上述服务逻辑和服务注册部署在linux（远程）上。如果分成了两个文件，记得go run *

下面是本地客户端请求HelloService服务的代码：

```go
func main(){
    //用rpc连接
	client,err := rpc.Dial("tcp","192.168.137.25:1234")
	if err != nil {
		panic(err)
	}

	var reply string
    //调用服务中的函数
	err = client.Call("Robot.Hello","world",&reply)
	if err != nil {
		panic(err)
	}

	fmt.Println(reply)
}
```

> 首选是通过rpc.Dial拨号RPC服务，然后通过client.Call调用具体的RPC方法。在调用client.Call时，第一个参数是用点号链接的RPC服务名字和方法名字，第二和第三个参数分别我们定义RPC方法的两个参数。

接着启动测试。

ps.如果出现以下报错：

panic: dial tcp 192.168.137.25:1234: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or establish
ed connection failed because connected host has failed to respond.

可以参考我这边文章来解决：

[主机不能访问虚拟机上的rpc服务 - 简书 (jianshu.com)](https://www.jianshu.com/p/a48dd35e8a9d)

## 3.2跨语言的RPC

标准库的RPC默认采用Go语言特有的gob编码。因此，其它语言调用Go语言实现的RPC服务将比较困难。但是跨语言是互联网时代RPC的一个首要条件，这里我们再来实现一个跨语言的RPC。得益于RPC的框架设计，Go语言的RPC其实也是很容易实现跨语言支持的。

这里我们将尝试通过官方自带的net/rpc/jsonrpc扩展实现一个跨语言RPC。

首先是基于json编码重新实现RPC服务：

```go
func main(){
    //注册rpc服务
	rpc.RegisterName("Robot",new(Hello))
	//设置监听
	listener,err := net.Listen("tcp","192.168.137.25:1234")
	if err != nil {
		panic(err)
	}

	for{
        //接收连接
		conn,err := listener.Accept()
		if err != nil {
			panic(err)
		}
		//给当前连接提供针对json格式的rpc服务
		go rpc.ServeCodec(jsonrpc.NewServerCodec(conn))
	}
}
```

> 代码中最大的变化是用rpc.ServeCodec函数替代了rpc.ServeConn函数，传入的参数是针对服务端的json编解码器。

客户端代码如下：

```go
func main(){
    //简历tcp连接
	conn,err := net.Dial("tcp","192.168.137.25:1234")
	if err !=nil{
		panic(err)
	}
	//建立基于json编解码的rpc服务
	client := rpc.NewClientWithCodec(jsonrpc.NewClientCodec(conn))

	var reply string
	//调用rpc服务方法
	err = client.Call("Robot.Hello","goku",&reply)
	if err != nil {
		panic(err)
	}

	fmt.Println("收到的数据为:",reply)
}
```

> 先手工调用net.Dial函数建立TCP链接，然后基于该链接建立针对客户端的json编解码器。

运行测试。

### 3.2.1请求数据

可以使用  nc 工具，查看客户端发送的数据：

1. 启动Linux终端，确保刚刚启动的服务端代码已经关闭，若不关闭，则端口被占用，nc启动不起来；
2. 执行 nc -l 1234 监听1234端口；
3. 在主机上运行客户端程序

>nc常用方法有两种：
>
>1.连接到指定ip和端口：nc hostname port	
>
>2.监听端口，等待连接：nc  -l   port

可以发现nc输出了以下的信息：

```json
{"method":"Robot.Hello","params":["goku"],"id":0}
```

这是一个标准的json编码的数据格式：

- method部分对应要调用的rpc服务和方法组合成的名字；
- params部分的第一个元素为参数；
- id是由调用端（客户端）维护的一个唯一的调用编号。即使有多个rpc对象，每个对象有多个不同方法，调用起来的id也都是相同的。

请求的json数据对象在内部对应两个结构体：客户端是clientRequest，服务端是serverRequest。clientRequest和serverRequest结构体的内容基本是一致的：

````go
type clientRequest struct {
	Method string         `json:"method"`
	Params []interface{}  `json:"params"`
	Id     uint64         `json:"id"`
}
type serverRequest struct {
	Method string           `json:"method"`
	Params *json.RawMessage `json:"params"`
	Id     *json.RawMessage `json:"id"`
}
````

### 3.2.2响应数据

了解了客户端需要发送哪些数据之后，再来看看服务器处理之后会返回哪些数据，同样使用nc命令，操作如下：

1. 启动服务端代码，因为要得到他的反馈；
2. 打开linux终端，运行如下指令：

```shell
echo -e '{"method":"Robot.Hello","params":["goku"],"id":1}'| nc localhost 1234
```

返回的数据如下：

```json
{"id":1,"result":"hello:goku","error":null}
```

也是一个json编码的数据格式：

1. id对应输入的id参数，对于顺序调用来说，id不是必须的，但是go语言的RPC框架支持异步调用，当响应结果的顺序和调用的顺序不一样时，可以通过id来识别对应的调用；
2. result为返回的结果；
3. error部分在出问题时表示错误信息。

ps.查看服务端响应的过程中，如果报错：

```shell
Ncat: Connection refused.
```

可以参考我这篇文章来解决：

[解决Ncat: Connection refused.的问题 - 简书 (jianshu.com)](https://www.jianshu.com/p/781341c47676)

响应的json数据也是对应内部的两个结构体：客户端是clientResponse，服务端是serverResponse。两个结构体的内容同样也是类似的：

```go
type clientResponse struct {
	Id     uint64           `json:"id"`
	Result *json.RawMessage `json:"result"`
	Error  interface{}      `json:"error"`
}
type serverResponse struct {
	Id     *json.RawMessage `json:"id"`
	Result interface{}      `json:"result"`
	Error  interface{}      `json:"error"`
}
```

因此，无论采用何种语言，只要遵循同样的json结构，并且流程相同，就可以和Go语言编写的RPC服务进行通信。这样我们就借用json简单实现了跨语言的RPC。

## 3.3RPC协议封装

在上述例子中，服务名是固定的，不够灵活且容易出错，所以可以对RPC的服务端和客户端再进行一次封装，屏蔽掉服务名。

### 3.3.1服务端封装

```go
//抽离服务名称
var serverName = "Robot"

//定义一个接口，包含所有服务内容
type RPCDesign interface {
	Hello(string,*string)error
    Sing(string,*string)error
}

type Robot struct{}

//打印
func(r *Robot)Hello(request string,reply *string)error{
	*reply = "hello:" + request
	return	nil
}

func(r *Robot)Sing(request string,reply *string)error{
	*reply = "为您播放：" + request
	return	nil
}


//实现工厂函数
func RegisterRPCServer(srv RPCDesign)error{
	return rpc.RegisterName(serverName,srv)
}

```

封装之后的服务端代码如下：

````go
func main() {
	//rpc表  注册rpc服务
	if err := RegisterRPCServer(new(Robot)); err != nil {
		fmt.Println("注册rpc服务失败")
		return
	}

	//设置监听
	listener, err := net.Listen("tcp", "192.168.137.25:1234")
	if err != nil {
		fmt.Println("设置监听错误")
		return
	}
	defer listener.Close()

	fmt.Println("listening....")
	for {
		//接收链接
		conn, err := listener.Accept()
		if err != nil {
			fmt.Println("获取连接失败")
			return
		}
		defer conn.Close()

		fmt.Println(conn.RemoteAddr().String() + "连接成功")


		//把rpc服务和套接字绑定
		rpc.ServeCodec(jsonrpc.NewServerCodec(conn))
	}

}
````

### 3.3.2客户端封装

```go
type RPCClient struct {
	rpcClient *rpc.Client
}

func NewRpcClient(addr string)(RPCClient){
	conn,err := net.Dial("tcp",addr)
	if err != nil {
		fmt.Println("connect failed...")
		return RPCClient{}
	}
	defer conn.Close()

	//套接字和rpc服务绑定
	client := rpc.NewClientWithCodec(jsonrpc.NewClientCodec(conn))
	return RPCClient{rpcClient:client}
}

func (this*RPCClient)CallFunc(req string,resp*string)error{
	return this.rpcClient.Call(serverName+".Hello",req,resp)
}
```

封装之后的客户端实现：

```go
func main()  {
	//初始化对象  与服务名有关的内容完全封装起来了
	client := NewRpcClient("192.168.137.25:1234")

	//调用成员函数
	var temp string
	client.CallFunc("goku",&temp)

	fmt.Println(temp)
}
```



