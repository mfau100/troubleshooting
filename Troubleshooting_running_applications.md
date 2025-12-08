### Troubleshooting running applications.

#### Gives an overview of cluster-level events.

oc get events

#### For an overview on a specific resource

oc describe <resource>

#### The go to resource with errors in the running app is the pod.

oc describe pod <podname>

#### But you can also look at other related resources.

oc describe <storage> 
oc describe <network>

#### Sometimes "oc logs" doesn't show the issue.
#### It should be the 1st thing you look at, sine "oc logs" connects to the standardout of the application.

oc logs <podname>

#### But if standard out isn't showing anything useful, try "oc exec".
#### But the options are often limited, common utilities like ps and ip are not installed, it depends on the container, but it doesn't hurt to try.

oc exec -it <podname> -- /bin/bash

#### If there's not bash use sh, which will always be there.

oc exec -it <podname> -- sh

#### You can add essential binaries in a dockerfiles you create.  For access to ip and ps.
#### Remember any container that is started as a rootless container is not addressible by any ip address.

yum install -y iputils procps-ng

#### Alternatively, consider bing-mounting the host bin directory while starting the contaier.  It will run the container with an interactive shell and access to all utilities in /bin on the host.
#### This works in podman, but not easy in openshift.

sudo podman run -it -v /bin:/bin nginx /bin/bash

#### Transfer file to and from Containers.

podman cp
oc cp

#### For example.

sudo podman cp my.conf mycontainer:/opt/myapp/
oc cp mypod:/opt/myapp/my.conf .

#### You can also mount volumes in a container.
..................................
oc new-project tammy
oc run nginx --image=bitnami/nginx
oc get pod
oc describe pod nginx
oc new-project tammy
10008  2025-12-07 20:43:13 oc run nginx --image=bitnami/nginx
10009  2025-12-07 20:43:16 oc get all
10010  2025-12-07 20:43:19 oc get pod
10011  2025-12-07 20:48:06 clear
10012  2025-12-07 20:48:23 oc describe pod nginx
# Had to remove the taint to get the pod to run.
[mfau@mfau-thinkpadx1carbon5th troubleshooting]$ oc get nodes
NAME   STATUS   ROLES                         AGE    VERSION
crc    Ready    control-plane,master,worker   266d   v1.31.6
[mfau@mfau-thinkpadx1carbon5th troubleshooting]$ oc adm taint nodes crc node.kubernetes.io/disk-pressure:NoSchedule-
node/crc untainted

# Now back to the troubleshooting since the pod is running.

[mfau@mfau-thinkpadx1carbon5th troubleshooting]$ oc get po
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          6m36s

# Logging to use the sh shell.

[mfau@mfau-thinkpadx1carbon5th troubleshooting]$ oc exec -it nginx -- sh

# The ps is there, but not the ip command.

sh-5.2$ ps
    PID TTY          TIME CMD
     45 pts/0    00:00:00 sh
     46 pts/0    00:00:00 ps
sh-5.2$ ip
sh: ip: command not found
sh-5.2$

# SVV tip - Many containers don't have the ps utility, but if you go to the /proc directory you'll see the pid directories.
# All of the pid directories have a file with cmdline and this line contains the name of the process that started this pid.
# This is an alternative if ps is available.
# The "oc exec" on a running container does not override the entrypoint, so it's safe to exit with stopping the container.
# If you started a container with a shell instead of the default entrypoing and you want to keep it running, then you control+p, control+q to detach from the container and to keep it running.

[mfau@mfau-thinkpadx1carbon5th ~]$ cd /proc
[mfau@mfau-thinkpadx1carbon5th proc]$ ls
1     1051  107   1175   1380   1570   19477  22361  2380   2419   2511   25777  2611  2677  2726  2880  3000  3252  3516  3944  4239  475   57   65   661  74   84   906  981         crypto         irq            mdstat        scsi           uptime
10    1053  1070  1178   1381   1591   1982   22515  2398   2424   25211  25778  2617  2678  2727  2888  3032  3267  36    3945  43    476   58   651  662  75   85   907  982         devices        kallsyms       meminfo       self           version
100   1054  1080  12780  1463   16     2      22684  24     2481   2522   25779  2618  2693  2729  2893  3055  3292  37    4     436   477   59   652  663  76   86   908  9927        diskstats      kcore          misc          slabinfo       vmallocinfo
1003  1058  1086  12781  1487   17     20     23     24002  2482   25226  25781  2620  2695  2770  29    31    33    3717  40    437   48    6    653  664  766  87   909  acpi        dma            keys           modules       softirqs       vmstat
1008  106   1089  12782  1493   18     20334  230    24004  2483   25227  25786  2637  27    2776  2905  3100  3320  3752  4082  438   49    60   654  665  77   88   91   asound      driver         key-users      mounts        stat           zoneinfo
1009  1061  1102  12783  1495   18997  20483  23088  241    24860  25247  25787  2639  2703  28    2925  3125  3374  3778  41    439   5     61   655  67   78   886  910  bootconfig  dynamic_debug  kmsg           mtd           swaps
1012  1062  112   13     15     19     2077   23089  2412   2487   25404  25788  2644  2705  2801  2932  3126  34    3839  4112  45    51    617  656  68   79   887  911  buddyinfo   execdomains    kpagecgroup    mtrr          sys
102   1063  113   1302   1502   190    20953  23090  24168  2489   25468  25790  2654  2711  2802  2962  3175  3409  3840  42    46    52    618  657  69   8    89   912  bus         filesystems    kpagecount     net           sysrq-trigger
103   1064  1136  1303   1503   1901   21     2334   24170  2495   2560   25936  2663  2714  2818  2963  3178  3432  3842  4211  461   53    619  658  7    802  894  93   cgroups     fs             kpageflags     pagetypeinfo  sysvipc
104   1066  1139  1304   15160  191    21581  235    24171  25     25623  25937  2669  2715  2824  2973  3194  3461  39    4217  465   54    621  659  70   814  903  95   cmdline     interrupts     latency_stats  partitions    thread-self
1047  1067  115   1352   1519   193    22     2379   24172  25059  25624  25938  2670  2720  2826  3     3200  35    3906  4219  47    55    63   66   72   815  904  958  consoles    iomem          loadavg        pressure      timer_list
105   1069  1174  1353   1520   19308  22050  238    2418   2509   25776  25940  2671  2725  2830  30    3225  3507  3940  4222  474   5625  64   660  73   83   905  96   cpuinfo     ioports        locks          schedstat     tty


[mfau@mfau-thinkpadx1carbon5th proc]$ sudo ls 1
[sudo] password for mfau: 
arch_status  auxv        cmdline          cpu_resctrl_groups  exe     gid_map            ksm_stat  loginuid   mem        mountstats  numa_maps  oom_score_adj  personality  sched      setgroups     stack  status   timens_offsets  uid_map
attr         cgroup      comm             cwd                 fd      io                 latency   map_files  mountinfo  net         oom_adj    pagemap        projid_map   schedstat  smaps         stat   syscall  timers          wchan
autogroup    clear_refs  coredump_filter  environ             fdinfo  ksm_merging_pages  limits    maps       mounts     ns          oom_score  patch_state    root         sessionid  smaps_rollup  statm  task     timerslack_ns
[mfau@mfau-thinkpadx1carbon5th proc]$ cat 1/cmdline
/usr/lib/systemd/systemd--switched-root--system--deserialize=53rhgb[mfau@mfau-thinkpadx1carbon5th proc]$ 
