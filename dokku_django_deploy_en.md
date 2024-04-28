# Deploying Django Applications using Dokku

This is a step-by-step guide on how to deploy a Django application on Dokku.

## Initial Server Configurations

1.Package Update

```
sudo apt update && sudo apt upgrade -y
```

2.Generate SSH Key

```
ssh-keygen -t rsa
```

3.Copy the entire contents of the public key to the `authorized_key` file

```
cat ~/.ssh/id_rsa.pub
vi ~/.ssh/authorized_keys
```

4.Do the same for the `root` user (directory: `/root.ssh/`)

5.Adjust all key permissions to 600:

```
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_rsa
```

6.Download Dokku (check for the latest version):

```
wget -NP . https://dokku.com/install/v0.34.4/bootstrap.sh
```

7.Install Dokku:

```
sudo DOKKU_TAG=v0.34.4 bash bootstrap.sh
```

8.Check the installed version:

```
dokku version
```

9.Configure a SSH Key:

```
cat ~/.ssh/authorized_keys | sudo dokku ssh-keys:add admin
```

10.Check the configured keys on the server:

```
sudo dokku ssh-keys:list
```

11.Install Postgres plugin:

```
sudo dokku plugin:install https://github.com/dokku/dokku-postgres.git
```

12.Install Letsencrypt plugin:

```
sudo dokku plugin:install https://github.com/dokku/dokku-letsencrypt.git
```

## Configurations on the Local Computer

1.Copy the contents of the server's private key:

On the server:

```
cat ~/.ssh/id_rsa
```

On the local computer:

```
vim ~/.ssh/authorized_keys
```

2.Change permissions:

```
chmod 600 ~/.ssh/authorized_keys
chmod 600 ~/.ssh/id_rsa
```

3.Key configuration:

```
ssh-add -l
```

If the agent is not started, start it with:

```
eval "$(ssh-agent -s)"
```

If no keys are loaded, add them with:

```
ssh-add ~/.ssh/your_private_key
```

4.Test SSH access:

```
ssh -o StrictHostKeyChecking=no <key_user_name>@<server_ip_address> whoami
```

Output: `<key_user_name>`

**In summary, the public key of the local computer must be in the server's authorized_keys. At the end of the key is the username. On the local machine, the ssh-agent must be configured with the command ssh-add ~/.ssh/private_key from the previous step. This private key is the same one that was generated with the public key.**

## Creating an Application in Dokku

1.Create application:

```
dokku apps:create <app_name>
```

Check if the creation was successful:

```
dokku apps:list
```

2.Domain configuration:

To configure a local domain:

```
dokku domains:set-global <domain.com>
```

To configure a domain for the application:

```
dokku domains:add <app_name> <server_ip_or_full_domain_name>
```

Check the configuration in NGINX:

```
dokku nginx:show-config <my_app>
```

Check the application's domain configuration:

```
dokku domains:report <my_app>
```

3.Create key for application domain SSL:

```
dokku letsencrypt:enable <my_app>
```

4.Create a Postgres database for the application:

```
dokku postgres:create <mydb>
```

Check if the database container has started:

```
sudo docker ps
```

5.Link the database to the application:

```
dokku postgres:link <mydb> <app_name>
```

## Configuration in a Django Application

1.Add to requirements.txt:

- python-decouple
- dj-database-url
- gunicorn
- psycopg2-binary
- django-storages\*

**\*Added for the application to work with static files on aws or other services. Adjust as needed for the application**

2.Configure environment variables (`.env`):

```
ALLOWED_HOSTS=*
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_STORAGE_BUCKET_NAME=
DATABASE_URL=
DEBUG=False
DJANGO_SETTINGS_MODULE=<app_name>.settings
DJANGO_SUPERUSER_EMAIL=
DJANGO_SUPERUSER_PASSWORD=
DJANGO_SUPERUSER_USERNAME=
SECRET_KEY=
USE_AWS=False
```

---

**Configure application environment variable on the SERVER:**:

```
dokku config:set --no-restart <my_app> <ENVIRONMENT_VARIABLE>=<VARIABLE_VALUE>
```

---

3.Create `Procfile`:

```
web: gunicorn <app_name>.wsgi --log-file -
```

4.Create `runtime.txt`:

```
python-3.12.3
```

5.Configurations in the `settings.py` file:

