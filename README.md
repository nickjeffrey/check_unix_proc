# check_unix_proc
nagios check for running processes on UNIX-like operating systems (good for processor architectures that cannot run the built-in nagios check_procs binary)

# Requirements
perl, ssh  on nagios server

# Configuration

This script is executed remotely on a monitored system by the NRPE or check_by_ssh  methods available in nagios.

If you hare using the check_by_ssh method, you will need a section in the services.cfg file on the nagios server that looks similar to the following.
This assumes that you already have ssh key pairs configured.
```
    # Define service for checking running processes
    # If no thresholds are specified, default to min=1 max=1 owner=""
    define service{
       use                             generic-24x7-service
       host_name                       unix11
       service_description             process dhcpd
       check_command                   check_by_ssh!"/usr/local/nagios/libexec/check_unix_proc --min=1 --max=5 owner=root dhcpd"
       }
```

Alternatively, if you are using the NRPE method, you should have a section similar to the following in the services.cfg file:
```
    define service{
       use                             generic-24x7-service
       host_name                       unix11
       service_description             process dhcpd
       check_command                   check_nrpe!check_unix_proc!1!5!root!dhcpd
       }
```

If you are using the NRPE method, you will also need a command definition similar to the following on each monitored host in the /usr/local/nagios/nrpe/nrpe.cfg file:
```
    command[check_unix_proc]=/usr/local/nagios/libexec/check_unix_process --min=$ARG1$ --max=$ARG2$ --owner=$ARG3$ $ARG4$
```

Please note that the --owner switch is optional.  If you do not specify the --owner switch, the process will check for *any* owner.
 
If you need to check for a process name that contains embedded spaces, wrap the whole process with escaped quotes.  For example:
```
    check_command                   check_by_ssh!"/usr/local/nagios/libexec/check_unix_proc --min=1 --max=5 owner=root \"/path/to/procname -xyz -abc\""
```
