---
layout: page
title: "BigQuery Schema"
permalink: /data/bq/schema/
breadcrumb: data
---

* Table of Contents
{:toc}

# BigQuery Schema

## Background

* Each M-Lab tool consists of a **client** and a **server**.
* Whenever an M-Lab user starts a test, the client and server interact to measure different aspects of that user's connection.
* A single user request triggers one or more **tests** (e.g., client-to-server test, server-to-client test).
* For each test, a server collects a **log**, and the test can be uniquely identified by its log filename.

## BigQuery Tables

M-Lab publishes data for each M-Lab project in its own BigQuery table:

* `plx.google:m_lab.ndt.all`
* `plx.google:m_lab.npad.all`
* `plx.google:m_lab.sidestream.all`
* `plx.google:m_lab.paris_traceroute.all`

### Legacy Tables

M-Lab also publishes tables in its legacy, deprecated per-month format:

`plx.google:m_lab.YYYY_MM.all` (*deprecated*)

For example, data for March 2016 is published at:

`plx.google:m_lab.2016_03.all` (*deprecated*)

M-Lab continues to support legacy tables but urges clients to move to the new per-project tables, which yield substantially improved performance. See the [Legacy Migration Guide]({{ site.baseurl }}/data/bq/legacymigration) for details.

## Schema Fields

All M-Lab data share the same data schema in BigQuery. The fields are described in the table below:

<div class="table-responsive" markdown="1">

