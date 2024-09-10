# Run Gitlab-CE + Gitlab-runner with compose
This project try to run Gitlab-CE and Gitlab runner with Podman-compose on localhost.
You can use docker-compose instead.
## Dependencies
### docker
REF: https://docs.docker.com/engine/install/
### podman
REF: https://podman.io/docs/installation
### podman-compose
REF: https://github.com/containers/podman-compose
## Commands
1. Setup environment
```bash
mkdir -p /srv/gitlab/gitlab-runner
touch /srv/gitlab/gitlab-runner/config.toml
```
Set permission for podman
```bash
sudo chown -R 1000:1000 /srv/gitlab
```
2. Deploy Gitlab-CE + Gitlab-Runner
```bash
podman-compose up -d
```
3. Reset Gitlab root password
```bash
# Access to gitlab-ce container
podman exec gitlab-ce /bin/bash
# Set root password
gitlab-rake "gitlab:password:reset[root]"
```
4. Setup Gitlab-runner
```bash
# Access to gitlab-runner container
podman exec gitlab-runner /bin/bash
# Register runner
gitlab-runner register
```
Parameters:
```yaml
Enter the GitLab instance URL (for example, https://gitlab.com/):
http://gitlab-ce
Enter the registration token:
glrt-xxx # Get this on your gitlab website (https://docs.gitlab.com/runner/register/?tab=Docker)
enter an executor: shell, ssh, parallels, docker, docker+machine, docker-autoscaler, custom, virtualbox, docker-windows, kubernetes, instance:
docker
```
set base image to: docker
## Note
1. Gitlab-server
- Using SSH repo with root user
Set this config on `~/.ssh/config` 
```
Host localhost
  HostName localhost
  User root
  Port 2202
  IdentityFile ~/.ssh/gitlab_rsa
```
2. Gitlab-runner
- Remember to set this parameter which help your runner resolve your local gitlab server. Check example file `config.toml.template`
```yaml
...
[runners.docker]
    ...
    network_mode = "host"
```
3. Podman
- When running podman on ubuntu Jammy, you may encouter container error with crun. Remove current crun with apt then install latest version on https://github.com/containers/crun
## TODO
