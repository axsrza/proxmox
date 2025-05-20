
```
apk add build-base git
apk add linux-headers
```

```
git clone https://github.com/thierry-goubier/undervolt.git
```

```
cd undervolt
```

```
make
```

```
cp undervolt /usr/bin/
chmod +x /usr/bin/undervolt
```

```
git clone https://github.com/melma/c60-tweak.git
cd c60-tweak
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
