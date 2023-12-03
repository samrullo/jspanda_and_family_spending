1. Connect to jspanda postgres container. 
```bash
docker exec -it jspanda_postgres bash
```

Connect postgres database
```bash
psql -U postgres postgres
```

Stop ```jspanda``` system service running on ```jspanda``` container

```bash
$systemctl stop jspanda
```

Remove ```jspanda``` database

```bash
DROP DATABASE jspanda;
```

Navigate to where db dump file is ```/var/dump/```

Recreate jspanda database

```bash
CREATE DATABASE jspanda;
```

Restore from dumped files

```bash
psql -U postgres -d jspanda -f jspanda_dump_file.sql
```

Restart ```jspanda``` service on jspanda container

```bash
$systemctl start jspanda
```