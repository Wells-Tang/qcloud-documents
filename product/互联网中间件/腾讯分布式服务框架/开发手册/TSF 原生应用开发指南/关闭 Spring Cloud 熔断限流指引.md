>!某些配置可能会关闭其他功能，请注意使用，做好验证

## 限流

| 组件 | 关闭方法 |
| ------ | ------ | ------ |
| Resilience | 不支持通过 property 配置，需自行修改代码来关闭 |
| Sentinel | 不支持通过 property 配置，需自行修改代码来关闭 |

## 熔断

### Hystrix/Spring Cloud Hystrix

可以通过 property [hystrix.command.default.circuitBreaker.enabled](https://github.com/Netflix/Hystrix/wiki/Configuration#circuitBreaker.enabled) 关闭，修改 application.yml：
```
hystrix:
 command:
   default:
     circuitBreaker:
       enabled: false
```

如果用了 Feign，此方式也可以关闭其中的熔断功能。

### Resilience

不支持配置，只能通过 [transitionToDisabledState()](https://github.com/resilience4j/resilience4j/issues/828#issuecomment-580162591) 或自行修改代码来关闭。

`transitionToDisabledState` 示例如下：

```
public class ProviderServiceResilience {
    private final static String cbName = "default";

    private final CircuitBreakerRegistry cbRegistry;

    public ProviderServiceResilience() {
        cbRegistry = CircuitBreakerRegistry.ofDefaults();
    }

    public String run() {
        try {
            cbRegistry.circuitBreaker(cbName).executeCallable(...);
        } catch (Exception e) {
        }
        return "resilience fallback\n";
    }

    public void disable() {
        cbRegistry.circuitBreaker(cbName).transitionToDisabledState();
    }
}
```

### Spring Cloud Circuit Breaker - Resilience

> 注意，此方法会关闭其他 Resilience 功能，如 TimeLimiter，请谨慎使用

可以通过 property `spring.cloud.circuitbreaker.resilience4j.enabled` 关闭，修改 application.yml：
```
spring:
  cloud:
    circuitbreaker:
      resilience4j:
        enabled: false
```

### Sentinel

需要自行修改代码来关闭

### Spring Cloud Circuit Breaker - Sentinel

可以通过 property `spring.cloud.circuitbreaker.sentinel.enabled` 关闭，修改 application.yml：
```
spring:
  cloud:
    circuitbreaker:
      sentinel:
        enabled: false
```