```
from decouple import config, Csv
from dj_database_url import parse as dburl

SECRET_KEY = config('SECRET_KEY')

DEBUG = config('DEBUG', default=False, cast=bool)

ALLOWED_HOSTS = config('ALLOWED_HOSTS', default='*', cast=Csv())

default_dburl = 'sqlite:///' + str(BASE_DIR / 'db.sqlite3')
DATABASES = {
    'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
}

if config('USE_AWS', default=False, cast=bool):
    AWS_ACCESS_KEY_ID = config('AWS_ACCESS_KEY_ID')
    AWS_SECRET_ACCESS_KEY = config('AWS_SECRET_ACCESS_KEY')
    AWS_STORAGE_BUCKET_NAME = config('AWS_STORAGE_BUCKET_NAME')
    AWS_S3_REGION_NAME = 'us-east-2'
    AWS_S3_CUSTOM_DOMAIN = f'{AWS_STORAGE_BUCKET_NAME}.s3.amazonaws.com'
    AWS_DEFAULT_ACL = 'public-read'
    AWS_QUERYSTRING_AUTH = False
    AWS_S3_OBJECT_PARAMETERS = {'CacheControl': 'max-age=86400'}
    MEDIA_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/'
    DEFAULT_FILE_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
    STATIC_URL = f'https://{AWS_S3_CUSTOM_DOMAIN}/static/'
    STATICFILES_STORAGE = 'storages.backends.s3boto3.S3Boto3Storage'
else:
    STATIC_URL = 'static/'
    STATIC_ROOT = str(BASE_DIR / 'staticfiles')

    MEDIA_URL = '/media/'
    MEDIA_ROOT = str(BASE_DIR / 'media')
```

## Prepare Project to Send to Dokku:

1.Git:

```
git add .
git commit -m "app prepared for deployment"
```

2.Configure application environment variable on the SERVER:

```
dokku config:set --no-restart <my_app> <ENVIRONMENT_VARIABLE>=<VARIABLE_VALUE>
```

Check environment variables configured in the application:

```
dokku config:show <my_app>
```

3.Add the Dokku remote repository to git:

```
git remote add dokku dokku@<your_domain>:<my_app>
git checkuot -b main
git push dokku main
```

**Note: When configuring the git remote, after the @ it must contain your valid domain (the one that hits the server) or the server's IP address, as access will be via SSH. After the ":", use the application name as it was configured in Dokku.**

4.After deployment, it is necessary to give the commands to migrate the database:

```
dokku run <my_app> python manage.py migrate
```

## Configure Application Storage

1.To persist data from an application container, we first need to create a storage directory. Dokku documentation recommends using `/var/lib/dokku/data/storage/<app_name>`:

```
mkdir /var/lib/dokku/data/storage/<my_app>/
chown -R dokku:dokku /var/lib/dokku/data/storage/<my_app>/
sudo chmod -R 777 /var/lib/dokku/data/storage/<my_app>/
```

2.Next we need to map/mount the container directories to the created directory. The command needs 2 arguments:

- Application name
- server-directory-path:container-directory-path

```
dokku storage:mount <my_app> /var/lib/dokku/data/storage/<my_app>/staticfiles:/app/staticfiles
dokku storage:mount <my_app> /var/lib/dokku/data/storage/<my_app>/media:/app/media
sudo chmod -R 777 /var/lib/dokku/data/storage/<my_app>/staticfiles/
sudo chmod -R 777 /var/lib/dokku/data/storage/<my_app>/media/
```

**Adjust names as needed.**

3.Redeploy to make sure the files will be collected:

```
dokku ps:restart <my_app>
```

List directories:

```
ls /var/lib/dokku/data/storage/<my_app>
```

Output: `media  staticfiles`

4.Configure django to serve media and staticfiles:

At the end of the `urls.py` file:

```
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

5.To configure NGINX to serve the files in the mapped folders, you will need to create a directory called `nginx.conf.d` inside the `/home/dokku/<my_app>/` directory:

```
mkdir -p /home/dokku/<my_app>/nginx.conf.d
chown dokku:dokku /home/dokku/<my_app>/nginx.conf.d/
```

6.After, create the file `static.conf` inside the directory created earlier:

```
vim /home/dokku/<my_app>/nginx.conf.d/static.conf
```

Paste the content into the `static.conf` file:

```
location /static/ {
    alias /var/lib/dokku/data/storage/<my_app>/staticfiles/;
}

