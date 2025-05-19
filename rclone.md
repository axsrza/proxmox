```
apk add unzip
curl https://rclone.org/install.sh | bash
```

```
rclone config
```

op:01
```
docker run -d \
  --network host \
  --name firefox \
  jlesage/firefox
```

op:02
```
docker run -d \
  --name firefox \
  --network host \
  linuxserver/firefox
```

```
rclone copy /home/music gdrive:/home/music --progress
```