| Field name                                           |     Type     |  Description                              |
| :----------------------------------------------------|:------------:|:------------------------------------------|
| `test_id`                                           |  `string`    |  ID of the test. It represents the filename of the log that contains the data generated during the test (e.g. `20090819T02:01:04.507508000Z_189.6.232.77:3859.c2s_snaplog.gz`). |
| `log_time`                                          |  `integer`   |  Timestamp of when test log was created (in seconds since Unix epoch).<br><br>For NDT and NPAD, this is derived from the "Date/Time" field in the .meta file (for NDT and NPAD, prefer the `web100_log_entry.log_time` field, as it is more reliable).<br><br>For SideStream and Paris Traceroute, this is the timestamp as represented in the test log file's filename. |
| `connection_spec.data_direction`                    |  `integer`   |  Direction of the data sent during the test:<br>CLIENT_TO_SERVER = `0`<br>SERVER_TO_CLIENT = `1` |
| `connection_spec.server_ip`                         |  `string`    |  Server's IP address. (This field is **optional**. It's preferable to use `web100_log_entry.connection_spec.local_ip`.) |
| `connection_spec.server_af`                         |  `integer`   |  Address family of the server's IP address. (This field is **optional**. It's preferable to use `web100_log_entry.connection_spec.local_af`.) |
| `connection_spec.server_hostname`                   |  `string`    |  Server's hostname. (This field is **optional**.) |
| `connection_spec.server_kernel_version`             |  `string`    |  Server's kernel version. (This field is **optional**.) |
| `connection_spec.client_ip`                         |  `string`    |  IP address of the user's client. (This field is **optional**. It's preferable to use `web100_log_entry.connection_spec.remote_ip`.) |
| `connection_spec.client_af`                         |  `integer`   |  Address family of the client's IP address. (This field is **optional**.) |
| `connection_spec.client_hostname`                   |  `string`    |  Client's hostname. (This field is **optional**.) |
| `connection_spec.client_application`                |  `string`    |  Client application that ran the test. (This field is **optional**.) |
| `connection_spec.client_browser`                    |  `string`    |  Client's browser. (This field is **optional**.) |
| `connection_spec.client_os`                         |  `string`    |  Client's operating system. (This field is **optional**.) |
| `connection_spec.client_kernel_version`             |  `string`    |  Client's kernel version. (This field is **optional**.) |
| `connection_spec.client_version`                    |  `string`    |  Client's version. (This field is **optional**.) |
| `connection_spec.client_geolocation.continent_code` |  `string`    |  Geolocation fields extracted from open dataset created by MaxMind and available at [www.maxmind.com](https://www.maxmind.com). (These fields are **optional**.) |
| `connection_spec.client_geolocation.country_code`   |  `string`    |   |
| `connection_spec.client_geolocation.country_code3`  |  `string`    |   |
| `connection_spec.client_geolocation.country_name`   |  `string`    |   |
| `connection_spec.client_geolocation.region`         |  `string`    |   |
| `connection_spec.client_geolocation.metro_code`     |  `integer`   |   |
| `connection_spec.client_geolocation.city`           |  `string`    |   |
| `connection_spec.client_geolocation.area_code`      |  `integer`   |   |
| `connection_spec.client_geolocation.postal_code`    |  `string`    |   |
| `connection_spec.client_geolocation.latitude`       |  `float`     |   |
| `connection_spec.client_geolocation.longitude`      |  `float`     |   |
| `connection_spec.server_geolocation.continent_code` |  `string`    |   |
| `connection_spec.server_geolocation.country_code`   |  `string`    |   |
| `connection_spec.server_geolocation.country_code3`  |  `string`    |   |
| `connection_spec.server_geolocation.country_name`   |  `string`    |   |
| `connection_spec.server_geolocation.region`         |  `string`    |   |
| `connection_spec.server_geolocation.metro_code`     |  `integer`   |   |
| `connection_spec.server_geolocation.city`           |  `string`    |   |
| `connection_spec.server_geolocation.area_code`      |  `integer`   |   |
| `connection_spec.server_geolocation.postal_code`    |  `string`    |   |
| `connection_spec.server_geolocation.latitude`       |  `float`     |   |
| `connection_spec.server_geolocation.longitude`      |  `float`     |   |
| `web100_log_entry.version`                          |  `string`    |  Web100 kernel patch version running on the server (as defined in `/proc/web100/header`). |
| `web100_log_entry.log_time`                         |  `integer`   |  Timestamp of when the Web100 log was created (in seconds since Unix epoch).<br><br>For NDT and NPAD, this is derived by calling the [`web100_get_log_time()`](https://github.com/web100/web100-userland/blob/master/lib/web100.h) function on the web100 log file.<br><br>For SideStream, this is the value of the `PollTime` field in web100 ASCII log. |
| `web100_log_entry.connection_spec.local_ip`         |  `string`    |  IP address of the M-Lab server, as logged in the Web100 log. |
| `web100_log_entry.connection_spec.local_af`         |  `integer`   |  Address family of the server's IP address, as logged in the Web100 log.  |
| `web100_log_entry.connection_spec.local_port`       |  `integer`   |  Port of the M-Lab server (in host-byte-order), as logged in the Web100 log. |
| `web100_log_entry.connection_spec.remote_ip`        |  `string`    |  IP address of the user's client, as logged in the Web100 log. |
| `web100_log_entry.connection_spec.remote_port`      |  `integer`   |  Port of the user's client (in host-byte-order), as logged in the Web100 log. |
| `web100_log_entry.snap.[web100_var_name]`, where `web100_var_name` is the name of a Web100 variable, as defined in [tcp-kis.txt][1] (field `VariableName`). |  See Web100 types |  [tcp-kis.txt][1] defines 150 Web100 variables. For example, `web100_log_entry.snap.MinRTT` represents the minimum sampled Round Trip Time. |
| `web100_log_entry.snap.StartTimeStamp`              |  `integer`   |  Time at which the test's TCP connection was established, in microseconds since UNIX epoch. This variable is a special case, as it contradicts [tcp-kis.txt](https://cloud.google.com/bigquery/docs/tcp-kis.txt){:target="_blank"}. tcp-kis.txt defines the field as a 32-bit integer, but remaps two distinct 32-bit integers into this single name, which is not possible for a 32-bit value. To work around this bug in tcp-kis and provide microsecond precision, this field is a 64-bit integer in the BigQuery dataset. |
| `paris_traceroute_hop.protocol`                     |  `integer`   |  Protocol used to generate the paris-traceroute trace.<br>UDP = `0`<br>TCP = `1`<br>ICMP = `2` |
| `paris_traceroute_hop.src_ip`                       |  `string`    |  The IP address of the start of the hop. |
| `paris_traceroute_hop.src_af`                       |  `integer`   |  The address family used to connect to `src_ip`.<br>AF_INET = `2`<br>AF_INET6 = `10` |
| `paris_traceroute_hop.src_hostname`                 |  `string`    |  The hostname of the start of the hop. This may be the same as `src_ip` if the hostname could not be resolved. |
| `paris_traceroute_hop.dest_ip`                      |  `string`    |  The IP address of the end of the hop. |
| `paris_traceroute_hop.dest_af`                      |  `integer`   |  The address family used to connect to `dest_ip`.<br>AF_INET = `2`<br>AF_INET6 = `10`. |
| `paris_traceroute_hop.dest_hostname`                |  `string`    |  The hostname of the end of the hop. This may be the same as `dest_ip` if the hostname could not be resolved. |
| `paris_traceroute_hop.rtt`                          |  `float`     |  The RTT measured from `connection_spec.server_ip` to `paris_traceroute_hop.dest_ip`. |

</div>

### Deprecated Fields

The following fields are deprecated and no longer have meaning in the data set.

* `type`
* `project`
* `web100_log_entry.is_last_entry`
* `web100_log_entry.group_name`

### Equivalent BigQuery and Web100 Field Types

[tcp-kis.txt](https://cloud.google.com/bigquery/docs/tcp-kis.txt){:target="_blank"} defines each Web100 variable with a specific [SNMP type](http://tools.ietf.org/html/rfc4898){:target="_blank"}. This table shows how to map each SNMP type to a BigQuery type.

| BigQuery Type |  Corresponding SNMP Type |
| ------------- | -------------------------|
| `integer`     |  `Integer32`, `Integer`, `INTEGER`, `Gauge32`, `ZeroBasedCounter32`, `Unsigned32`, `Unsigned16`, `Counter32`, `ZeroBasedCounter64` |
| `string`      |  `Ip_Address`            |
| `bool`        |  `TruthValue`            |

### Blacklist Flags Field

An additional field called ```blacklist_flags``` was added to all tables in response to a switch discard issue, an episode of degraded performance. See our [blog post]({{ site.baseurl }}/blog/traffic-microbursts-and-their-effect-on-internet-measurement) for complete details.

The ```blacklist_flags``` field is used to mark test results that could be impacted by site configuration issues, or otherwise communicate potentially relevant information about the state of the platform at the time of the test. This field was created to mark tests affected by the "switch discard issue" identified in 2015-2016, but M-Lab may use the field for other use cases in the future.

Currently, the following values are present in our data in this field:

| Field Name | Value | Description |
| ---------- | ----- | ----------- |
| `blacklist_flags` | `0` | unaffected tests |
| | `1` | tests affected by switch discards |
| | `2` | tests not shown to be unaffected by switch discards |

M-Lab recommends that you update your queries to include ```AND blacklist_flags == 0``` to limit results to unaffected test results. Sample queries for reference are listed below.

~~~sql
# Sample Fast Table query limiting to unaffected tests

SELECT
  num_tests,
FROM
  (
    SELECT
      COUNT(*) num_tests,
    FROM
      plx.google:m_lab.ndt.all
    WHERE
      web100_log_entry.log_time >= PARSE_UTC_USEC('2009-08-01 00:00:00') / POW(10, 6)
      AND web100_log_entry.log_time < PARSE_UTC_USEC('2009-09-01 00:00:00') / POW(10, 6)
      AND blacklist_flags == 0
  )
GROUP BY
  num_tests
~~~

~~~sql
# Sample Legacy Monthly Table query limiting to unaffected tests

SELECT
  num_tests,
FROM
  (
    SELECT
      COUNT(*) num_tests,
    FROM
      plx.google:m_lab.2009_08.all
    WHERE
      web100_log_entry.log_time >= PARSE_UTC_USEC('2009-08-01 00:00:00') / POW(10, 6)
      AND web100_log_entry.log_time < PARSE_UTC_USEC('2009-09-01 00:00:00') / POW(10, 6)
      AND blacklist_flags == 0
      AND web100_log_entry.is_last_entry == 1
  )
GROUP BY
  num_tests
~~~

## BigQuery Examples

See [BigQuery Examples]({{ site.baseurl }}/data/bq/examples) for examples of queries against this schema.