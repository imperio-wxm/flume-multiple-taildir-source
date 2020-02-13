# multiple-taildir-source

> Flume taildirsource source code extension, supports dynamic subdirectory recognition and regular filtering.

- 基于flume 1.7.0源码修改

## 此项目是对 flume TaildirSource源码的一个扩展

由于flume自带的TaildirSource仅仅支持对`根目录`的监听，即无法监听子目录

- 支持对当前目录及其子目录的文件进行监听

- 支持对所有文件的正则过滤，即只有满足正则的文件路径才会被监听

## 不足：

- 要对目录及其子目录做监听，必定要遍历全目录，当目录层级太多，文件数太多的时候因遍历太过耗时而导致问题

## 新增配置

```shell
// 强制刷新缓存时间间隔，默认：2 * 60 * 1000 ms
forceRefreshIntervalMs

// 对文件过滤的正则表达式，满足正则的文件路径才会监听，默认 null 不进行过滤全部监听 
filePathRegularExpression
```

## 配置样例

```yaml
agent.sources=s1
agent.channels=c1
agent.sinks=r1

agent.sources.s1.channels=c1
agent.sinks.r1.channel=c1

agent.sources.s1.type=com.wxmimperio.flume.source.MultipleTaildirSource
# 文件位置记录
agent.sources.s1.positionFile=/home/wxmimperio/bigdata/flume/position/taildir_position.json
# 文件过滤正则
agent.sources.s1.filePathRegularExpression=/home/wxmimperio/bigdata/flume/files/.*/test.*.txt
# 文件强制刷新间隔
agent.sources.s1.forceRefreshIntervalMs=130000
agent.sources.s1.filegroups=f1
# 文件根目录，会监听这个目录及其子目录下文件
agent.sources.s1.filegroups.f1=/home/wxmimperio/bigdata/flume/files/.*
agent.sources.s1.fileHeader=true

agent.channels.c1.type=memory
agent.sinks.r1.type=logger
```

## 启动测试

- maven 打包，将`multiple-taildir-source-1.0.jar`放在 `flume/lib` 下

```shell
../bin/flume-ng agent -n agent -f ../conf/flume-conf.properties -c ../conf -Dflume.root.logger=INFO,console
```


