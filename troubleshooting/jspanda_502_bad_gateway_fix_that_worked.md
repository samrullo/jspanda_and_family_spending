# When jspanda stops with 502 bad gateway error following fix worked

Log into ```jspanda``` container

```bash
jspanda_bash
```

Enable ```jspanda``` service

```bash
systemctl enable jspanda
```

Start ```jspanda``` service

```bash
systemctl start jspanda
```

Above should start ```jspanda``` uwsgi service which routes requests from Nginx web server to Flask python application.