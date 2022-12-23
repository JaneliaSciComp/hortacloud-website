---
title: "Troubleshooting"
linkTitle: "Troubleshooting"
weight: 60
description: >
  Troubleshooting HortaCloud services
---

## No user can login to the workstation

We have seen this behavior if the "/data" volume ran out of space. You can check this by connecting to the EC2 instance running the JACS stack and running `df -h /data`. 

If the disk is full you, you can try to find anything that could be removed but if the limit was reached because the size your data you really have to resize the volume. The steps to resize an EBS volume can be found in the AWS documentation. First run [the instructions to increase the size of and EBS volume](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/requesting-ebs-volume-modifications.html) and then [the instructions that make the entire new disk space available to the EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/recognize-expanded-volume-linux.html)

If users still cannot use the workstation for tracing, after the `/data` volume was resized, and there is plenty of disk space available, the reason might be that RabbitMQ failed to start properly, which may happen if RabbitMQ runs out of disk space. Users can also inspect the log from the workstation and look for errors like:
```
java.lang.IllegalStateException: Message connection could not be opened to host <hostip> after <n> attempts
```
If this is case check the size of the file `/data/db/rabbitmq/jacs/mnesia/rabbit\@jacs-rabbit/msg_stores/vhosts/628WB79CIFDYO9LJI6DKMI09L/recovery.dets` If the file exists and its size is 0 then remove the file and restart the entire stack.