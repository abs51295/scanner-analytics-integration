# Atomic scanner: scanner-analytics-integration


This is a container image scanner based on `atomic scan`. The goal of this
scanner is integration with [fabric8-analytics server]([200~https://github.com/fabric8-analytics/f8a-server-backbone/). 
The scanner reads the `Labels` of the image and triggers scan at server with data in Labels.

### Steps to use:

- Pull scanner Docker image from **registry.centos.org**:

```
$ docker pull registry.centos.org/pipeline-images/scanner-analytics-integration
```

- Install it using `atomic`:

```
$ atomic install registry.centos.org/pipeline-images/scanner-analytics-integration
```

- Run the scanner for a given image

```
$sudo IMAGE_NAME=<image-to-scan> SERVER=<server-url> atomic scan --scanner analytics-integration $IMAGE_NAME
```

where

```
IMAGE_NAME = Image under test
SERVER = Fabric7 Analytics Server URL
```

### Output of the scanner:

- Upon successful execution of scanner (irrespective of the successful server connection), scanner output looks like
```
# SERVER=http://localhost IMAGE_NAME=nshaikh/test_label atomic scan --verbose --scanner=analytics-integration nshaikh/test_label
docker run -t --rm -v /etc/localtime:/etc/localtime -v /run/atomic/2018-03-01-09-47-21-795242:/scanin -v /var/lib/atomic/analytics-integration/2018-03-01-09-47-21-795242:/scanout:rw,Z -v /var/run/docker.sock:/var/run/docker.sock -e IMAGE_NAME=nshaikh/test_label -e SERVER=http://localhost registry.centos.org/pipeline-images/scanner-analytics-integration python integration.py register

Scanner execution status: False

Files associated with this scan are in /var/lib/atomic/analytics-integration/2018-03-01-09-47-21-795242.
```

Notice line, `Scanner execution status: False` <- indicating the scanner could not connect to server.

Scanner exports the result as mentioned on stdout
```
Files associated with this scan are in /var/lib/atomic/analytics-integration/2018-03-01-09-47-21-795242.

```

Lets take a look at result

```
# tree /var/lib/atomic/analytics-integration/2018-03-01-09-47-21-795242
/var/lib/atomic/analytics-integration/2018-03-01-09-47-21-795242
├── aa295492a6702264ffe86c341e92aa83f9687e810cf29d72593eb7a2c27d0449
│   └── scanner-analytics-integration.json
└── environment.json

1 directory, 2 files
```

The result file is `scanner-analytics-integration.json`

Now lets take a look at the contents of result file.

```
# cat /var/lib/atomic/analytics-integration/2018-03-01-09-47-21-795242/aa295492a6702264ffe86c341e92aa83f9687e810cf29d72593eb7a2c27d0449/scanner-analytics-integration.json
{
    "Scan Type": "register",
    "CVE Feed Last Updated": "NA",
    "UUID": "a295492a6702264ffe86c341e92aa83f9687e810cf29d72593eb7a2c27d0449",
    "Scan Results": {
        "email-ids": "nshaikh@redhat.com,samuzzal@redhat.com",
        "git-sha": "46e443d",
        "git-url": "https://github.com/fabric8-analytics/f8a-server-backbone",
        "image_name": "nshaikh/test_label",
        "server_url": "http://localhost"
    },
    "Successful": false,
    "Finished Time": "2018-03-01-09-47-23-186428",
    "Summary": "Error: [\"('Connection aborted.', error(111, 'Connection refused'))\"]",
    "Start Time": "2018-03-01-H-47-23",
    "Scanner": "scanner-analytics-integration"
}
```

The `"Scan Results"` field indicates, Labels data retrieved from image under test reports errors in `"Summary"` field.
Whether connection to server was successful, is indicated by `"Successful"` field,
here `false` as no server is running on `http://localhost` (given in scanner run command).

