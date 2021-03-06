# 后端服务器FAQ {#concept_dt4_dh5_vdb .concept}

包含以下常见问题：

-   [1. 负载均衡运行中是否可调整ECS数量？](#section_bt3_knx_wdb)
-   [2. 后端ECS实例的操作系统是否可以不同？](#section_ct3_knx_wdb)
-   [3. 可以使用不同地域的ECS实例作为后端服务器么？](#section_dt3_knx_wdb)
-   [4. 为什么有100开头的IP在频繁访问ECS实例？](#section_gt3_knx_wdb)
-   [5. ECS实例上没有配置压缩，为什么从负载均衡返回的响应却被压缩了?](#section_ot3_knx_wdb)
-   [6. ECS实例使用了HTTP1.0是否支持chunked transfer传输编码？](#section_pt3_knx_wdb)
-   [7. 为什么负载均衡后端ECS实例频繁收到UA为KeepAliveClient的请求？](#section_c43_sqx_wdb)

## 1. 负载均衡运行中是否可调整ECS数量？ {#section_bt3_knx_wdb .section}

可以。

您可以在任意时刻增加或减少负载均衡的后端ECS实例数量并且支持不同ECS实例之间的切换。但是为了保证您对外服务的稳定，确保在执行上述操作时，开启了负载均衡的健康检查功能并保证负载均衡后端至少有一台正常运行的ECS实例。

## 2. 后端ECS实例的操作系统是否可以不同？ {#section_ct3_knx_wdb .section}

可以。

负载均衡本身不会限制后端ECS实例使用哪种操作系统，只要确保后端ECS实例中的应用服务部署相同且数据一致即可。但建议使用相同的操作系统，以便您日后的管理维护。

## 3. 可以使用不同地域的ECS实例作为后端服务器么？ {#section_dt3_knx_wdb .section}

不可以。

负载均衡不支持跨地域部署。负载均衡实例的地域和后端ECS实例的地域必须相同。

## 4. 为什么有100开头的IP在频繁访问ECS实例？ {#section_gt3_knx_wdb .section}

负载均衡系统除了会通过系统服务器的内网IP将来自外部的访问请求转到后端ECS实例之外，还会对ECS实例进行健康检查和可用性监控，这些访问的来源都是由负载均衡系统发起的。

负载均衡系统的地址段为100.64.0.0/10（100.64.0.0/10 是阿里云保留地址，其他用户无法分配到该网段内，不会存在安全风险），所以会有很多100开头的IP地址访问ECS实例。

为了确保您对外服务的可用性，确保对上述地址的访问配置了放行规则。

## 5. ECS实例上没有配置压缩，为什么从负载均衡返回的响应却被压缩了? {#section_ot3_knx_wdb .section}

可能是客户端浏览器端支持压缩。您可以在控制台上创建监听时关闭Gzip压缩功能，或改用TCP监听。

## 6. ECS实例使用了HTTP1.0是否支持chunked transfer传输编码？ {#section_pt3_knx_wdb .section}

支持。

## 7. 为什么负载均衡后端ECS实例频繁收到UA为KeepAliveClient的请求？ {#section_c43_sqx_wdb .section}

问题现象：

负载均衡后端的ECS实例即使在没有用户访问时也会频繁收到GET请求，来源的IP是阿里云的内网IP，User-Agent显示为KeepAliveClient。

问题原因：

监听协议选择的是TCP，而健康检查选择了HTTP协议。TCP监听下使用HTTP协议进行健康检查时，默认使用GET方法请求。

解决方案：

建议您将监听协议和健康检查协议统一设置为同一个协议。

