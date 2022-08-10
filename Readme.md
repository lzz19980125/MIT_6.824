# MapReduce的架构与原理

## lab_1文件的组织架构

- `main`：主文件夹

  - `mrcoordinator.go`：coordinator 的启动文件（coordinator是英文协调员的意思）

  - `mrworker.go`：worker的启动文件

  - `mrsequential.go`：实验串行模拟版本（a simple sequential mapreduce implementation）

    > 注：以上文件需要在不同的终端启动

  - `test-mr.sh`：测试实验是否通过的`shell`脚本

- `mr`：worker 和 coordinator 具体实现的文件夹

  - `coordinator.go`
  - `worker.go`
  - `rpc.go`：worker 和 coordinator 通信的实现

- `mrapps`：每对Map-Reduce操作的具体实现文件夹

  - `wc.go` : Map-Reduce的具体应用程序: word count
  - `indexer.go` ：Map-Reduce的具体应用程序: text indexer

## lab_1 Mapreduce的串行(sequential)版本运行与调试步骤

整个串行版本的运行命令如下：

```shell
$ cd ~/6.824
$ cd src/main
$ go build -race -buildmode=plugin ../mrapps/wc.go #编译word count 的实现文件 ../mrapps/wc.go为动态链接库文件wc.so
$ rm mr-out*
$ go run -race mrsequential.go wc.so pg*.txt #运行mapreduce串行文件
$ more mr-out-0
A 509
ABOUT 2
ACT 8
...
```

在goland中的debug configure如下：

![image-20220713204837422](C:\Users\lizi2\AppData\Roaming\Typora\typora-user-images\image-20220713204837422.png)

## Lab_1 MapReduce你需要完成的地方

你需要改动的所有代码都在 `mr/coordinator.go`, `mr/worker.go`, and `mr/rpc.go`中，项目具体的启动过程如下：

```bash
$ go build -race -buildmode=plugin ../mrapps/wc.go    #编译mapreduce的应用程序word count作为动态链接库

#In the main directory, run the coordinator:
$ rm mr-out*
$ go run -race mrcoordinator.go pg-*.txt

#在另一个终端中, run some workers:
$ go run -race mrworker.go wc.so

#在项目运行完毕后，查看main directory中的mr-out-*
```

### 有关`mrcoordinator.go`以及`mrworker.go`的Debug窗口配置如下:

![image-20220718223125295](C:\Users\lizi2\AppData\Roaming\Typora\typora-user-images\image-20220718223125295.png)

![image-20220718223145657](C:\Users\lizi2\AppData\Roaming\Typora\typora-user-images\image-20220718223145657.png)

```go
//整个项目的架构

```

