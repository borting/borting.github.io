---
layout: post
title: "Where Does Docker Images and Layers Store? "
author: "Borting"
categories: journal
tags: [Docker]
image: chimei-museum.jpg
---

以在 "2021-11-27 20:53:24 CST" 用 `docker pull` 從 docker hub 抓下來的 nginx:latest 為例 (sha256:097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e), 來看看 docker 如何存放 image 和 layer 資訊.

info about how layers are made up images

# Basic Information

docker pull 後可以看到 image 的 sha256 ID 為 `097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e`.
```shell
borting@XPS:~/$ docker pull nginx:latest 
latest: Pulling from library/nginx
eff15d958d66: Pull complete 
1e5351450a59: Pull complete 
2df63e6ce2be: Pull complete 
9171c7ae368c: Pull complete 
020f975acd28: Pull complete 
266f639b35ad: Pull complete 
Digest: sha256:097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e
Status: Downloaded newer image for nginx:latest
docker.io/library/nginx:latest
```

用 `docker image` 看一下更多 image 的訊息
```shell
borting@XPS:~/$ docker images --digests --no-trunc 
REPOSITORY   TAG       DIGEST                                                                    IMAGE ID                                                                  CREATED       SIZE
nginx        latest    sha256:097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e   sha256:ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291   10 days ago   141MB
```

docker inspect 的結果
```shell
borting@XPS:~/$ docker inspect nginx:latest 
[
    {
        "Id": "sha256:ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291",
        "RepoTags": [
            "nginx:latest"
        ],
        "RepoDigests": [
            "nginx@sha256:097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2021-11-17T10:38:14.652464384Z",
        "Container": "8a038ff17987cf87d4b7d7e2c80cb83bd2474d66e2dd0719e2b4f7de2ad6d853",
        "ContainerConfig": {
            "Hostname": "8a038ff17987",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.21.4",
                "NJS_VERSION=0.7.0",
                "PKG_RELEASE=1~bullseye"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"nginx\" \"-g\" \"daemon off;\"]"
            ],
            "Image": "sha256:2fb4060b053a39040c51ff7eadd30325de2c76650fc50aa42839070e16e8bdcb",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGQUIT"
        },
        "DockerVersion": "20.10.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.21.4",
                "NJS_VERSION=0.7.0",
                "PKG_RELEASE=1~bullseye"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "Image": "sha256:2fb4060b053a39040c51ff7eadd30325de2c76650fc50aa42839070e16e8bdcb",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGQUIT"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 141490847,
        "VirtualSize": 141490847,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/8ff4649e53fe0416c796cce33aaa54b6cc7719f9eec26bebd59fba1da9ca8e33/diff:/var/lib/docker/overlay2/ab36405cffa9e8d57ea942f3730eb14f515d2b185b6f01c4598734f328b2ab1a/diff:/var/lib/docker/overlay2/dd70129c85a9323b2c5b6d05ee617d1856a6af7c98fce1a21320ee84dc9dfdf5/diff:/var/lib/docker/overlay2/05789092fdcf7cf60783bd97d1e91101c126bc3541492cbf39197d393c9622b4/diff:/var/lib/docker/overlay2/2467260cd29fba7d59f07c3d4e332555e538b6f433c96c5c19ec62e27f07b371/diff",
                "MergedDir": "/var/lib/docker/overlay2/21924b284183455f624411ad146fee93187ff9a2c409f4988e2a8af6425471dd/merged",
                "UpperDir": "/var/lib/docker/overlay2/21924b284183455f624411ad146fee93187ff9a2c409f4988e2a8af6425471dd/diff",
                "WorkDir": "/var/lib/docker/overlay2/21924b284183455f624411ad146fee93187ff9a2c409f4988e2a8af6425471dd/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:e1bbcf243d0e7387fbfe5116a485426f90d3ddeb0b1738dca4e3502b6743b325",
                "sha256:37380c5830feb5d6829188be41a4ea0654eb5c4632f03ef093ecc182acf40e8a",
                "sha256:ff4c727794302b5a0ee4dadfaac8d1233950ce9a07d76eb3b498efa70b7517e4",
                "sha256:49eeddd2150fbd14433ec1f01dbf6b23ea6cf581a50635554826ad93ce040b68",
                "sha256:1e8ad06c81b6baf629988756d90fd27c14285da4d9bf57179570febddc492087",
                "sha256:8525cde30b227bb5b03deb41bda41deb85d740b834be61a69ead59d840f07c13"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

檢視 manifest
```shell
# The output is same as that of next command
borting@XPS:~/$ docker manifest inspect nginx:latest

