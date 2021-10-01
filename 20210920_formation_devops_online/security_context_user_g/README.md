






vagrant@kubemaster:~/devops/Devops/20210920_formation_devops_online/security_context_user_g$ k exec -ti --tty busy-pod bysunode1 -- sh
Defaulted container "busynode1" out of: busynode1, busynode2
$
$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
1000         1  0.0  0.0   4336   740 ?        Ss   10:35   0:00 /bin/sh -c sleep 600  ##
1000         6  0.0  0.0   4236   692 ?        S    10:35   0:00 sleep 600
1000        14  0.0  0.0   4336   712 pts/0    Ss   10:37   0:00 sh
1000        19  0.0  0.1  17500  2096 pts/0    R+   10:37   0:00 ps aux

Affichez les fonctionnalités du processus 1 :
bitmap for process 1:
cd /proc/1
cat status

...
CapPrm:	00000000a80425fb
CapEff:	00000000a80425fb
...




source
https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
