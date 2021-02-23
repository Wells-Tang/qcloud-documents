TSF 原生应用是一种不需要应用做修改、配置就可以实现服务注册、发现，并接入 TSF、使用 TSF 服务治理的应用。

## 优势

* 无需修改应用代码
* 无需引入额外 SDK（除调用链外）
* 无需额外配置

## 实现原理

应用到注册中心的所有请求如注册、发现，会被代理到我们自己的注册中心从而完成服务的注册和发现。当前的服务治理功能是基于 [TSF Mesh](https://cloud.tencent.com/document/product/649/17928) 来实现的，服务调用会经过 TSF Mesh 的 sidecar，在 sidecar 中实现负载均衡、服务路由等治理功能。

>!服务中的熔断、限流功能可能会 TSF 自身的功能冲突，请确保只开启了一种，否则可能会产品非预期的结果。如果确定要启用 TSF 的治理功能来代替服务自身的，可以参考 《关闭 Spring Cloud 熔断限流指引》。

## 支持应用类型和组件

目前仅支持 Spring Cloud，且支持 Eureka/Consul 两种注册中心。未来可能支持更多语言和框架，以及更多注册中心。具体如下：

<table>
    <thead>
        <tr>
            <th>语言和框架</th>
            <th>功能</th>
            <th>支持组件</th>
            <th>说明</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=5>Java & Spring Cloud</td>
            <td>注册中心</td>
            <td>
              <li>Consul</li>
              <li>Eureka</li>
            </td>
            <td></td>
        </tr>
        <tr>
            <td>负载均衡</td>
            <td>
              <li>Ribbon</li>
              <li>spring-cloud-loadbalancer</li>
            </td>
            <td>TSF 会使用自己的负载均衡器。只要是使用注册中心做服务发现，或者通过服务名调用均可支持</td>
        </tr>
        <tr>
            <td>使用 TSF 调用链</td>
            <td>Zipkin, Sleuth</td>
            <td>支持 <a href="https://github.com/openzipkin/b3-propagation">B3 propagation</a> 的组件即可</td>
        </tr>
        <tr>
            <td>使用自己维护的调用链后端</td>
            <td>理论上所有开源组件都支持，已验证：Skywalking、Zipkin、Jaeger、Pinpoint</td>
            <td></td>
        </tr>
        <tr>
            <td>微服务网关</td>
            <td>Spring Cloud Gateway, Zuul</td>
            <td>只要正常部署原生应用即可</td>
        </tr>
    </tbody>
</table>
