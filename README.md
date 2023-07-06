# PI4 STORIES - k3s journey

## Using docker image
Use the [Dockerfile from the Rear User Guide](https://github.com/rear/rear-user-guide/blob/master/Dockerfile) to build a mkdocs container image. Then start it up as following:

```bash
$ docker run -it -v /home/gdha/projects/web/pi-stories:/home/gdha/web \
  -v /home/gdha/.gitconfig:/home/gdha/.gitconfig -v /home/gdha/.ssh:/home/gdha/.ssh \
  -v /home/gdha/.gnupg:/home/gdha/.gnupg --net=host mkdocs
$ docker rename condescending_neumann pi
```

Later on you can go start up the container image as `docker start -i pi`

You can serve the web pages locally via `mkdocs serve` and it will tell you which URL link to use in your browser.
