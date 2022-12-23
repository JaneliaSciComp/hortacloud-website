---
title: "Troubleshooting"
linkTitle: "Troubleshooting"
weight: 40
description: >
  Troubleshooting HortaCloud services
---

## No user can login to the workstation

We have seen this behavior if the "/data" volume ran out of space. You can check this by connecting to the EC2 instance running the JACS stack and running `df -h /data`. 

If the disk is full you, you can try to find anything that could be removed but if the limit was reached because the size your data you really have to resize the volume. To resize the volume follow the instructions from AWS to [increase the size of the volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/requesting-ebs-volume-modifications.html) and then the [instructions to make the entire new volume available](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html)

If users still cannot use the workstation after the `/data` volume was resized and they see errors like:
```
java.lang.IllegalStateException: Message connection could not be opened to host <hostip> after <n> attempts
```
rabbitmq which failed to restart properly. Sometimes RabbitMQ may crash if it runs out of disk space. You can check this by looking at the size of `/data/db/rabbitmq/jacs/mnesia/rabbit\@jacs-rabbit/msg_stores/vhosts/628WB79CIFDYO9LJI6DKMI09L/recovery.dets` If the size is 0 then remove the file and restart the entire stack.