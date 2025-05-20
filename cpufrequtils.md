
```
apk add cpufrequtils
```

```
cpufreq-info
```

```
nano /etc/local.d/cpufreq.start
```

Conteúdo:
```
#!/bin/sh
for cpu in /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor; do
    echo ondemand > "$cpu"
done
```

```
chmod +x /etc/local.d/cpufreq.start
```

```
rc-update add local default
```

```
rc-update show | grep local
```
