
.. _advanced:

==================
Advanced Features
==================

-------------------
Shared Subscription
-------------------

*EMQ X* 3.0 supports shared subscription at cluster level. It allows load balancing between multiple subscribers in the same group when distributing MQTT messages. ::

                                -----------
                                |         | --Msg1--> Subscriber1
    Publisher--Msg1,Msg2,Msg3-->|  EMQ X  | --Msg2--> Subscriber2
                                |         | --Msg3--> Subscriber3
                                -----------

Two ways to create a shared subscription:

+-----------------+-------------------------------------------+
|  Prefix         | Examples                                  |
+-----------------+-------------------------------------------+
| $queue/         | mosquitto_sub -t '$queue/topic'           |
+-----------------+-------------------------------------------+
| $share/<group>/ | mosquitto_sub -t '$share/group/topic'     |
+-----------------+-------------------------------------------+

example:
.. code-block:: shell

    mosquitto_sub -t '$share/group/topic'

    mosquitto_pub -t 'topic' -m msg -q 2
