Geoserver on Docker
===================

Geoserver Dockerfile with GDAL bindings which include:

- ECW
- MrSID
- JP2K


Getting Started
---------------

Run your geoserver on port 8080 and mount your `data_dir`.

```bash
$ mkdir ~/geoserver_data
$ docker run -d \
    -p 8080:8080 \
    -v ~/geoserver_data:/opt/geoserver/data_dir \
    --name geoserver \
    deniscarriere/geoserver
```

GDAL
----

How imagery metadata using **GDALInfo** on a MrSid file.

```bash
docker run -t -i -v /data:/data -v /www:/www geodata/gdal /bin/bash
```

How to create a TMS using **GDAL2Tiles.py** on a MrSid file.

```bash
gdal2tiles.py -z 0-13 Brockville.sid /www/brockville/
```

- `-z` Zoom Levels
- `-s` Source input projection
- `input` Input files
- `output` Output folder


TMS Resolution
--------------

| Zoom Level |     Scale      | Pixel Resolution |
|:-----------|:---------------|:----------------:|
|  22        |  1:125         |  3.5 cm          |
|  21        |  1:250         |  7 cm            |
|  20        |  1:500         |  15 cm           |
|  19        |  1:1,000       |  30 cm           |
|  18        |  1:2,000       |  60 cm           |
|  17        |  1:4,000       |  1.2 m           |
|  16        |  1:8,000       |  2.4 m           |
|  14        |  1:35,000      |  10 m            |
|  11        |  1:250,000     |  76 m            |
|  10        |  1:500,000     |  153 m           |
|  9         |  1:1 Million   |  305 m           |
|  7         |  1:4 Million   |  1.2 km          |

Amazon S3
---------

### Permissions

```json
{
  "Version": "2012-10-17",
  "Id": "S3PolicyId1",
  "Statement": [
    {
      "Sid": "IPAllow",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::pacgeo/*",
      "Condition": {
        "IpAddress": {
          "aws:SourceIp": "52.24.20.105/24"
        }
      }
    }
  ]
}
```

Nginx
-----

Set up your Nginx configuration in `/etc/nginx/sites-available/geoserver`.

```conf
server {
    listen  80;
    server_name  example.com www.example.com;
    location / {
        proxy_pass  http://127.0.0.1:8080/;
    }
}

proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
```
