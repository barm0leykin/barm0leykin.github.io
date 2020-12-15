### SSH docker build
#### docker build
``
DOCKER_BUILDKIT=1 docker build --build-arg CACHEBUST=545 --build-arg BASEIMAGE=baseimage-ub20 -t admin_interface --add-host=git.xtools.tv:10.15.0.199 --ssh default .
``
Можно каждый раз переменную не указывать:
``
export DOCKER_BUILDKIT=1
``

#### Dockerfile
``
# syntax=docker/dockerfile:experimental
RUN git config --global --add url."git@git.xtools.tv:tv/".insteadOf "https://git.xtools.tv/tv/"

RUN --mount=type=ssh CACHE=$CACHEBUST git clone --depth 1 --single-branch --branch master git@git.xtools.tv:tv/go-container.git    $GOPATH/src/git.xtools.tv/tv/go-container
``
### SSH docker run
``
docker run -it --network=host -v $(dirname $SSH_AUTH_SOCK):$(dirname $SSH_AUTH_SOCK) -e SSH_AUTH_SOCK=$SSH_AUTH_SOCK --name go-container go-container
``

### Проверка линтером
``
docker run --rm -i hadolint/hadolint < Dockerfile
``
### source
тк каждый RUN отрабытывает отдельно, то загрузка переменных через source не сработает в следующем ране. Их нужно запихнуть в файл `/etc/bash.bashrc` и переопределить шелл с опщией `--login`, чтобы этот файл автоматически подгружался
```
RUN echo "source /etc/profile.d/rvm.sh" >> /etc/bash.bashrc
SHELL ["/bin/bash", "--login", "-c"]
RUN echo "здесь уже есть переменные окружения из /etc/bash.bashrc"
```