
TASK-1 => Setup collectd to monitor various metrics for operating system and one of the following datastores ES/RMQ/AS.

TASK-2 => Setup influxdb on a vm.

TASK-3 => Send collectd metrics to influxdb

TASK-4 => Plot these metrics on a grafana dashboards

TASK-5 => Setup riemann on a vm

TASK-6 => Write a script to collect the metrics previously collected by collectd and send it to riemann

TASK-7 => The metrics should go to influxdb from riemann

TASK-8 => metrics such as disk usage/ram usage/HWM etc should have a threshold (80%) and should send a critical alert to riemann once the threshold is breach

TASK-9 => NAGIOS.
