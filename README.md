# JBrowse

Configurable docker image for [GMOD/JBrowse](https://github.com/gmod/jbrowse/).
[http://jbrowse.org/](http://jbrowse.org/)

This docker image allows customization of loaded data; by placing
executable shell scripts in the mounted folder, you can easily load data
on boot.

## Based On: 
https://github.com/erasche/docker-jbrowse - Eric Rasche

## Quick Start:
Run the docker image from linux shell (retrieves and installs if necessary).
```console
$ docker run -p 8080:80 jbrowse/gmod-jbrowse
```
After this completes, JBrowse will be accessible from a web browser.
Find the container ip address on windows:
```console
docker-machine ip
```
Find the container ip address on linux/mac:
```console
docker inspect --format '{{ .NetworkSettings.IPAddress }}' $(docker ps -q)
```
From a web browser:
```console
http://<container IP address>:8080
```
## running specific version:
(earliest docker image available is 1.12.0)
Run the docker image from linux shell (retrieves and installs if necessary).
```console
$ docker run -p 8080:80 jbrowse/gmod-jbrowse:1.12.0
```

## Launching with YAML script:

A `docker-compose.yml` file is provided for your convenience, allowing you to boot up the example quite quickly:

```console
$ docker-compose up
```

## Mount point

Data can be provided to the container via a mount:

```console
$ docker run -v `pwd`/my-data/:/data/ jbrowse/jbrowse-1.12.0
```

## Startup Scripts

Running the default JBrowse instance is likely uninteresting, and you'd like to
run it with your own data.

This is easy to do, just dump data in a folder and provide some `.sh` script(s)
to load that data on boot. Here is an example of how the volvox data is loaded:

```bash
rm -rf $JBROWSE_DATA/json/yeast/;
bin/prepare-refseqs.pl \
    --fasta sample_data/raw/yeast_scaffolds/chr1.fa.gz \
    --fasta sample_data/raw/yeast_scaffolds/chr2.fa.gzip \
    --out $JBROWSE_DATA/json/yeast;

gunzip -c \
    $JBROWSE_DATA/yeast_scaffolds/chr1.fa.gz \
    $JBROWSE_DATA/raw/yeast_scaffolds/chr2.fa.gzip \
    > $JBROWSE_DATA/raw/yeast_chr1+2/yeast.fa;

bin/biodb-to-json.pl \
    --conf $JBROWSE_DATA/raw/yeast.json \
    --out $JBROWSE_DATA/json/yeast/;

bin/add-json.pl \
    '{ "dataset_id": "yeast" }' \
    $JBROWSE_DATA/json/yeast/trackList.json

bin/generate-names.pl --dir $JBROWSE_DATA/json/yeast/;

```

### Environment Variables

There are a couple environment variables available to startup scripts:

Variable       | Value/Use
-------------- | ---
`JBROWSE`      | The location of the jbrowse installation, including the `index.html`
`JBROWSE_DATA` | Location for the `sample_data` folder which contains publicised data
`DATA_DIR`     | Location of mounted data
