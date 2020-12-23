# RabbitMQ last key value queue

This code provides new type of queue, which is replaces elements in queue by key.
It is helpful to update queue by newest elements (some ticks, real-time data and etc).

Example of work:

Presentation of queue is ``[ Head, SecondElement, ... LastElememt ]``

Head is going to as next data for consumers

Initial state:
```
{key_1, value_1},{key_2, value_2},
```
Step 1: Add new non existed element {key_3, value_3} :
```
{key_1, value_1}, {key_2, value_2}, {key_3, value_3}
```
Step 2: Updating element {key_1, new_value_1}:
```
{key_2, value_2}, {key_3, value_3}, {key_1, new_value_1}
```

# Usage

## Initialisation

Before using you must define this queue. Queue will be created by this module if you will provide specific key for argument (options) of creation command:
``x-last-value-per-topic = 'enabled'``

## Puting of element

You must provide key of element in routing_key options within putting operation.

## Examples of code

All examples of python's code for usage this queue (and tests) are available in test folder.


# Installation instructions

1. Clone the `rabbitmq-server` and build it.
    ```bash
    git clone https://github.com/rabbitmq/rabbitmq-server
    make
    ```
2. Clone this repo now under `deps` subdirectory
    ```
    cd rabbitmq-server/deps
    git clone https://github.com/kenetic-capital/rabbitmq-last-value-per-topic-queue.git
    ```
3. `make run-broker` (after taking a while) should print this plugin in the list of enabled plugins.
4. Stop the process. (`^C` and then `a` to abort).
5. Copy `plugins/rabbit_topic_last_value_queue-[version].ez` in to the plugins directory for your installation (usually `/usr/lib/rabbitmq/plugins/`)
6. You should now see the plugin when you do `rabbitmq-plugins` list and can enable/disable it using that tool

If you need any more info in installing plugins and/or troubleshooting then refer to:

* https://www.rabbitmq.com/plugins.html

* https://www.rabbitmq.com/community-plugins.html



# Testing

See test/TEST_PLAN.md


# Launching of test

1. ```make run-broker``` <- start RMQ
2. go to python folder and start ```consumer.py``` and ```producer.py```



# Authors

Sergey Loguntsov <loguntsov@gmail.com>

Mark Hingston <mark.hingston@kenetic.capital>
