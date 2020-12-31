# Mbsync Docker

An opinionated set of simple Docker containers with Mbsync ([Isync](https://isync.sourceforge.io/)) and [Supercronic](https://github.com/aptible/supercronic).


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


## Image variants

Currently the following distributions are included:

 - Alpine 3.12
 - Debian 10.7 (Slim variant)
 - Ubuntu 20.04 (LTS release)


## Supported architectures

Currently the following architectures are built for each image variant:

 - linux/amd64
 - linux/arm64
 - linux/arm/v7


# Frequently Asked Questions
## Why another Mbsync Docker Image

There was already a number of Mbsync/Isync images available on the Docker Hub, but unfortunately they all had their various short comings. Most weren't built via a Continuous Integration tool, none had any image tags, and none provided the Dockerfile or a link to their SCM tool of choice to inspect the image contents and build. Thus I decided to make my own set of images for Mbsync to address all these points, and here we are.


## Why isn't the image named Isync

Great question! Since most people use Isync via the Mbsync command, I decided to name the image Mbsync, as this appears to be what most people search for and are familiar with. And the project itself states:

> While isync is the project name, mbsync is the current executable name; this change was necessary because of massive changes in the user interface. An isync executable still exists; it is a compatibility wrapper around mbsync.


## Why Supercronic and not just regular cron

If we were running a normal system in an interactive manner, then normal Cron or friends (Anacron, Fcron, etc) would be fine choices. However in a containerised environment, these traditional cron implementations have some downsides, that make Supercronic a better fit. Namely the printing of jobs output to stdout, hence when running Supercronic as the ENTRYPOINT/CMD I can see the output of the jobs being run without having to jump through any hoops. There are undoubtedly other features that Supercronic brings with it that make it a better fit for use in containers, but this was the main motivator for me.
