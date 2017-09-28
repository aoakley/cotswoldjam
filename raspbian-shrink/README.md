# Raspian Shrink

Shrinks a Raspberry Pi Raspbian SD card image

Public Domain 2016-04 Andrew Oakley cotswoldjam.org

(Note: RASPBIAN images only. Will not work with NOOBS.)

## Usage

```sh
sudo raspbian-shrink [ -f ] [ -m FREESPACEMB ] imagename.img [ output.img ]
```

## OSX support

Works nicely using docker, thanks to James Osborne contribution.

### Install Docker

https://docs.docker.com/docker-for-mac/install/

### Docker image build

In a new folder, clone the repo:

```sh
git clone https://github.com/aoakley/cotswoldjam.git
```

add the provided [Dockerfile](./Dockerfile)

Build the docker image, in the same directory, here represents a repository name and the image will be called raspbian-shrink:

`docker build -t <name>/raspbian-shrink .`

### Shrink image

To run the container and shrink our image (large.img) we need some extra permissions to execute the mounts and we must map the loop0 device (which actually comes from the underlying linux VM running in xhyve that is part of Docker for Mac AFAIK). This command assumes that large.img is in the current working directory, but you can change this behaviour by modifying this command to insert the path to the image instead of $(pwd):

`docker run --rm -it -v $(pwd):/work-dir --device=/dev/loop0:/dev/loop0:rw --cap-add=SYS_ADMIN --name shrink <name>/raspbian-shrink large.img shrunk.img`

Or add this to you .bashrc :

```sh
DOCKER_REPO_PREFIX=<name>

shrink()
{
   docker run --rm \
       -it \
       -v `pwd`:/work-dir \
       --device=/dev/loop0:/dev/loop0:rw \
       --cap-add=SYS_ADMIN \
       --name shrink \
       ${DOCKER_REPO_PREFIX}/rapbian-shrink "$@"
}
```

Then run `shrink large.img small.img` directly from the terminal to resize some image :)