# Inspect the REPOSITORY@DIGEST
borting@XPS:~/$ docker manifest inspect nginx@sha256:097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e
{
   "schemaVersion": 2,
   "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
   "manifests": [
      {
         "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
         "size": 1570,
         "digest": "sha256:2f14a471f2c2819a3faf88b72f56a0372ff5af4cb42ec45aab00c03ca5c9989f",
         "platform": {
            "architecture": "amd64",
            "os": "linux"
         }
      },
      {
         "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
         "size": 1570,
         "digest": "sha256:f8167813f5eca57d590a5f561bbdeaafefacbc2a6645e532e84463938f9659cd",
         "platform": {
            "architecture": "arm",
            "os": "linux",
            "variant": "v5"
         }
      },
      {
         "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
         "size": 1570,
         "digest": "sha256:f6548b8fc86f8a20f4bd73555e117388d373311731c5fe2b0e957722a2f059b5",
         "platform": {
            "architecture": "arm",
            "os": "linux",
            "variant": "v7"
         }
      },
      {
         "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
         "size": 1570,
         "digest": "sha256:a6c61523d9ec031962daaec36cb0217b687b3edc377ea1552e2cd6f308df0558",
         "platform": {
            "architecture": "arm64",
            "os": "linux",
            "variant": "v8"
         }
      },
      {
         "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
         "size": 1570,
         "digest": "sha256:ef81d712430a62bc83fd0ce77821ac73530c423a9f66cec25e69c650accc6ae2",
         "platform": {
            "architecture": "386",
            "os": "linux"
         }
      },
      {
         "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
         "size": 1570,
         "digest": "sha256:32239681a11afe5103d29c2997accf11ecb6391f0f6f51d2bdf95431945e22f0",
         "platform": {
            "architecture": "mips64le",
            "os": "linux"
         }
      },
      {
         "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
         "size": 1570,
         "digest": "sha256:15bab52bec6c2181ef06e4e3850ee85a9fa3676b2fc499461fe02940f65bc6f7",
         "platform": {
            "architecture": "ppc64le",
            "os": "linux"
         }
      },
      {
         "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
         "size": 1570,
         "digest": "sha256:2611220dbc4fdfbff1c966d241797914d2125b39f07387664fd82f1ab0b7e806",
         "platform": {
            "architecture": "s390x",
            "os": "linux"
         }
      }
   ]
}
```

```shell
# Check the manifest of amd64 because the architecture of my computer is amd64
borting@XPS:~/$ docker manifest inspect nginx@sha256:2f14a471f2c2819a3faf88b72f56a0372ff5af4cb42ec45aab00c03ca5c9989f
{
	"schemaVersion": 2,
	"mediaType": "application/vnd.docker.distribution.manifest.v2+json",
	"config": {
		"mediaType": "application/vnd.docker.container.image.v1+json",
		"size": 7656,
		"digest": "sha256:ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291"
	},
	"layers": [
		{
			"mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
			"size": 31370267,
			"digest": "sha256:eff15d958d664f0874d16aee393cc44387031ee0a68ef8542d0056c747f378e8"
		},
		{
			"mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
			"size": 25347687,
			"digest": "sha256:1e5351450a593c3a3d7a5104f93c8b80d8dc00c827158cb3a5bf985916ea3f75"
		},
		{
			"mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
			"size": 602,
			"digest": "sha256:2df63e6ce2be0b3cefd3e659558e92b8085f032db96828343ec9cf0b7d4409fe"
		},
		{
			"mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
			"size": 895,
			"digest": "sha256:9171c7ae368c6ca24dae913fce356801f624f656360c78ca956a92c3f0fe0ec7"
		},
		{
			"mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
			"size": 668,
			"digest": "sha256:020f975acd28936c7ff43827238aed4771d14235dc983389ec149811f7e0b7cf"
		},
		{
			"mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
			"size": 1394,
			"digest": "sha256:266f639b35ad602ee76c3b4d4cf88285a50adf8f561d8d96d331db732fe16982"
		}
	]
}
# "digest": "sha256:ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291" <-- This is image ID
```

https://github.com/docker-library/repo-info/blob/master/repos/nginx/remote/1.md

Anaylsis
* `RepoDigests` is the unique content address of the image `nginx:latest`, which refers to somewhere of Docker hub(?)
```shell
$ docker manifest inspect nginx@sha256:097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e
```

* `RepoDigests` + `Architecture` determines the actual image would be pull, in this case that is and can be found in [this location on Docker Hub](https://hub.docker.com/layers/nginx/library/nginx/latest/images/sha256-2f14a471f2c2819a3faf88b72f56a0372ff5af4cb42ec45aab00c03ca5c9989f?context=explore)
```shell
$ docker manifest inspect nginx@sha256:2f14a471f2c2819a3faf88b72f56a0372ff5af4cb42ec45aab00c03ca5c9989f
```

* On local host `/var/lib/docker/image/overlay2/repositories.json` stores the info of pulled imagae.
"sha256:ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291" is the image ID (same as we get from inspecting `RepoDigests` + `Architecture`)
```shell
$ cat /var/lib/docker/image/overlay2/repositories.json
{"Repositories":{"nginx":{"nginx:latest":"sha256:ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291","nginx@sha256:097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e":"sha256:ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291"}}}
```

* Image 的 configuration 放在 `/var/lib/docker/image/overlay2/imagedb/content/sha256/IMAGE_ID`, 其結果和 `docker image inspect` 結果相同
For example "/var/lib/docker/image/overlay2/imagedb/content/sha256/ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291"

* Inspect the image ID, we can call `docker image inspect IMAGE_ID` to find layer info
(The output is same as that of running `docker inspect nginx:latest`)
```shell
$ docker image inspect "sha256:ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291"
[
    {
        "Id": "sha256:ea335eea17ab984571cd4a3bcf90a0413773b559c75ef4cda07d0ce952b00291",
        "RepoTags": [
            "nginx:latest"
        ],
        "RepoDigests": [
            "nginx@sha256:097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2021-11-17T10:38:14.652464384Z",
        "Container": "8a038ff17987cf87d4b7d7e2c80cb83bd2474d66e2dd0719e2b4f7de2ad6d853",
        "ContainerConfig": {
            "Hostname": "8a038ff17987",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.21.4",
                "NJS_VERSION=0.7.0",
                "PKG_RELEASE=1~bullseye"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"nginx\" \"-g\" \"daemon off;\"]"
            ],
            "Image": "sha256:2fb4060b053a39040c51ff7eadd30325de2c76650fc50aa42839070e16e8bdcb",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGQUIT"
        },
        "DockerVersion": "20.10.7",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.21.4",
                "NJS_VERSION=0.7.0",
                "PKG_RELEASE=1~bullseye"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "Image": "sha256:2fb4060b053a39040c51ff7eadd30325de2c76650fc50aa42839070e16e8bdcb",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGQUIT"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 141490847,
        "VirtualSize": 141490847,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/8ff4649e53fe0416c796cce33aaa54b6cc7719f9eec26bebd59fba1da9ca8e33/diff:/var/lib/docker/overlay2/ab36405cffa9e8d57ea942f3730eb14f515d2b185b6f01c4598734f328b2ab1a/diff:/var/lib/docker/overlay2/dd70129c85a9323b2c5b6d05ee617d1856a6af7c98fce1a21320ee84dc9dfdf5/diff:/var/lib/docker/overlay2/05789092fdcf7cf60783bd97d1e91101c126bc3541492cbf39197d393c9622b4/diff:/var/lib/docker/overlay2/2467260cd29fba7d59f07c3d4e332555e538b6f433c96c5c19ec62e27f07b371/diff",
                "MergedDir": "/var/lib/docker/overlay2/21924b284183455f624411ad146fee93187ff9a2c409f4988e2a8af6425471dd/merged",
                "UpperDir": "/var/lib/docker/overlay2/21924b284183455f624411ad146fee93187ff9a2c409f4988e2a8af6425471dd/diff",
                "WorkDir": "/var/lib/docker/overlay2/21924b284183455f624411ad146fee93187ff9a2c409f4988e2a8af6425471dd/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:e1bbcf243d0e7387fbfe5116a485426f90d3ddeb0b1738dca4e3502b6743b325",
                "sha256:37380c5830feb5d6829188be41a4ea0654eb5c4632f03ef093ecc182acf40e8a",
                "sha256:ff4c727794302b5a0ee4dadfaac8d1233950ce9a07d76eb3b498efa70b7517e4",
                "sha256:49eeddd2150fbd14433ec1f01dbf6b23ea6cf581a50635554826ad93ce040b68",
                "sha256:1e8ad06c81b6baf629988756d90fd27c14285da4d9bf57179570febddc492087",
                "sha256:8525cde30b227bb5b03deb41bda41deb85d740b834be61a69ead59d840f07c13"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

幾個我們有興趣的資訊
```shell
"Image": "sha256:2fb4060b053a39040c51ff7eadd30325de2c76650fc50aa42839070e16e8bdcb",


"GraphDriver": {
    "Data": {
        "LowerDir": "/var/lib/docker/overlay2/8ff4649e53fe0416c796cce33aaa54b6cc7719f9eec26bebd59fba1da9ca8e33/diff:/var/lib/docker/overlay2/ab36405cffa9e8d57ea942f3730eb14f515d2b185b6f01c4598734f328b2ab1a/diff:/var/lib/docker/overlay2/dd70129c85a9323b2c5b6d05ee617d1856a6af7c98fce1a21320ee84dc9dfdf5/diff:/var/lib/docker/overlay2/05789092fdcf7cf60783bd97d1e91101c126bc3541492cbf39197d393c9622b4/diff:/var/lib/docker/overlay2/2467260cd29fba7d59f07c3d4e332555e538b6f433c96c5c19ec62e27f07b371/diff",
        "MergedDir": "/var/lib/docker/overlay2/21924b284183455f624411ad146fee93187ff9a2c409f4988e2a8af6425471dd/merged",
        "UpperDir": "/var/lib/docker/overlay2/21924b284183455f624411ad146fee93187ff9a2c409f4988e2a8af6425471dd/diff",
        "WorkDir": "/var/lib/docker/overlay2/21924b284183455f624411ad146fee93187ff9a2c409f4988e2a8af6425471dd/work"
    },
    "Name": "overlay2"
},


"RootFS": {
    "Type": "layers",
    "Layers": [
        "sha256:e1bbcf243d0e7387fbfe5116a485426f90d3ddeb0b1738dca4e3502b6743b325",
        "sha256:37380c5830feb5d6829188be41a4ea0654eb5c4632f03ef093ecc182acf40e8a",
        "sha256:ff4c727794302b5a0ee4dadfaac8d1233950ce9a07d76eb3b498efa70b7517e4",
        "sha256:49eeddd2150fbd14433ec1f01dbf6b23ea6cf581a50635554826ad93ce040b68",
        "sha256:1e8ad06c81b6baf629988756d90fd27c14285da4d9bf57179570febddc492087",
        "sha256:8525cde30b227bb5b03deb41bda41deb85d740b834be61a69ead59d840f07c13"
    ]
},
```


Docker Registry API V2 seperates location of content from naming, i.e. layers become content-addressable blobs.
The content addess is called `digest`, for example
```
nginx@sha256:097c3a0913d7e3a5b01b6c685a60c03632fc7a2b50bc8e35bcaa3691d788226e
```
is the SHA256 hash of image manifest.

A digest can represent a manifest, a layer or a combination of them (i.e. an image).


manifest
* describe a component of an image in a single JSON file
* The file contains the digests of all lower layers.
* If any layer is changed, then its digest is changed, as well as the manifest of the image is changed


Comparison
* image ID is the hash of the local image JSON configuration (this configuration is different from manifest).
* image digest is the SHA256 hash of image manifest (introduced by Docker Registry V2). This might none if the image has not been pushed to or pulled from a V2 registry. 

Container
Continer 執行起來後的 writable layer, metadata, log 都放在 `/var/lib/docker/containers/[CONTAINER_ID]`


# Reference
* [Docker Registry V2](https://www.slideshare.net/Docker/docker-registry-v2)
