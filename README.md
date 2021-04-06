# Docker IMAPFilter + Isync

An opinionated set of simple Docker containers with [IMAPFilter](https://github.com/lefcha/imapfilter), [Isync](https://isync.sourceforge.io/) and [Supercronic](https://github.com/aptible/supercronic).

Forked from https://github.com/cewood/mbsync-docker to add imapfilter to the image.


## Usage

Being slightly opinionated there isn't any `ENTRYPOINT` specified, instead only a default `CMD` to run Supercronic with the bundled `/etc/crontab` file that simply echoes a short message once a minute. This means you can easily change what the container does without having to override an `ENTRYPOINT`, and if you want to use the Supercronic functionality you only need to mount in your own crontab file at `/etc/crontab` and run the container without any additional args.


### Docker Run

```
$ docker run --rm -it \
    -v $PWD/mbsyncrc:/workdir/.mbsyncrc \
    -v $PWD:/workdir/data \
    -w /workdir \
    cewood/mbsync:alpine_UPDATEME \
    mbsync --all --pull --create-slave --expunge-slave --verbose
```


### Docker Compose

```
version: "3.4"
services:
  mbsync:
    image: cewood/mbsync:alpine_UPDATEME
    container_name: mbsync
    working_dir: /workdir
    volumes:
      - "/mnt/storage/containers/mbsync/data:/workdir/data"
      - "./mbsync/mbsyncrc:/workdir/.mbsyncrc"
      - "./mbsync/crontab:/etc/crontab"  # Be sure to update the crontab with your command(s)
    restart: unless-stopped
    labels:
      org.label-schema.group: "backups"
```

