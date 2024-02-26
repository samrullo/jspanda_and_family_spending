After sometime my docker containers start consuming a lot of memory and I can't access my sites
To resolve that 

1. Stop all dockers after navigating to ```~/thevolume/jspanda_and_family_spending```
```bash
docker-compose down
```

2. Start all containers
```bash
docker-compose up
```

3. Unfortunately you will have to reinstall all python libraries by logging into each container with ```jspanda_bash``` and ```familyspendingbash```

Also ```jspanda_bash``` will give you hard time first time installing ```uwsgi```. refer to [uwsgi install solve](uwsgi_install_fails.md)

```bash
pip install -r requirements
```

4. Finally start services

```bash
systemctl start jspanda
```

