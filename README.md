# hessenwiki

The `hessenwiki` is the internal collaboration and documentation tool for the BdP Landesverband Hessen e.V.
This Repository contains documentation and configuration files needed to set up a new instance of the confluence service.

If you want to run your own instance using this documentation please make sure to replace all hardcoded instances of the hessenwiki URL (`www.hessenwiki.de`) and its derivatives.

## Commands

### Start

```bash
sudo docker compose up -d
```

### Stop

```bash
sudo docker compose down
```

### Find out what executable uses port 80

```bash
sudo netstat -tulpn | grep :80
```

## Backup

1. Stop the confluence service.

```bash
sudo docker compose down
```

2. Create a copy of the confluence directory.

```bash
sudo cp -R -a /home/hessenwiki/hessenwiki/confluence/. /home/hessenwiki/hessenwiki/confluence-backup
```

3. Make a backup of the Database.

```bash
sudo docker compose exec -it postgresql pg_dump -U hessenwiki -d confluence > /home/hessenwiki/confluence_backup/dump_`date +%Y-%m%d_%H-%M-%S`.sql
```

4. Restart confluence.

```bash
sudo docker compose up -d
```

5.  Transfer the files to a secure backup location.

    1. Optionally compress the backup

```bash
    tar -zcvf confluence-backup_`date +%Y-%m%d*%H-%M-%S`.tar.gz /home/hessenwiki/hessenwiki/confluence-backup
```

## Restore from Backup

1. Make sure you have both the database dump and the confluence folder
2. Copy the confluence backup to the correct location specified in the `compose.yaml`
   1. If you've compressed the file beforehand you need to unzip it

```bash
# Replace $date with the correct date from the target backup
tar -xvzf confluence-backup.tar.gz -C /home/hessenwiki/hessenwiki/confluence
```

3. Create a new postgres-container and restore the dump

```bash
cat dump.sql | docker compose exec -i postgresql psql -d confluence -U hessenwiki
```
