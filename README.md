# jinja_yaml2xml
Converts YAML to XML using Jinja 2

Made for use with [saltstack formula for clickhouse](https://github.com/sergiustheblack/clickhouse-saltstack-formula)

## Usage
All parameters in YAML will be converted to XML as is. To indicate xml attributes use "@"-prefixed keys.
```
compression:
  "@incl": clickhouse_compression
  "@replace": replace
  case:
    min_part_size: 10000000000
    min_part_size_ratio: 0.01
    method: zstd
```
Will be expanded to
```
<compression incl="clickhouse_compression" replace="replace">
    <case>
        <min_part_size>10000000000</min_part_size>
        <min_part_size_ratio>0.01</min_part_size_ratio>
        <method>zstd</method>
    </case>
</compression>
```
Simple key-value pairs with xml attributes are not so simple and should be assigned with lists.
```
sample:
  value: click

interserver_http_port:
  - "@incl": http
  - 9009

<sample>
    <value>click</value>
</sample>

<interserver_http_port incl="http">9009</interserver_http_port>
```
Lists are exteremely ugly (thanks, Clickhouse's native YAML processor)
```
   <zookeeper>
       <node index="1">
           <host>zk1.example.com</host>
           <port>2181</port>
       </node>
       <node index="2">
           <host>zk2.example.com</host>
           <port>2181</port>
       </node>
       <node index="3">
           <host>zk3.example.com</host>
       </node>
       <node index="4">
           <host>zk4.example.com</host>
           <port>2181</port>
       </node>
   </zookeeper>
```
```
  zookeeper:
    node:
    - - "@index": 1
      - host: zk1.example.com
      - port: 2181
    - - "@index": 2
      - host: zk2.example.com
      - port: 2181
    - - "@index": 3
      - host: zk3.example.com
      - port: 2181
```
And the most head-breaking example:
```
  remote_servers:
    "@replace": replace
    clickhouse_cluster01:
      shard:
      - - internal_replication: true
        - replica:
            host: 192.168.0.1
            port: 9440
            secure: true
        - replica:
            host: 192.168.0.2
            port: 9440
            secure: true
      - - internal_replication: true
        - replica:
            host: 192.168.0.3
            port: 9440
            secure: true
        - replica:
            host: 192.168.0.4
            port: 9440
            secure: true
```
