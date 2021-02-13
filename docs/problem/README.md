# 常见问题

## 无法启动
> 常见原因列举以下，如不在下列清单中，可在相关项目下 Issues 中留言

### 端口被占用
因为存在其他项目或应用进程占用了当前项目的 **端口号** (默认:8089)

```bash
Description:

Web server failed to start. Port 8089 was already in use.

Action:

Identify and stop the process that's listening on port 8089 or configure this application to listen on another port.
```

出现的原因可能是
1. 项目已经在启动，上次退出IDEA未终止进程，导致重复启动
2. docker容器启动过该项目，容器未终止
3. 其他应用进程使用了相关端口号，导致端口被占用

对于这种问题，最简单粗暴的方法就是通过命令行杀掉相关进程

> windows环境下处理
```shell
# 8089为默认端口号,请根据实际情况自行修改
netstat -ano|findstr "8089"
# 若存在端口被使用则会列出清单，其中包含PID进程号
taskkill /f /pid 进程号
```
> linux环境下处理
```bash
lsof -i:8089
sudo kill pid号
```

其他方法可对症下药处理.
如docker-compose(容器编排)的终止
```shell
# 在对应的docker-compose.yml目录下执行
docker-compose stop 
# 即可
```

## 如何反馈项目 Bug
可在 [Issues](https://gitee.com/garrettxia/sleep-monitoring-platform/issues) 中留言，或者加QQ群903130550进行反馈