location /media/ {
    alias /var/lib/dokku/data/storage/<my_app>/media/;
}
```

Confirm the permissions of the `static.conf` file:

```
chown -R dokku:dokku /home/dokku/<my_app>/nginx.conf.d/static.conf
```

7.Restart the application for the settings to take effect:

```
dokku ps:restart <my_app>
```

## Backup Database to AWS

1.Install awscli:

```
sudo apt update
sudo apt install awscli
```

2.Configure aws credentials. You will be asked for:

- AWS Access Key ID
- AWS Secret Access Key
- Default region name (can be left blank)
- Default output format (can be left blank)

```
aws configure
```

3.Create a backup script. Create the file `backup_db.sh` and put the content below:

```
#!/bin/bash

# Date to name the backup file
DATE=$(date +"%Y-%m-%d_%H-%M-%S")

# Path to save backups
BACKUP_DIR="/var/backups/postgres"

# Database service name
DB_SERVICE="service_name"

# Full path of the backup file
BACKUP_FILE="${BACKUP_DIR}/backup_${DB_SERVICE}_${DATE}.dump"

# Create backup directory if it doesn't exist
mkdir -p ${BACKUP_DIR}

# Run backup
dokku postgres:export ${DB_SERVICE} > ${BACKUP_FILE}

# Transfer to S3
aws s3 cp ${BACKUP_FILE} s3://your-s3-bucket/path/to/backup/

# Optional: Clean up old backups locally
find ${BACKUP_DIR} -type f -mtime +30 -name '*.dump' -delete
```

**Adjust directory names.**

4.Grant execution permission to the script:

```
chmod +x /home/dokku/scripts/backup_db.sh
```

5.Test the script:

```
/home/dokku/scripts/backup_db.sh
```

6.Automate script execution with `Cron`:

```
crontab -e
```

Add the line below to make the backup every day at 00:00. Adjust as needed:

```
0 0 * * * /home/dokku/scripts/backup_db.sh >> /var/log/backup_db.log 2>&1
```

7.Change file and directory permissions:

```
sudo chmod -R 777 /var/backups/postgres
```

**To avoid permission problems, you can use `sudo` to run `crontab -e` so that it is executed by root.**

## Restore Database

1.Import dump file to database service:

```
dokku postgres:import <database_service> < <file_name>.dump
```

**If import is not possible, it may be necessary to destroy the application database, create a new one, and then, with the new empty database, do the import.**

## Cheat sheet

- Add environment variable:

```
dokku config:set --no-restart <my_app> <VARIABLE>=<Value>
dokku config:set <my_app> USE_AWS=True
```

- Run command:

```
dokku run <my_app> python manage.py collectstatic --noinput
```

- Check NGINX configuration of an application:

```
dokku nginx:show-config <my_app>
```

- Check domain configurations of an application:

```
dokku domains:report <my_app>
```

- Remove domain from an application:

```
dokku domains:remove <my_app> <domain.com>
```

- Restart an application:

```
dokku ps:restart <my_app>
```

- Rebuild an App:

```
dokku ps:rebuild <my_app>
```

- Access container

```
dokku enter <my_app> <application_type>
```

- List container mount points

```
dokku storage:list <my_app>
```

- Mount a directory on the host to the container

```
dokku storage:mount <my_app> /var/lib/dokku/data/storage/<my_app>/<folder_name>:/app/<folder_name>/
```

- Remove mount point

```
dokku storage:unmount <my_app> /var/lib/dokku/data/storage/<my_app>/<folder_name>:/app/<folder_name>
```

- Create a new database service:

```
dokku postgres:create <service_name>
```

- Export database to a file:

```
dokku postgres:export <service_name> > backup.dump
```

- Import data from a dump file:

```
dokku postgres:import <service_name> < backup.dump
```

- Destroy a database (ATTENTION):

```
dokku postgres:destroy <service_name>
```

- Link database to a Dokku application:

```
dokku postgres:link <service_name> <application_name>
```

- Unlink database from application

```
dokku postgres:unlink <service_name> <application_name>
```

- Copy file to S3 bucket:

```
aws s3 cp <file_name> s3://your-s3-bucket/path/
```

- Copy a file from the S3 bucket to the server:

```
aws s3 cp s3://your-s3-bucket/path/<file_name> <file_destination_path>
```

## Source

https://testdriven.io/blog/django-dokku/
https://dokku.com/docs/deployment/application-deployment/
