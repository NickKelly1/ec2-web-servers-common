
the ./registry directory can be mounted by a docker registry

[Self-hosted registry guide](https://docs.docker.com/registry/deploying/#copy-an-image-from-docker-hub-to-your-registry)

[Auth Guide](https://docs.docker.com/registry/deploying/#native-basic-auth)

[REST(ish...?) API reference](https://docs.docker.com/registry/spec/api/)

[Deleting from private repo](https://forums.docker.com/t/delete-repository-from-v2-private-registry/16767/3)

```bash
mkdir auth

# ensure htpasswd is installed... or figure out how to access it from within the docker container
# create a htpasswd file the registry can use for basic auth
# the docker registry uses bcrypt algorithm, so use htpasswd's -B option
htpasswd -Bc ./auth/htpasswd nick-support

# note: must use TLS... This example assumes the registry runs behidn an encrypted proxy
docker run -d \
  -p 5000:5000 \
  --restart=always \
  --name registry \
  -v "$(pwd)"/auth:/auth \
  -e "REGISTRY_AUTH=htpasswd" \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry:2

docker login docker.nickkelly.dev
# promtped for user...
# prompted for password...
```


## Http API

### Deleting

[Deleting from a private registry](https://forums.docker.com/t/delete-repository-from-v2-private-registry/16767/3)

If deleting stuff, docker requires a lot of maintenance

After an image is deleted using a http request
```bash
# list catalogue (images)
curl \
   -u nick-support \
   -X GET \
   -i \
   -H "Accept: application/vnd.docker.distribution.manifest.v2+json" \
   https://docker.nickkelly.dev:443/v2/_catalog

# list image tags
curl \
   -u nick-support \
   -X GET \
   -i \
   -H "Accept: application/vnd.docker.distribution.manifest.v2+json" \
   https://docker.nickkelly.dev:443/v2/my-ubuntu/tags/list

# list image tags manifest
curl \
   -u nick-support \
   -X GET \
   -i \
   -H "Accept: application/vnd.docker.distribution.manifest.v2+json" \
   https://docker.nickkelly.dev:443/v2/my-ubuntu/manifests/latest

# send delete request for reference to manifests digest (sha:...)
# manifest.config.digest = sha:4f53....
curl \
   -u nick-support \
   -X DELETE \
   -i \
   -H "Accept: application/vnd.docker.distribution.manifest.v2+json" \
   https://docker.nickkelly.dev:443/v2/my-ubuntu/manifests/sha256:096efd74bb89d5ec92cb3a61c79cf8ceab00c135b7d3e
```


Afer deleting images / tags, you may still have to perform garbage collection

If removing an image completely, after removing all its tags you may have to remove the images directory

From within container:
```sh
docker exec -it /bin/sh
cd /bin
# registry garbage-collect /etc/docker/registry/config.yml --dry-run /etc/docker/registry/config.yml
registry garbage-collect /etc/docker/registry/config.yml /etc/docker/registry/config.yml
```

From outside container:
```sh
# docker -exec -it /bin/registry garbage-collect --dry-run /etc/docker/registry/config.yml
docker -exec -it /bin/registry garbage-collect /etc/docker/registry/config.yml # --dry-run
```

