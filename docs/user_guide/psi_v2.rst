PSI v2 QuickStart
=================

Release Docker
--------------

Check official release docker image at `dockerhub <https://hub.docker.com/r/secretflow/psi-anolis8>`_. We also have mirrors at Alibaba Cloud: secretflow-registry.cn-hangzhou.cr.aliyuncs.com/secretflow/psi-anolis8.


Prepare data and config
-----------------------

Please check details of configs at :doc:`/reference/psi_v2_config`.

.. code-block::
   :caption: receiver.config

        {
            "protocol_config": {
                "protocol": "PROTOCOL_KKRT",
                "role": "ROLE_RECEIVER",
                "broadcast_result": true
            },
            "input_config": {
                "type": "IO_TYPE_FILE_CSV",
                "path": "/root/receiver/receiver_input.csv"
            },
            "output_config": {
                "type": "IO_TYPE_FILE_CSV",
                "path": "/root/receiver/receiver_output.csv"
            },
            "link_config": {
                "parties": [
                    {
                        "id": "receiver",
                        "host": "127.0.0.1:5300"
                    },
                    {
                        "id": "sender",
                        "host": "127.0.0.1:5400"
                    }
                ]
            },
            "self_link_party": "receiver",
            "keys": [
                "id0",
                "id1"
            ],
            "debug_options": {
                "trace_path": "/root/receiver/receiver.trace"
            },
            "check_duplicates": false,
            "sort_output": false,
            "recovery_config": {
                "enabled": false,
            }
        }


.. code-block::
   :caption: sender.config

        {
            "protocol_config": {
                "protocol": "PROTOCOL_KKRT",
                "role": "ROLE_SENDER",
                "broadcast_result": true
            },
            "input_config": {
                "type": "IO_TYPE_FILE_CSV",
                "path": "/root/sender/sender_input.csv"
            },
            "output_config": {
                "type": "IO_TYPE_FILE_CSV",
                "path": "/root/sender/sender_output.csv"
            },
            "link_config": {
                "parties": [
                    {
                        "id": "receiver",
                        "host": "127.0.0.1:5300"
                    },
                    {
                        "id": "sender",
                        "host": "127.0.0.1:5400"
                    }
                ]
            },
            "self_link_party": "sender",
            "keys": [
                "id0",
                "id1"
            ],
            "debug_options": {
                "trace_path": "/root/sender/sender.trace"
            },
            "check_duplicates": false,
            "sort_output": false,
            "recovery_config": {
                "enabled": false,
            }
        }


You need to prepare following files:

+------------------------+------------------------------------------------+-------------------------------------------------------------------------------+
| File Name              | Location                                       | Description                                                                   |
+========================+================================================+===============================================================================+
| receiver.config        | /tmp/receiver/receiver.config                  | Config for receiver.                                                          |
+------------------------+------------------------------------------------+-------------------------------------------------------------------------------+
| sender.config          | /tmp/sender/sender.config                      | Config for sender.                                                            |
+------------------------+------------------------------------------------+-------------------------------------------------------------------------------+
| receiver_input.csv     | /tmp/receiver/receiver_input.config            | SupInput for receiver. Make sure the file contains two id keys - id0 and id1. |
+------------------------+------------------------------------------------+-------------------------------------------------------------------------------+
| sender_input.csv       | /tmp/sender/sender_input.config                | Input for sender. Make sure the file contains two id keys - id0 and id1.      |
+------------------------+------------------------------------------------+-------------------------------------------------------------------------------+


Run PSI
-------

In the first terminal, run the following command::

    docker run -it  --rm  --network host --mount type=bind,source=/tmp/receiver,target=/root/receiver -w /root  --cap-add=SYS_PTRACE --security-opt seccomp=unconfined --cap-add=NET_ADMIN --privileged=true secretflow-registry.cn-hangzhou.cr.aliyuncs.com/secretflow/psi-anolis8:0.1.0beta bash -c "./main --config receiver/receiver.config"


In the other terminal, run the following command simultaneously::

    docker run -it  --rm  --network host --mount type=bind,source=/tmp/sender,target=/root/sender -w /root  --cap-add=SYS_PTRACE --security-opt seccomp=unconfined --cap-add=NET_ADMIN --privileged=true secretflow-registry.cn-hangzhou.cr.aliyuncs.com/secretflow/psi-anolis8:0.1.0beta bash -c "./main --config sender/sender.config"


Building from source
--------------------

You could build psi binary with bazel::

    bazel build psi/psi:main -c opt


Then use binary with::

    ./bazel-bin/psi/psi/main --config <config JSON file path>