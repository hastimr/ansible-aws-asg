AWS ASG  [![Circle CI](https://circleci.com/gh/VivaReal/ansible-aws-asg/tree/master.svg?style=svg&circle-token=39ff1e2f2d1e1e9fc23cca130dca267e0c3348a1)](https://circleci.com/gh/VivaReal/ansible-aws-asg/tree/master)
=========

This ansible role manage AWS asg.
When ec2_load_balancer are defined, the asg will attach elb.

Requirements
------------

- Ansible 2.0.1 or higher.
- Tested on Ubuntu 14.04 and Amazon 7

Role Variables
--------------

| parameter             | required | default | choices | comments |
| --------------------- | -------- | ------- | -------- |-------- |
| ec2_find_ami_name                   |  no     |         || ami name to find. ex. ubuntu-docker-base-ami-1* |
| ec2_assign_public_ip                   |   no    |    yes     |yes, no|  when provisioning within vpc, assign a public IP address. |
| ec2_count                   |  no     |     1    || number of instances to launch|
| ec2_sg_id                   |   yes    |         || security group id (or list of ids) to use with the instance |
| ec2_instance_type                   |   no    |     t2.micro    || instance type to use for the instance, see http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html|
| ec2_ebs_optimized  | no  |  false ||  whether instance is using optimized EBS volumes, see http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSOptimized.html|
| ec2_instance_profile_name |  no |   ||  Name of the IAM instance profile to use. Recommended to use aws-elasticbeanstalk-ec2-role |
| ec2_base_image  |  yes |   ||  ami ID to use for the instance. Only use when ec2_find_ami_name is blank. |
| ec2_key_name | yes  |   |   |key pair to use on the instance|
| ec2_monitoring  |  no | yes  | yes, no |  enable detailed monitoring (CloudWatch) for instance |
| ec2_load_balancer | no | | | List of ELB names to use for the group |
| ec2_launch_config_name | yes | | | Name of the Launch configuration to use for the group. See the ec2_lc module for managing these. |
| ec2_asg_subnets | yes | | | List of VPC subnets to use|
| aws_asg_desired_capacity | no | 1 | | Desired number of instances in group, if unspecified then the current group value will be used.|
| aws_asg_min_size | no | 1 | | Minimum number of instances in group, if unspecified then the current group value will be used.|
| aws_asg_max_size | no | 1 | | Maximum number of instances in group, if unspecified then the current group value will be used.|
| asg_health_check_period | no | 360 | | |
| aws_asg_health_check_type | no | default elb when ec2_load_balancer is defined / default ec2 when ec2_load_balancer is not defined | | The service you want the health status from, Amazon EC2 or Elastic Load Balancer.|
| aws_resource_tags  | yes  |   | | a hash/dictionary of tags to add to the new instance or for starting/stopping instance by tag; '{"key":"value"}' and '{"VREnv":"PROD","VRProject":"sample","VRTeam":"infra", "Name":"ami name"}' |
| aws_wait_timeout | no | 600 | |  how long before wait gives up, in seconds |
| vivareal_project_build | yes | | |  ASG name |
| wait_for_instances | no  | True | | Wait for the ASG instances to be in a ready state before exiting. If instances are behind an ELB, it will wait until the ELB determines all instances have a lifecycle_state of "InService" and a health_status of "Healthy".|  
| state |  no |  present |present, absent,running, stopped| create or terminate instances  |
| region |  yes |   || The AWS region to use. Must be specified if ec2_url is not used. If not specified then the value of the EC2_REGION environment variable, if any, is used. See http://docs.aws.amazon.com/general/latest/gr/rande.html#ec2_region  |
| ec2_metric_alarm_metric_low_name | no | <Auto Scaling Group Name>-CPU-LOW | | The name of the cloudwatch alarm that will be created |
| ec2_metric_alarm_low_metric | no | CPUUtilization | | What metric cloudwatch will monitor (Ref: http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CW_Support_For_AWS.html) |
| ec2_metric_alarm_low_namespace | no | AWS/EC2 | | CloudWatch namespaces are containers for metrics. Metrics in different namespaces are isolated from each other|
| ec2_metric_alarm_low_statistic | no | Avarage | Minimum, Maximum, Sum, Average, SampleCount| Statistics are metric data aggregations over specified periods of time. |
| ec2_metric_alarm_low_operator| no | <= | =, >, <, >=, <=| The arithmetic operation to use when comparing the specified Statistic and Threshold. The specified Statistic value is used as the first operand.|
| ec2_metric_alarm_low_trigger| no | 30 | | Sets the min/max bound for triggering the alarm.|
| ec2_metric_alarm_low_interval| no | 300 | | The time (in seconds) between metric evaluations.|
| ec2_metric_alarm_low_evaluation_periods| no | 3 | The number of times in which the metric is evaluated before final calculation. |
| ec2_metric_alarm_low_unit| no | Percent | Seconds, Bytes, Bits, Percent, Count,  Bytes/Second, Bits/Second, Count/Second, None | The threshold's unit of measurement. |
| ec2_metric_alarm_low_description | no | "Scale down ASG if CPUUtilization is <= 30" | |
| ec2_metric_alarm_low_dimensions | no | {"AutoScalingGroupName": "<Auto Scaling Group Name>"} | | Describes to what the alarm is applied|
| ec2_metric_alarm_metric_high_name | no | <Auto Scaling Group Name>-CPU-HIGH | | The name of the cloudwatch alarm that will be created |
| ec2_metric_alarm_high_metric | no | CPUUtilization | | What metric cloudwatch will monitor (Ref: http://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/CW_Support_For_AWS.html) |
| ec2_metric_alarm_high_namespace | no | AWS/EC2 | | CloudWatch namespaces are containers for metrics. Metrics in different namespaces are isolated from each other|
| ec2_metric_alarm_high_statistic | no | Avarage | Minimum, Maximum, Sum, Average, SampleCount| Statistics are metric data aggregations over specified periods of time. |
| ec2_metric_alarm_high_operator | no | >= | =, >, <, >=, <=| The arithmetic operation to use when comparing the specified Statistic and Threshold. The specified Statistic value is used as the first operand.|
| ec2_metric_alarm_high_trigger| no | 70 | | Sets the min/max bound for triggering the alarm.|
| ec2_metric_alarm_high_interval| no | 300 | | The time (in seconds) between metric evaluations.|
| ec2_metric_alarm_high_evaluation_periods | no | 3 | The number of times in which the metric is evaluated before final calculation. |
| ec2_metric_alarm_high_unit | no | Percent | Seconds, Bytes, Bits, Percent, Count,  Bytes/Second, Bits/Second, Count/Second, None | The threshold's unit of measurement. |
| ec2_metric_alarm_high_description | no | "Scale UP ASG if CPUUtilization is >= 70" | |
| ec2_metric_alarm_high_dimensions | no | {"AutoScalingGroupName": "<Auto Scaling Group Name>"} | | Describes to what the alarm is applied|

Output variables
--------------
    ec2_ami_instance_id: launched instance id
    ec2_launched: registered host group



Example Playbook
----------------


    - hosts: localhost
      vars:
        aws_resource_tags: {
         'Name': 'instance-name',
         'VREnv': 'PROD',
         'VRProject': 'infra-ansible',
         'VRTeam': 'infra',
         'zabbix-metadata': 'if-ansible-sample'
        }
        region: us-east-1
        ec2_sg_id: ['sg-32f1634b']
        ec2_vpc_id: vpc-202e7845
        ec2_key_name: master_virginia
        ec2_assign_public_ip: yes
        ec2_find_ami_name: "ubuntu-docker-base-ami-1*"
        ec2_instance_type: t2.small
        ec2_instance_profile_name: aws-elasticbeanstalk-ec2-role
        ec2_base_image: ami-8fcee4e5
      roles:
        - { role: aws-ec2-provisioning }

Ansible modules
--------------
[ec2](http://docs.ansible.com/ansible/ec2_module.html)

[ec2_ami_find](http://docs.ansible.com/ansible/ec2_ami_find_module.html)


License
-------

BSD

Authors:
------------------

Giancarlo Rubio (<gianrubio@gmail.com>)
Wilson Hipolito (<wilson.hipolito@vivareal.com>)
