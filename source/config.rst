
.. _configuration:

=============
Configuration
=============

The main configuration files of the *EMQ X* broker are under 'etc/' folder:

+----------------------+-----------------------------------+
| File                 | Description                       |
+----------------------+-----------------------------------+
| etc/emqx.conf        | *EMQ X* 3.0 Configuration File    |
+----------------------+-----------------------------------+
| etc/acl.conf         | The default ACL File              |
+----------------------+-----------------------------------+
| etc/plugins/\*.conf  | Config Files of Plugins           |
+----------------------+-----------------------------------+

-----------------------
EMQ X 3.0 Config Syntax
-----------------------

The *EMQ X* integrated with `cuttlefish` library, and uses a user-friendly `k = v` syntax for configuration file:

.. code-block:: properties

    ## Node name
    node.name = emqx@127.0.0.1
    ...
    ## Max ClientId Length Allowed.
    mqtt.max_clientid_len = 1024
    ...

The configuration files will be preprocessed and translated to Erlang `app.config` before the *EMQ X* broker started::

    ----------------------                                          3.0/schema/*.schema      -------------------
    | etc/emqx.conf      |                   -----------------              \|/              | data/app.config |
    |       +            | --> mergeconf --> | data/app.conf | -->  cuttlefish generate  --> |                 |
    | etc/plugins/*.conf |                   -----------------                               | data/vm.args    |
    ----------------------                                                                   -------------------

------------------------
OS Environment Variables
------------------------

+------------------+----------------------------------------+
| EMQX_NODE_NAME   | Erlang node name                       |
+------------------+----------------------------------------+
| EMQX_NODE_COOKIE | Cookie for distributed erlang node     |
+------------------+----------------------------------------+
| EMQX_MAX_PORTS   | Maximum number of opened sockets       |
+------------------+----------------------------------------+
| EMQX_TCP_PORT    | MQTT TCP Listener Port, Default: 1883  |
+------------------+----------------------------------------+
| EMQX_SSL_PORT    | MQTT SSL Listener Port, Default: 8883  |
+------------------+----------------------------------------+
| EMQX_WS_PORT     | MQTT/WebSocket Port, Default: 8083     |
+------------------+----------------------------------------+
| EMQX_WSS_PORT    | MQTT/WebSocket/SSL Port, Default: 8084 |
+------------------+----------------------------------------+

-------------
EMQ X Cluster
-------------

Cluster Name
------------

.. code-block:: properties

    ## Cluster name
    cluster.name = emqcl

Cluster Discovery
-----------------

.. code-block:: properties

    ## Cluster discovery strategy: manual | static | mcast | dns | etcd | k8s
    cluster.discovery = manual

Cluster Autoheal
----------------

.. code-block:: properties

    ## Cluster Autoheal: on | off
    cluster.autoheal = on

Cluster Autoclean
-----------------

.. code-block:: properties

    ## Clean down node of the cluster
    cluster.autoclean = 5m

----------------------------
EMQ X Autodiscovery Strategy
----------------------------

*EMQ X* 3.0 supports node discovery and autocluster with various strategies:

+------------+---------------------------------+
| Strategy   | Description                     |
+============+=================================+
| manual     | Create cluster manually         |
+------------+---------------------------------+
| static     | Autocluster by static node list |
+------------+---------------------------------+
| mcast      | Autocluster by UDP Multicast    |
+------------+---------------------------------+
| dns        | Autocluster by DNS A Record     |
+------------+---------------------------------+
| etcd       | Autocluster using etcd          |
+------------+---------------------------------+
| k8s        | Autocluster on Kubernetes       |
+------------+---------------------------------+

Create cluster manually
-----------------------

This is the default configuration of clustering, nodes joins a cluster `./bin/emqx_ctl join <Node>` command:

.. code-block:: properties

    cluster.discovery = manual

Autocluster by static node list
-------------------------------

.. code-block:: properties

    cluster.discovery = static

    ##--------------------------------------------------------------------
    ## Cluster with static node list

    cluster.static.seeds = emqx1@127.0.0.1,ekka2@127.0.0.1

Autocluster by IP Multicast
---------------------------

.. code-block:: properties

    cluster.discovery = mcast

    ##--------------------------------------------------------------------
    ## Cluster with multicast

    cluster.mcast.addr = 239.192.0.1

    cluster.mcast.ports = 4369,4370

    cluster.mcast.iface = 0.0.0.0

    cluster.mcast.ttl = 255

    cluster.mcast.loop = on

Autocluster by DNS A Record
---------------------------

.. code-block:: properties

    cluster.discovery = dns

    ##--------------------------------------------------------------------
    ## Cluster with DNS

    cluster.dns.name = localhost

    cluster.dns.app  = ekka

Autocluster using etcd
----------------------

.. code-block:: properties

    cluster.discovery = etcd

    ##--------------------------------------------------------------------
    ## Cluster with Etcd

    cluster.etcd.server = http://127.0.0.1:2379

    cluster.etcd.prefix = emqcl

    cluster.etcd.node_ttl = 1m

Autocluster on Kubernetes
-------------------------

.. code-block:: properties

    cluster.discovery = k8s

    ##--------------------------------------------------------------------
    ## Cluster with k8s

    cluster.k8s.apiserver = http://10.110.111.204:8080

    cluster.k8s.service_name = ekka

    ## Address Type: ip | dns
    cluster.k8s.address_type = ip

    ## The Erlang application name
    cluster.k8s.app_name = ekka

---------------------
EMQ X Node and Cookie
---------------------

The node name and cookie of *EMQ X* should be configured when clustering:

.. code-block:: properties

    ## Node name
    node.name = emqx@127.0.0.1

    ## Cookie for distributed node
    node.cookie = emqx_dist_cookie

---------------------------
Erlang Distributed Protocol
---------------------------

.. code-block:: properties

    ## Specify the erlang distributed protocol.
    ##
    ## Value: Enum
    ##  - inet_tcp: the default; handles TCP streams with IPv4 addressing.
    ##  - inet6_tcp: handles TCP with IPv6 addressing.
    ##  - inet_tls: using TLS for Erlang Distribution.
    ##
    ## vm.args: -proto_dist inet_tcp
    node.proto_dist = inet_tcp

    ## Specify SSL Options in the file if using SSL for Erlang Distribution.
    ##
    ## Value: File
    ##
    ## vm.args: -ssl_dist_optfile <File>
    ## node.ssl_dist_optfile = {{ platform_etc_dir }}/ssl_dist.conf

-------------------
Erlang VM Arguments
-------------------

Configure and Optimize Erlang VM:

.. code-block:: properties

    ## SMP support: enable, auto, disable
    node.smp = auto

    ## Enable kernel poll
    node.kernel_poll = on

    ## async thread pool
    node.async_threads = 32

    ## Erlang Process Limit
    node.process_limit = 256000

    ## Sets the maximum number of simultaneously existing ports for this system
    node.max_ports = 65536

    ## Set the distribution buffer busy limit (dist_buf_busy_limit)
    node.dist_buffer_size = 32MB

    ## Max ETS Tables.
    ## Note that mnesia and SSL will create temporary ets tables.
    node.max_ets_tables = 256000

    ## Tweak GC to run more often
    node.fullsweep_after = 1000

    ## Crash dump
    node.crash_dump = log/crash.dump

    ## Distributed node ticktime
    node.dist_net_ticktime = 60

    ## Distributed node port range
    ## node.dist_listen_min = 6000
    ## node.dist_listen_max = 6999

The two most important parameters for Erlang VM:

+--------------------------+---------------------------------------------------------------------------+
| node.process_limit       | Max number of Erlang proccesses. A MQTT client consumes two proccesses.   |
|                          | The value should be larger than max_clients * 2                           |
+--------------------------+---------------------------------------------------------------------------+
| node.max_ports           | Max number of Erlang Ports. A MQTT client consumes one port.              |
|                          | The value should be larger than max_clients.                              |
+--------------------------+---------------------------------------------------------------------------+

------------------
Log Level and File
------------------

Console Log
-----------

.. code-block:: properties

    ## Console log. Enum: off, file, console, both
    log.console = console

    ## Console log level. Enum: debug, info, notice, warning, error, critical, alert, emergency
    log.console.level = error

    ## Console log file
    ## log.console.file = log/console.log

Error Log
---------

.. code-block:: properties

    ## Error log file
    log.error.file = log/error.log

Crash Log
---------

.. code-block:: properties

    ## Enable the crash log. Enum: on, off
    log.crash = on

    log.crash.file = log/crash.log

Syslog
------

.. code-block:: properties

    ## Syslog. Enum: on, off
    log.syslog = on

    ##  syslog level. Enum: debug, info, notice, warning, error, critical, alert, emergency
    log.syslog.level = error

------------------------
MQTT Protocol Parameters
------------------------

Maximum ClientId Length
-----------------------

.. code-block:: properties

    ## Max ClientId Length Allowed.
    mqtt.max_clientid_len = 1024

Maximum Packet Size
-------------------

.. code-block:: properties

    ## Max Packet Size Allowed, 64K by default.
    mqtt.max_packet_size = 64KB

MQTT Client Idle Timeout
------------------------

.. code-block:: properties

    ## Client Idle Timeout (Second)
    mqtt.client.idle_timeout = 30

Enable Per Client Statistics
----------------------------

.. code-block:: properties

    ## Enable client Stats: on | off
    mqtt.client.enable_stats = off

Force GC Count
--------------

.. code-block:: properties

    ## Force GC: integer. Value 0 disabled the Force GC.
    mqtt.conn.force_gc_count = 100

----------------------------
Allow Anonymous and ACL File
----------------------------

Allow Anonymous
---------------

.. code-block:: properties

    ## Allow Anonymous authentication
    mqtt.allow_anonymous = true

Default ACL File
----------------

Enable the default ACL module:

.. code-block:: properties

    ## ACL nomatch
    mqtt.acl_nomatch = allow

    ## Default ACL File
    mqtt.acl_file = etc/acl.conf

Define ACL rules in etc/acl.conf. The rules by default:

.. code-block:: erlang

    %% Allow 'dashboard' to subscribe '$SYS/#'
    {allow, {user, "dashboard"}, subscribe, ["$SYS/#"]}.

    %% Allow clients from localhost to subscribe any topics
    {allow, {ipaddr, "127.0.0.1"}, pubsub, ["$SYS/#", "#"]}.

    %% Deny clients to subscribe '$SYS#' and '#'
    {deny, all, subscribe, ["$SYS/#", {eq, "#"}]}.

    %% Allow all by default
    {allow, all}.

An ACL rule is an Erlang tuple. The Access control module of *EMQ X* broker matches the rule one by one from top to bottom::

              ---------              ---------              ---------
    Client -> | Rule1 | --nomatch--> | Rule2 | --nomatch--> | Rule3 | --> Default
              ---------              ---------              ---------
                  |                      |                      |
                match                  match                  match
                 \|/                    \|/                    \|/
            allow | deny           allow | deny           allow | deny

-----------------------
MQTT Session Parameters
-----------------------

.. code-block:: properties

    ## Upgrade QoS?
    mqtt.session.upgrade_qos = off

    ## Max number of QoS 1 and 2 messages that can be “inflight” at one time.
    ## 0 means no limit
    mqtt.session.max_inflight = 32

    ## Retry Interval for redelivering QoS1/2 messages.
    mqtt.session.retry_interval = 20s

    ## Max Packets that Awaiting PUBREL, 0 means no limit
    mqtt.session.max_awaiting_rel = 100

    ## Awaiting PUBREL Timeout
    mqtt.session.await_rel_timeout = 20s

    ## Enable Statistics: on | off
    mqtt.session.enable_stats = off

    ## Expired after 1 day:
    ## w - week
    ## d - day
    ## h - hour
    ## m - minute
    ## s - second
    mqtt.session.expiry_interval = 2h

+------------------------------+----------------------------------------------------------+
| session.upgrade_qos          | Upgrade QoS according to the subscription                |
+------------------------------+----------------------------------------------------------+
| session.max_inflight         | Max number of QoS1/2 messages that can be delivered at   |
|                              | the same time                                            |
+------------------------------+----------------------------------------------------------+
| session.retry_interval       | Retry interval for unacked QoS1/2 messages.              |
+------------------------------+----------------------------------------------------------+
| session.await_rel_timeout    | Awaiting PUBREL Timeout                                  |
+------------------------------+----------------------------------------------------------+
| session.max_awaiting_rel     | Max number of Packets that Awaiting PUBREL               |
+------------------------------+----------------------------------------------------------+
| session.enable_stats         | Interval of Statistics Collection                        |
+------------------------------+----------------------------------------------------------+
| session.expiry_interval      | Session expiry interval                                  |
+------------------------------+----------------------------------------------------------+

------------------
MQTT Message Queue
------------------

The message queue of session stores:

1. Offline messages for persistent session.

2. Pending messages for inflight window is full

Queue parameters:

.. code-block:: properties

    ## Type: simple | priority
    mqtt.mqueue.type = simple

    ## Topic Priority: 0~255, Default is 0
    ## mqtt.mqueue.priority = topic/1=10,topic/2=8

    ## Max queue length. Enqueued messages when persistent client disconnected,
    ## or inflight window is full.
    mqtt.mqueue.max_length = infinity

    ## Low-water mark of queued messages
    mqtt.mqueue.low_watermark = 20%

    ## High-water mark of queued messages
    mqtt.mqueue.high_watermark = 60%

    ## Queue Qos0 messages?
    mqtt.mqueue.qos0 = true

+-----------------------+---------------------------------------------------+
| mqueue.type           | Queue type: simple or priority                    |
+-----------------------+---------------------------------------------------+
| mqueue.priority       | Topic priority                                    |
+-----------------------+---------------------------------------------------+
| mqueue.max_length     | Max Queue size, infinity means no limit           |
+-----------------------+---------------------------------------------------+
| mqueue.low_watermark  | Low watermark                                     |
+-----------------------+---------------------------------------------------+
| mqueue.high_watermark | High watermark                                    |
+-----------------------+---------------------------------------------------+
| mqueue.qos0           | If Qos0 message queued?                           |
+-----------------------+---------------------------------------------------+

----------------------
Sys Interval of Broker
----------------------

.. code-block:: properties

    ## System Interval of publishing broker $SYS Messages
    mqtt.broker.sys_interval = 60s

-----------------
PubSub Parameters
-----------------

.. code-block:: properties

    ## PubSub Pool Size. Default should be scheduler numbers.
    mqtt.pubsub.pool_size = 8

    mqtt.pubsub.by_clientid = true

    ##TODO: Subscribe Asynchronously
    mqtt.pubsub.async = true

----------------------
EMQ X Edge Config Parameters
----------------------

EMQ X Edge is a lightweight MQTT messaging broker, which can be running at resource constrained edge devices. 

It provides local data processing, and cloud synchronization which automatically synchronizes data to the cloud, for example, EMQ X Broker deployed on aws.

Config Bridge to a EMQ X Broker on aws:

.. code-block:: properties

    ## Bridge address
    mqtt.bridge.max_queue_len = 10000

    ## Ping Interval of bridge node. Unit: Second
    mqtt.bridge.ping_down_interval = 1s

-------------------
Plugins' Etc Folder
-------------------

.. code-block:: properties

    ## Dir of plugins' config
    mqtt.plugins.etc_dir = etc/plugins/

    ## File to store loaded plugin names.
    mqtt.plugins.loaded_file = data/loaded_plugins

--------------
MQTT Listeners
--------------

Configure the TCP listeners for MQTT, MQTT/SSL, MQTT/WS, MQTT/WSS Protocols.

The most important parameter for MQTT listener is `max_clients`: max concurrent clients allowed.

The TCP Ports occupied by the *EMQ X* broker by default:

+-----------+-----------------------------------+
| 1883      | MQTT Port                         |
+-----------+-----------------------------------+
| 8883      | MQTT/SSL Port                     |
+-----------+-----------------------------------+
| 8083      | MQTT/WebSocket Port               |
+-----------+-----------------------------------+
| 8084      | MQTT/WebSocket/SSL                |
+-----------+-----------------------------------+
| 8080      | HTTP Management API               |
+-----------+-----------------------------------+

Listener Parameters:

+----------------------------------+-------------------------------------------------------+
| listener.tcp.${name}.acceptors   | TCP Acceptor Pool                                     |
+----------------------------------+-------------------------------------------------------+
| listener.tcp.${name}.max_clients | Maximum number of concurrent TCP connections allowed  |
+----------------------------------+-------------------------------------------------------+
| listener.tcp.${name}.rate_limit  | Maximum number of concurrent TCP connections allowed  |
+----------------------------------+-------------------------------------------------------+

MQTT/TCP Listener - 1883
-------------------------

*EMQ X* 3.0 supports configuration of multiple MQTT listeners.

.. code-block:: properties

    ##--------------------------------------------------------------------
    ## External TCP Listener

    ## External TCP Listener: 1883, 127.0.0.1:1883, ::1:1883
    listener.tcp.external = 0.0.0.0:1883

    ## Size of acceptor pool
    listener.tcp.external.acceptors = 16

    ## Maximum number of concurrent clients
    listener.tcp.external.max_clients = 102400

    #listener.tcp.external.mountpoint = external/

    ## Rate Limit. Format is 'burst,rate', Unit is KB/Sec
    #listener.tcp.external.rate_limit = 100,10

    #listener.tcp.external.access.1 = allow 192.168.0.0/24

    listener.tcp.external.access.2 = allow all

    ## Proxy Protocol V1/2
    ## listener.tcp.external.proxy_protocol = on
    ## listener.tcp.external.proxy_protocol_timeout = 3s

    ## TCP Socket Options
    listener.tcp.external.backlog = 1024

    #listener.tcp.external.recbuf = 4KB

    #listener.tcp.external.sndbuf = 4KB

    listener.tcp.external.buffer = 4KB

    listener.tcp.external.nodelay = true

    ##--------------------------------------------------------------------
    ## Internal TCP Listener

    ## Internal TCP Listener: 11883, 127.0.0.1:11883, ::1:11883
    listener.tcp.internal = 127.0.0.1:11883

    ## Size of acceptor pool
    listener.tcp.internal.acceptors = 16

    ## Maximum number of concurrent clients
    listener.tcp.internal.max_clients = 102400

    #listener.tcp.external.mountpoint = internal/

    ## Rate Limit. Format is 'burst,rate', Unit is KB/Sec
    ## listener.tcp.internal.rate_limit = 1000,100

    ## TCP Socket Options
    listener.tcp.internal.backlog = 512

    listener.tcp.internal.tune_buffer = on

    listener.tcp.internal.buffer = 1MB

    listener.tcp.internal.recbuf = 4KB

    listener.tcp.internal.sndbuf = 1MB

    listener.tcp.internal.nodelay = true

MQTT/SSL Listener - 8883
-------------------------

.. code-block:: properties

    ##--------------------------------------------------------------------
    ## External SSL Listener
    listener.ssl.external = 8883

    ## Size of acceptor pool
    listener.ssl.external.acceptors = 16

    ## Maximum number of concurrent clients
    listener.ssl.external.max_clients = 1024

    ## listener.ssl.external.mountpoint = inbound/

    ## Rate Limit. Format is 'burst,rate', Unit is KB/Sec
    ## listener.ssl.external.rate_limit = 100,10

    ## Proxy Protocol V1/2
    ## listener.ssl.external.proxy_protocol = on
    ## listener.ssl.external.proxy_protocol_timeout = 3s

    listener.ssl.external.access.1 = allow all

    ## SSL Options
    listener.ssl.external.handshake_timeout = 15
    listener.ssl.external.keyfile = etc/certs/key.pem
    listener.ssl.external.certfile = etc/certs/cert.pem
    ## listener.ssl.external.cacertfile = etc/certs/cacert.pem
    ## listener.ssl.external.verify = verify_peer
    ## listener.ssl.external.fail_if_no_peer_cert = true

MQTT/WebSocket Listener - 8083
------------------------------

.. code-block:: properties

    ##--------------------------------------------------------------------
    ## External MQTT/WebSocket Listener

    listener.ws.external = 8083

    listener.ws.external.acceptors = 4

    listener.ws.external.max_clients = 64

    listener.ws.external.access.1 = allow all

MQTT/Websocket/SSL Listener - 8084
-----------------------------------

.. code-block:: properties

    ##--------------------------------------------------------------------
    ## External MQTT/WebSocket/SSL Listener

    listener.wss.external = 8084

    listener.wss.external.acceptors = 4

    listener.wss.external.max_clients = 64

    listener.wss.external.access.1 = allow all

    ## SSL Options
    listener.wss.external.handshake_timeout = 15s

    listener.wss.external.keyfile = {{ platform_etc_dir }}/certs/key.pem

    listener.wss.external.certfile = {{ platform_etc_dir }}/certs/cert.pem

    ## listener.wss.external.cacertfile = {{ platform_etc_dir }}/certs/cacert.pem

    ## listener.wss.external.verify = verify_peer

    ## listener.wss.external.fail_if_no_peer_cert = true

HTTP API Listener - 8080
------------------------

.. code-block:: properties

    ##--------------------------------------------------------------------
    ## HTTP Management API Listener

    listener.api.mgmt = 127.0.0.1:8080

    listener.api.mgmt.acceptors = 4

    listener.api.mgmt.max_clients = 64

    listener.api.mgmt.access.1 = allow all

--------------
System Monitor
--------------

.. code-block:: properties

    ## Long GC, don't monitor in production mode for:
    sysmon.long_gc = false

    ## Long Schedule(ms)
    sysmon.long_schedule = 240

    ## 8M words. 32MB on 32-bit VM, 64MB on 64-bit VM.
    sysmon.large_heap = 8MB

    ## Busy Port
    sysmon.busy_port = false

    ## Busy Dist Port
    sysmon.busy_dist_port = true

--------------------------
Plugin Configuration Files
--------------------------

+----------------------------------------+--------------------------------------+
| File                                   | Description                          |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_auth_username.conf    | Username/Password Auth Plugin Config |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_auth_clientid.conf    | ClientId Auth Plugin Config          |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_ldap.conf             | LDAP Auth/ACL Plugin Config          |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_auth_http.conf        | HTTP Auth/ACL Plugin Config          |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_auth_mongo.conf       | MongoDB Auth/ACL Plugin Config       |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_auth_mysql.conf       | MySQL Auth/ACL Plugin Config         |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_auth_pgsql.conf       | Postgre Auth/ACL Plugin Config       |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_auth_redis.conf       | Redis Auth/ACL Plugin Config         |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_coap.conf             | CoAP Protocol Plugin Config          |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_retainer.conf         | Retainer Plugin Config               |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_web_hook.conf         | Web Hook Plugin Config               |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_lua_hook.conf         | Lua Hook Plugin Config               |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_dashboard.conf        | Dashboard Plugin Config              |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_plugin_template.conf  | Template Plugin Config               |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_recon.conf            | Recon Plugin Config                  |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_reloader.conf         | Reloader Plugin Config               |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_sn.conf               | MQTT-SN Protocal Plugin Config       |
+----------------------------------------+--------------------------------------+
| etc/plugins/emqx_stomp.conf            | Stomp Protocl Plugin Config          |
+----------------------------------------+--------------------------------------+
