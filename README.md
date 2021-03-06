This is an example on how to start a docker-compose environment in a (remote) docker host.

**NB** This is similar to [rgl/windows-docker-compose-example](https://github.com/rgl/windows-docker-compose-example).

```bash
# set the DOCKER_HOST environment variable.
# this make the docker client use this dockerd.
# NB you must start dockerd with -H tcp://0.0.0.0:2375
export DOCKER_HOST=tcp://10.1.0.2:2375

# create the environment defined in docker-compose.yml
# and leave it running in the background.
docker-compose up --build -d

# show running containers.
docker-compose ps

# execute command inside the containers.
docker-compose exec -T hello ls -laF /
docker-compose exec -T etcd etcd --version
docker-compose exec -T -e ETCDCTL_API=3 etcd etcdctl version
docker-compose exec -T -e ETCDCTL_API=3 etcd etcdctl endpoint health
docker-compose exec -T -e ETCDCTL_API=3 etcd etcdctl put foo bar
docker-compose exec -T -e ETCDCTL_API=3 etcd etcdctl get foo

# get the allocated hello port and create an endpoint url based in
# the DOCKER_HOST environment variable host.
hello_endpoint="$(
python3 <<'EOF'
import os
import urllib.parse
import subprocess

docker_host_ip_address = urllib.parse.urlparse(os.environ['DOCKER_HOST']).netloc.split(':')[0]
p = subprocess.Popen(
    ['docker-compose', 'port', 'hello', '8888'],
    text=True,
    stdout=subprocess.PIPE,
    stderr=subprocess.STDOUT)
stdout, stderr = p.communicate()
hello_port = stdout.strip().split(':')[-1]
hello_endpoint = f'http://{docker_host_ip_address}:{hello_port}'
print(hello_endpoint)
EOF
)"
# invoke the hello endpoint.
wget -qO- $hello_endpoint

# show logs.
docker-compose logs

# destroy the environment.
docker-compose down
```
