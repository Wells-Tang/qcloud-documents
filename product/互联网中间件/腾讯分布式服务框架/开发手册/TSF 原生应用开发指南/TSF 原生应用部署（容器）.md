以 demo 为例演示如何部署一个原生应用。

<span id="zb"></span>
## 准备工作
1. 下载 TSF 提供的 demo（参考《TSF 原生应用 demo 介绍》）。
2. 解压 Demo 压缩包，按 README 提示执行命令 `make docker-build`。
3. 在 TSF 控制台上已创建容器集群并添加节点，详情参考 [集群](https://cloud.tencent.com/document/product/649/13684)。

## 创建并部署原生应用

### 1. 创建应用

1.1 登录 [TSF 控制台](https://console.cloud.tencent.com/tsf)。
1.2 在左侧导航，单击【[应用管理](https://console.cloud.tencent.com/tsf/app)】，进入应用列表页。
1.3 在应用列表页，单击【新建应用】，并填写以下信息：
   - 应用名：填写应用名称
   - 部署方式：选择**容器部署**
   - 应用类型：选择 **原生应用**

1.4 单击【提交】，完成应用创建。

<span id="步骤2"></span>

### 2. 将镜像推送到仓库

2.1  在左侧导航，单击【[镜像仓库](https://console.cloud.tencent.com/tsf/image)】，进入镜像列表页。首次使用时，您需要设置镜像仓库密码（该密码与腾讯云官网账号密码独立）。
2.2 在镜像列表页，单击【[应用管理](https://console.cloud.tencent.com/tsf/app)】>【ID/应用名】>【镜像】，单击【使用指引】，根据指引中的命令将 demo 应用的镜像（参考 [准备工作](#zb) 中的第2步）推送到镜像仓库中（详请参见 [镜像仓库使用指引](https://cloud.tencent.com/document/product/649/16695)）。

### 3. 创建部署组
在 TSF 平台上，使用部署组来部署应用，用户需要先创建部署组，再执行部署应用操作。
3.1 在应用详情页内，单击【[部署组](https://console.cloud.tencent.com/tsf/group)】，进入部署组列表页。
3.2 在部署组列表页，单击【新建部署组】，并填写以下信息：
   - 部署组名：填写部署组名称
   - 集群：选择应用将部署的集群
   - 命名空间：选择命名空间属性
   - 日志配置项：无（实际需按情况选择）
   - 日志投递：无（实际需按情况选择）
3.3 单击【保存&下一步】，完成部署组创建。


   - 实例资源限制：0.5核，512MiB
   - 实例数：1

3.3 设置访问设置、更新方式、日志配置项。
   - 网络访问方式
   - 端口协议：协议选择 TCP，容器端口和服务端口填写相同的数值（consul-consumer：8001，consul-provider：8002，eureka-consumer：8003，eureka-provider：8004）



### 4. 部署应用
4.1 在刚创建完部署组的《部署应用》页面，或者在部署组列表页，单击部署组右侧的【部署应用】。
4.2 部署相关信息，使用 [步骤2](#步骤2) 中仓库中的镜像（其他字段含义参考 [容器应用部署组-部署应用](https://cloud.tencent.com/document/product/649/15525#.E9.83.A8.E7.BD.B2.E5.BA.94.E7.94.A8)），例如：
   - 实例资源限制：0.5核，512MiB
   - 实例数：1
   - 对于 consumer 可以选择 NodePort，方便测试
   - 端口协议：协议选择 TCP，容器端口和服务端口填写相同的数值（consul-consumer：8001，consul-provider：8002，eureka-consumer：8003，eureka-provider：8004）
4.3  单击【提交】，部署组状态变为运行中，则表示应用部署成功。

## 查看服务是否注册成功

1. 在左侧导航栏，单击【[服务治理](https://console.cloud.tencent.com/tsf/service)】进入服务列表页，选择正确的地域和命名口行家，查看服务是否注册成功。如果注册成功，服务显示在线状态。
2. 在服务列表页，单击服务 ID，进入服务详情页，查看具体信息。

## 验证服务调用

确保按之前的流程部署一组 consumer 和 provider（如 consul-consumer 和 consul-provider）

### 请求 consumer 来调用 provider

有3种方式可以从公网请求 consumer：

- **云主机 IP + NodePort**： 如果部署组在部署时，选择了 NodePort 访问方式，可以通过**云主机 IP + NodePort**来访问`consumer`服务的`/ping-provider`接口。其中`云主机 IP`为集群中任一云主机的 IP，`NodePort`可以在部署组的基本信息页面被查看。

```
curl <云主机 IP>:<NodePort>/ping-provider
```

- **负载均衡 IP + 服务端口**：如果部署组在部署时，选择了公网访问方式，可以通过**负载均衡 IP + 服务端口**来访问`consumer`服务的`/ping-provider`接口。

- **API 网关**：用户可以通过在 API 网关配置微服务 API 来调用 user 服务的接口。关于如何配置微服务 API 网关，可参考文档 [API 网关作为请求入口](https://cloud.tencent.com/document/product/649/17644)。


也可以在容器内通过服务名和服务端口请求 consumer。先通过 kubectl 等方式进入 容器，然后调用：

```
wget -O- consul-consumer:8001/ping-provider
wget -O- eureka-consumer:8003/ping-provider
```

### 在控制台验证服务之间是否调用

可以验证服务是否成功被注册，同时验证服务之间是否成功地进行了调用。在**服务治理**界面：选择集群和命名空间后，如果服务列表中的服务状态为**在线**或**单点在线**，表示服务被代理注册成功。如果服务提供者的请求量大于0，表示 provider 被 consumer 请求成功。
