# 安装 Skywalking

[Skywalking官网](https://skywalking.apache.org/downloads/ 'Skywalking')

## 部署 Skywalking

1. 下载 ```Skywalking APM``` 后上传至服务器

2. 解压

   ```bash
   # tar -xzvf apache-skywalking-apm-10.2.0.tar.gz
   
   # cd apache-skywalking-apm-bin
   ```

3. 查看bin目录里的启动脚本

   ```bash
   # ls bin/
   oapService.bat      oapServiceInit.sh     oapServiceNoInit.sh  startup.bat  webappService.bat
   oapServiceInit.bat  oapServiceNoInit.bat  oapService.sh        startup.sh   webappService.sh
   ```
   
   - oapService: 核心服务，用来接收各个服务的监控数据并解析、处理、存储
   - webappService: UI服务，用来展示 Skywalking的监控数据、图表分析等。

4. 修改配置文件
   
   1. 修改OAP的存储模式(**只能是配置文件里已有的存储实例**):
   
   ```bash
   # vim config/application.yml
   storage:
     selector: ${SW_STORAGE:xxx}
   ```
   
   2. 修改UI端口(默认是8080，如果默认端口被使用，就改成其他端口号):
   
   ```bash
   # vim webapp/application.yml
   serverPort: ${SW_SERVER_PORT:-8085}
   ```

5. 启动服务

   ```bash
   # ./bin/startup.sh
   ```
   
6. 查看服务是否启动

   ```bash
   # ps -ef | grep skywalking
   root       30378       1 35 09:37 pts/0    00:07:30 /usr/local/jdk/bin/java -Xms256M -Xmx4096M -Doap.logDir=/home/saftop/apache-skywalking-apm-bin/logs
   
   # netstat -lnp | grep 11800
   tcp6       0      0 :::11800                :::*                    LISTEN      30378/java 
      
   # netstat -lnp | grep 12800
   tcp6       0      0 :::12800                :::*                    LISTEN      30378/java 
   ```
   
   - 11800端口: API端口
   - 12800端口: REST端口

7. 从浏览器访问监控界面: ```http://IP:8085```

## 部署 SkyWalking Agent

**SkyWalking Agent 不需要和 apache-skywalking-apm-bin（OAP Server + UI）安装在同一台机器上，它们是完全独立的组件，可以分开部署。**

- apache-skywalking-apm-bin：包含 OAP Server（后端服务）和 UI（前端界面），负责接收、存储、分析 Agent 发送的数据，并通过 UI 展示。
- SkyWalking Agent：是一个轻量级的探针（JAR 包），需要附加到被监控的 Java 应用进程中，负责收集调用链路、性能指标等数据，并发送给 OAP Server。

两者的关系是：Agent 向 OAP Server 发送数据，OAP Server 处理数据，UI 展示数据。这两者可以部署在不同机器、不同容器甚至不同网络环境中（只要网络互通）。

1. 下载 ```Java Agent```

2. 解压

   ```bash
   # tar -xzvf apache-skywalking-java-agent-9.5.0.tgz
   
   # cd skywalking-agent
   ```

3. 修改配置文件

   ```bash
   # vim config/agent.config
   agent.service_name=组名::${SW_SERVICE_NAME}
   collector.backend_service=${SW_AGENT_COLLECTOR_BACKEND_SERVICES:OAP服务器IP:11800}
   ```

4. 启动 SpringBoot 的 jar 包
   
   ```bash
   # nohup java -javaagent:/usr/local/skywalking-agent/skywalking-agent.jar -DSW_SERVICE_NAME=demo -jar -XX:+HeapDumpOnOutOfMemoryError -Xmx256m -Xms256m ./demo.jar > /dev/null 2>&1 &
   ```
   
   调用jar包里的接口，即可在 SkyWalking UI 中看到监控的数据。
