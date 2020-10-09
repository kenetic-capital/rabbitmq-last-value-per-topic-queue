
# Installation instructions

Easiest thing to do is to:
- make run-broker from above, check that you see "started with 1 plugins", then stop the process
- copy plugins/rabbit_topic_last_value_queue-[version].ez in to the plugins directory for your installation (usually /usr/lib/rabbitmq/plugins/)
- you should now see the plugin when you do rabbitmq-plugins list and can enable/disable it using that tool

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
