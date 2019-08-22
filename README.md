# jinja_yaml2xml
Converts YAML to XML using Jinja 2

Made for use with [saltstack formula for clickhouse](https://github.com/sergiustheblack/clickhouse-saltstack-formula)

## Usage
All parameters in YAML will be converted to XML as is.
Except "xmlattributes" and "value", which used in a special way. For example:

```
compression:
  xmlattributes:
    incl: clickhouse_compression
    replace: replace
  case:
    min_part_size: 10000000000
    min_part_size_ratio: 0.01
    method: zstd
interserver_http_port:
  xmlattributes:
    incl: http
  value: 9009
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
<interserver_http_port incl="http">9009</interserver_http_port>
```
But without xmlattributes
```
sample:
  value: click

<sample>
    <value>click</value>
</sample>
```
