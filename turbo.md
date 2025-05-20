
```
apk add build-base
apk add linux-headers
```

```
wget https://sourceforge.net/projects/undervolt/files/undervolt/0.4/undervolt-0.4.tgz/download -O undervolt-0.4.tgz
```

```
tar xzf undervolt-0.4.tgz
cd undervolt-0.4
```

```
make
```

```
cp undervolt /usr/bin/
chmod +x /usr/bin/undervolt
```

```
wget https://github.com/melma/c60-tweak/archive/refs/heads/master.zip -O c60-tweak.zip
unzip c60-tweak.zip
```

```
cd c60-tweak-master
chmod +x c60-tweak.sh
```

```
./c60-tweak.sh
```

```
nano /etc/local.d/c60-tweak.start
```

Conteúdo:
```
#!/bin/sh
/usr/bin/undervolt -p0 1050 -p1 1050 -p2 850 && echo "Turbo unlocked"
```

```
chmod +x /etc/local.d/c60-tweak.start
```

```
rc-update add local default
```
