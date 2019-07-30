
# Ubuntu 18.04 + nginx + pgadmin4 (server mode + reverse proxy)
![pgadmin](https://github.com/rbernardes/nginx-modules/blob/master/nginx_logo.png?raw=true)

### Install required packages:
```
apt install build-essential libssl-dev libffi-dev virtualenv python-pip libpq-dev python-dev
```

### Create directories/permissions:
```
mkdir -p /var/lib/pgadmin4/sessions
mkdir /var/lib/pgadmin4/storage
mkdir /var/log/pgadmin4
chown -R rbernardes.rbernardes /var/lib/pgadmin4/
chown -R rbernardes.rbernardes /var/log/pgadmin4/
```

### Enable virtualenv and install pgadmin (at this time 4.4.11):
```
virtualenv .pgadmin4
cd .pgadmin4/
source bin/activate

wget https://ftp.postgresql.org/pub/pgadmin/pgadmin4/v4.11/pip/pgadmin4-4.11-py2.py3-none-any.whl
pip install pgadmin4-4.11-py2.py3-none-any.whl
```

### Configure server environment:
```
vi nano ~/.pgadmin4/lib/python2.7/site-packages/pgadmin4/config_local.py

---
DEFAULT_SERVER = '0.0.0.0'
DEFAULT_SERVER_PORT = 5050
LOG_FILE = '/var/log/pgadmin4/pgadmin4.log'
SQLITE_PATH = '/var/lib/pgadmin4/pgadmin4.db'
SESSION_DB_PATH = '/var/lib/pgadmin4/sessions'
STORAGE_DIR = '/var/lib/pgadmin4/storage'
SERVER_MODE = True
---
```

### Setup pgadmin4:
```
python lib/python2.7/site-packages/pgadmin4/setup.py

---
NOTE: Configuring authentication for SERVER mode.

Enter the email address and password to use for the initial pgAdmin user account:

Email address: youremail@address
Password:
Retype password:
pgAdmin 4 - Application Initialisation
======================================
---
```

### Change permission to nginx access:
```
chown -R www-data:www-data /var/lib/pgadmin4/
sudo chown -R www-data:www-data /var/log/pgadmin4/
```

### Create script to start pgadmin:
```
vi /usr/local/bin/pgadmin.sh


#!/bin/bash
. /home/rbernardes/.pgadmin4/bin/activate
# virtualenv is now active.
#
nohup python /home/rbernardes/.pgadmin4/lib/python2.7/site-packages/pgadmin4/pgAdmin4.py &

[SHIFT + zz to save file]
chmod +x /usr/local/bin/pgadmin.sh
```

### Run script:
```
[~/.pgadmin4]: pgadmin.sh
[~/.pgadmin4]: nohup: appending output to 'nohup.out'
```

### Check:
```
[~/.pgadmin4]: netstat -anlp |grep 5050
tcp        0      0 0.0.0.0:5050            0.0.0.0:*               LISTEN      27749/python
```

### Configure nginx domain (attached)
