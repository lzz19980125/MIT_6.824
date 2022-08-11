# MIT 6.824 Lab 1: Re-implement MapReduce model described in OSDI' 04

本项目为MIT 6.824 (distribute system) 课程中的Lab 1, 旨在根据发表在OSDI '04上MapReduce的原始论文重构一个简易的MapReduce模型, 并用于`Word Count`任务(对文本进行词频统计). 该模型具有一定的容错(Fault Tolerance)机制, 当多个Worker突然崩溃(Crash)或无响应时, Master仍能够进行正确的调度, 使得模型最终产出正确的结果. 整个项目基于Golang进行实现. 

## OSDI' 04中MapReduce模型运行的主要流程

![](https://github.com/lzz19980125/MIT_6.824/blob/main/picture%20bed/mr1.PNG)

## OSDI' 04中MapReduce模型中的Fault Tolerance机制

![](https://github.com/lzz19980125/MIT_6.824/blob/main/picture%20bed/mr2.PNG)

## MIT 6.824 Lab 1中Rules以及Hints中需要注意的地方

![](https://github.com/lzz19980125/MIT_6.824/blob/main/picture%20bed/mr3.PNG)

## MIT 6.824 Lab1 中各个test的主要逻辑

![](https://github.com/lzz19980125/MIT_6.824/blob/main/picture%20bed/mr4.PNG)

## 项目的组织架构

* `6.824`:项目的主文件夹
  * `src`
    * `main`
      * `mrcoordinator.go`: `coordinator `的启动函数
      * `mrworker.go`: `worker`的启动函数
      * `mrsequential.go`: `MapReduce`的串行实现
      * `pg-*.txt` : `Word Count Task`需要处理的文本
      * `test-mr.sh ` : `MIT 6.824`官方提供的测试脚本
    * `mr`
      * `coordinator.go` : `Master`的主要实现
      * `worker.go `: `Worker`的主要实现
      * `rpc.go` : `rpc`通信的实现
    * `mrapps` 
      * `wc.go ` :`Word Count test`
      * `indexer.go` : `Indexer test`
      * `mtiming.go` : `Map parallelism test`
      * `rtiming.go`: `Reduce parallelism test`
      * `early_exit.go`: `early exit test`
      * `jobcount.go`: `job count test`
      * `crash.go` :`crash test`
* `lecture notes`: 个人有关`MIT 6.824`课程的笔记
* `related paper`: `MapReduce`相关论文
* `picture bed` : 图床

## 项目流程图



## MIT 6.824 Lab1中mrsequential的运行步骤以及GoLand的Debug设置

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

在goland中的debug设置如下：

![](https://github.com/lzz19980125/MIT_6.824/blob/main/picture%20bed/goland1.png)

## Lab_1 MapReduce学习者需要完成的地方

需要改动的所有代码都在 `mr/coordinator.go`, `mr/worker.go`, and `mr/rpc.go`中，项目具体的启动过程如下：

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

![](https://github.com/lzz19980125/MIT_6.824/blob/main/picture%20bed/goland2.png)

![](https://github.com/lzz19980125/MIT_6.824/blob/main/picture%20bed/goland3.png)

