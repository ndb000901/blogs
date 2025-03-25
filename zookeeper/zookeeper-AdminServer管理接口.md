# zookeeper-AdminServer 管理接口

## 1.配置

`zoo.cfg`
```properties
admin.enableServer=true
admin.serverPort=8080
admin.serverAddress=0.0.0.0
```

- admin.enableServer=true：启用 HTTP 管理接口（默认就是 true）
- admin.serverPort=8080：监听的端口
- admin.serverAddress=0.0.0.0：监听的地址（可以换成内网地址）

## 2.访问

```bash

curl http://192.168.43.242:8080/commands
```

### 输出

```text

<a href="/commands/configuration">configuration</a>
<br/>
<a href="/commands/connection_stat_reset">connection_stat_reset</a>
<br/>
<a href="/commands/connections">connections</a>
<br/>
<a href="/commands/dirs">dirs</a>
<br/>
<a href="/commands/dump">dump</a>
<br/>
<a href="/commands/environment">environment</a>
<br/>
<a href="/commands/get_trace_mask">get_trace_mask</a>
<br/>
<a href="/commands/hash">hash</a>
<br/>
<a href="/commands/initial_configuration">initial_configuration</a>
<br/>
<a href="/commands/is_read_only">is_read_only</a>
<br/>
<a href="/commands/last_snapshot">last_snapshot</a>
<br/>
<a href="/commands/leader">leader</a>
<br/>
<a href="/commands/monitor">monitor</a>
<br/>
<a href="/commands/observer_connection_stat_reset">observer_connection_stat_reset</a>
<br/>
<a href="/commands/observers">observers</a>
<br/>
<a href="/commands/restore">restore</a>
<br/>
<a href="/commands/ruok">ruok</a>
<br/>
<a href="/commands/server_stats">server_stats</a>
<br/>
<a href="/commands/set_trace_mask">set_trace_mask</a>
<br/>
<a href="/commands/snapshot">snapshot</a>
<br/>
<a href="/commands/stat_reset">stat_reset</a>
<br/>
<a href="/commands/stats">stats</a>
<br/>
<a href="/commands/system_properties">system_properties</a>
<br/>
<a href="/commands/voting_view">voting_view</a>
<br/>
<a href="/commands/watch_summary">watch_summary</a>
<br/>
<a href="/commands/watches">watches</a>
<br/>
<a href="/commands/watches_by_path">watches_by_path</a>
<br/>
<a href="/commands/zabstate">zabstate</a>
<br/>
```

```bash

curl http://192.168.43.242:8080/commands/stat
```

### 输出
```json
{
  "version" : "3.9.3-c26634f34490bb0ea7a09cc51e05ede3b4e320ee, built on 2024-10-17 23:21 UTC",
  "read_only" : false,
  "server_stats" : {
    "packets_sent" : 0,
    "packets_received" : 0,
    "fsync_threshold_exceed_count" : 0,
    "client_response_stats" : {
      "last_buffer_size" : -1,
      "min_buffer_size" : -1,
      "max_buffer_size" : -1
    },
    "provider_null" : false,
    "server_state" : "follower",
    "outstanding_requests" : 0,
    "min_latency" : 0,
    "avg_latency" : 0.0,
    "max_latency" : 0,
    "data_dir_size" : 134218396,
    "log_dir_size" : 134218396,
    "last_processed_zxid" : 12884901890,
    "num_alive_client_connections" : 0,
    "auth_failed_count" : 0,
    "non_mtlsremote_conn_count" : 0,
    "non_mtlslocal_conn_count" : 0,
    "uptime" : 848520
  },
  "client_response" : {
    "last_buffer_size" : -1,
    "min_buffer_size" : -1,
    "max_buffer_size" : -1
  },
  "node_count" : 21,
  "connections" : [ ],
  "secure_connections" : [ ],
  "command" : "stats",
  "error" : null
}
```