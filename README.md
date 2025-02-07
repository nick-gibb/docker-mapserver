# Alpine Docker container for Mapserver

[![](https://dockerbuildbadges.quelltext.eu/status.svg?organization=bjoernschilberg&repository=docker-alpine-mapserver)](https://hub.docker.com/r/bjoernschilberg/docker-alpine-mapserver/)

A minimal Mapserver Docker image based on [Alpine
Linux](https://alpinelinux.org/about/) with a complete package index and only
18 MB in size (compressed)!

## What is Alpine Linux?

Alpine Linux is a Linux distribution built around [musl
libc](https://www.musl-libc.org/) and
[BusyBox](https://busybox.net/about.html). The pure image is only 5 MB in
size and has access to a package repository that is much more complete than
other BusyBox based images.

## What Mapserver version is included?

Check https://pkgs.alpinelinux.org/packages?name=mapserver&branch=edge

## Build image

```shell
docker build -t mapserver .
```

## Start container

```shell
docker run -d -p 8181:80 -v `pwd`/map:/map mapserver
```

while the command is

```shell
docker run -d -p [exposed port]:[internal port] -v [your-path]:[container-path] mapserver
```

## Access container via http

When Docker is forwarded as `localhost` simply open the following URL:

```shell
http://localhost:8181/cgi-bin/mapserv
```

To make sure the url specified above works the web browser should give a
response:

```shell
No query information to decode. QUERY_STRING is set, but empty.
```

A quick way to access the contents of your mapfile in a browser can be
achived by:

```shell
http://localhost:8181/cgi-bin/mapserv?map=/map/frida.map&mode=browse&template=openlayers&layers=all
```

 The displayed map should look like that:

 ![Image of Frida Map](frida-map-browse-mode-screenshot.png)

## Create your own `mapfile`

Replace the frida.map with the name of your own mapserver
[mapfile](http://www.mapserver.org/mapfile/).

This works:

http://localhost/cgi-bin/mapserv?SERVICE=WMS&VERSION=1.3.0&REQUEST=GetMap&FORMAT=image%2Fpng&TRANSPARENT=true&LAYERS=quebec&map=%2Fmap%2Fgeneric.map&CRS=EPSG%3A4326&STYLES=&WIDTH=1435&HEIGHT=918&BBOX=45.470575,-73.618441,45.498887,-73.536730

http://localhost/cgi-bin/mapserv?map=/map/generic.map&layer=quebec&mode=map


http://localhost/cgi-bin/mapserv?map=/map/generic.map&mode=browse&template=openlayers&layers=all

## Debugging

You can use the following environment variables (when starting the container)
to debug it:

* `MS_DEBUGLEVEL`: The debug level 0=off 5=verbose
* `MS_ERRORFILE`: If you want the debug to something other than STDOUT

More infos on [Debugging MapServer](http://mapserver.org/de/optimization/debugging.html).

## Logs

Watch container logs with:

```shell
docker logs <CONTAINER ID>
```

View the log files being written as they happen:

```shell
docker logs --tail=10 -f <CONTAINER ID>
```

## Tunings

You can use the following environment variables (when starting the container)
to tune it:

* `MAX_REQUESTS_PER_PROCESS`: To work around memory leaks (defaults to 1000)

## Enter a running container via shell

```shell
docker exec -it <CONTAINER ID> /bin/ash
```

`ash` is the [Almquist Shell](https://en.wikipedia.org/wiki/Almquist_shell),
the default shell under [Alpine Linux](https://alpinelinux.org/) provided by
[BusyBox](https://busybox.net/about.html).

There you'll see the mapped `/map` folder and can use useful cli tools like

* Mapserver utilities (e.g. `mapserv`): http://www.mapserver.org/utilities/
* Raster utilities (e.g. `gdalinfo`): http://www.gdal.org/gdal_utilities.html
* Vector utilities (e.g. `ogr2ogr`): http://www.gdal.org/ogr_utilities.html

## Start and enter a new container via shell

To enter the container with a shell interface simple switch from `daemon`
mode to `interactive` mode.

```shell
docker run -it -v `pwd`/map:/map mapserver /bin/ash
```
