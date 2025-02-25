# Troubleshooting JetBrains Fleet

[Home](../index) | [Up](wsl-index)

I quite like Fleet as a general purpose editor. I do not like specialised IDEs as I tend to work in multiple lanaguages and need my own project layouts, so Fleet is a much better option. It is not as good as Emacs of course, but what could be. Working with WSL on W10 means Emacs is a bit problematic as the X support is very flaky in W10.

So I am trying to get Fleet working with my new RockyLinux WSL image. I keep getting this error: ``command '-d' 'RockyLinux-9-Base' 'ip' 'addr' 'show' 'eth0'" exits with 127``.

There is not much help in the Fleet user guide, so I had to look at some logs to find out what the problem was. The first thing was to find the logs. From the Fleet window click "Tools" (the 9 dots) -> Actions -> Collect Logs which creates a Zip bundle you can look at. This makes more sense:

```
[20230709 15:13:35.517 INFO  FR fleet.wsl.frontend.WslSupervisor] Opening wsl container: WslId(id=RockyLinux-9-Base)
[20230709 15:13:36.217 ERROR ?? fleet.controller.ProcessHolder] 
fleet.controller.ManagedProcessAbnormalExitException: Process [C:\Windows\System32\wsl.exe, -d, RockyLinux-9-Base, ip, addr, show, eth0] finished with exit code 127. Check log for details.
running wsl with args -d RockyLinux-9-Base ip addr show eth0
127
at fleet.controller/fleet.controller.ProcessHolder$start$3.invoke(FleetController.kt:105)
at fleet.controller/fleet.controller.ProcessHolder$start$3.invoke(FleetController.kt:102)
at fleet.controller/fleet.controller.ProcessHolder.start$lambda$2(FleetController.kt:102)
at java.base/java.util.concurrent.CompletableFuture.uniWhenComplete(Unknown Source)
at java.base/java.util.concurrent.CompletableFuture$UniWhenComplete.tryFire(Unknown Source)
at java.base/java.util.concurrent.CompletableFuture.postComplete(Unknown Source)
at java.base/java.util.concurrent.CompletableFuture.postFire(Unknown Source)
at java.base/java.util.concurrent.CompletableFuture$UniHandle.tryFire(Unknown Source)
at java.base/java.util.concurrent.CompletableFuture$Completion.exec(Unknown Source)
at java.base/java.util.concurrent.ForkJoinTask.doExec(Unknown Source)
at java.base/java.util.concurrent.ForkJoinPool$WorkQueue.topLevelExec(Unknown Source)
at java.base/java.util.concurrent.ForkJoinPool.scan(Unknown Source)
at java.base/java.util.concurrent.ForkJoinPool.runWorker(Unknown Source)
at java.base/java.util.concurrent.ForkJoinWorkerThread.run(Unknown Source)
[20230709 15:13:36.228 ERROR FR fleet.wsl.frontend.WslSupervisor] "wsl.exe '-d' 'RockyLinux-9-Base' 'ip' 'addr' 'show' 'eth0'" exits with 127
stdout: 
stderr: /bin/bash: line 1: ip: command not found
```

Conclusion - Fleet uses WSL to run some commands inside the image, and this one is failing because the command it wants does no exist! The solution is simple: ``sudo dnf install iproute`` will install the "ip" command. The Rocky image I am using is pretty minimal so I am not surprised a package the comes with CentOS by default is not a default package for Rocky.


---
[Home](../index) | [Up](wsl-index)
