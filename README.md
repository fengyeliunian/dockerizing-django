## Django Development With Docker Compose and Machine

Featuring:

- Docker version 18.03.0-ce, build 0520e24302
- docker-compose version 1.20.1, build 5d8c71b2
- Docker Machine version 0.14.0, build 89b8332
- Python 3.6.5

Blog post -> https://realpython.com/blog/python/django-development-with-docker-compose-and-machine/

### Install Docker Toolbox on Windows

via -> https://docs.docker.com/toolbox/toolbox_install_windows/

### OS X or Windows Instructions

1. Start new machine - `docker-machine create -d virtualbox dev;`
1. Configure your shell to use the new machine environment - `eval $(docker-machine env dev)`
1. Build images - `docker-compose build`
1. Start services - `docker-compose up -d`
1. Create migrations - `docker-compose run --no-deps --rm web python manage.py migrate`
1. Collectstatic - `docker-compose run --no-deps --rm web python manage.py collectstatic`
1. Grab IP - `docker-machine ip dev` - and view in your browser

### Backup/Restore a dockerized PostgreSQL database

via -> https://stackoverflow.com/a/29913462

- Backup your databases
```shell
docker exec -t your-db-container pg_dumpall -c -U postgres > dump_`date +%d-%m-%Y"_"%H_%M_%S`.sql
```
- Restore your databases
```shell
cat your_dump.sql | docker exec -i your-db-container psql -U postgres
```

### Migrate Django from SQLite to PostgreSQL

via -> https://stackoverflow.com/questions/3034910/whats-the-best-way-to-migrate-a-django-db-from-sqlite-to-mysql

Dump existing data:

```shell
docker-compose run --no-deps --rm web python manage.py dumpdata > datadump.json
```

Change settings.py to Postgres backend. Make sure you can connect on PostgreSQL. Then:

```shell
docker-compose run --no-deps --rm web python manage.py migrate --run-syncdb
```

Run this on Django shell to exclude contentype data

```shell
docker-compose run --no-deps --rm web python manage.py shell

>>> from django.contrib.contenttypes.models import ContentType
>>> ContentType.objects.all().delete()
>>> quit()
```

Finally:

```shell
docker-compose run --no-deps --rm web python manage.py loaddata datadump.json
```
