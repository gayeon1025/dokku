# 업그레이드

만약 0.3.0 이전의 버전을 사용중이라면 [새로운 버전](/docs/getting-started/installation.md)으로 업그레이드 할 것을 권장합니다.

## 보안 업데이트

보안 관련 업데이드 사항에 대해서는 [Dokku Twitter 게정](https://twitter.com/dokku)을 참고해 주시기 바랍니다. Dokku는 데몬을 전혀 사용하지 않기 때문에, Dokku의 보안 이슈는 적습니다.

종종 운영 체제는 보안 업데이트를 제공할 것입니다. 운영 체제에 대한 자동 업그레이그를 진행할 것을 권장합니다. 이와 관련해서 아래의 링크들을 참고하세요:

- [Arch Linux System Maintenance](https://wiki.archlinux.org/index.php/System_maintenance)
- [Centos Automatic Security Updates](https://serversforhackers.com/c/automatic-security-updates-centos)
- [Debian Unattended Upgrades](https://wiki.debian.org/UnattendedUpgrades)
- [Ubuntu Unattended Upgrades](https://help.ubuntu.com/community/AutomaticSecurityUpdates)

Docker는 주기적으로 엔진 업데이트를 발표합니다. Docker의 릴리즈 노트를 숙지한 후 해당 다큐먼트의 안내에 따라 업그레이드를 진행해 주십시오. 더 자세한 내용은 [Docker documentation](https://docs.docker.com/)을 참고하세요.

마지막으로, Dokku 역시 `dokku-update`라는 유용하고 권장하는 패키지를 제공합니다:

- 별도로 설치 가능하므로, Dokku를 업그레이드해도 이 패키지의 실행에는 영향을 미치지 않습니다.
- 많은 업그레이드 인스트럭션을 자동화합니다.
- 향후 업그레이드 프로세스를 향상시킬 수 있는 명확한 방법을 제공합니다.

소스로 설치할 경우 `contrib/dokku-update`에서 다운로드 가능하며, 패키지 저장소의 `dokku-update`라는 Debian 및 RPM 기반의 시스템에서도 이용 가능합니다. 

## Migration 가이드

업그레이드 전에, migration 가이드를 통해 새로운 기능에 익숙해진 다음 배포를 업그레이드 할 수 있도록 준비하시기 바랍니다.

### 0.5 Migration Guide

- [0.5 Migration Guide](/docs/appendices/0.5.0-migration-guide.md)

### 0.6 Migration Guide

- [0.6 Migration Guide](/docs/appendices/0.6.0-migration-guide.md)

### 0.7 Migration Guide

- [0.7 Migration Guide](/docs/appendices/0.7.0-migration-guide.md)

### 0.8 Migration Guide

- [0.8 Migration Guide](/docs/appendices/0.8.0-migration-guide.md)

### 0.9 Migration Guide

- [0.9 Migration Guide](/docs/appendices/0.9.0-migration-guide.md)

### 0.10 Migration Guide

- [0.10 Migration Guide](/docs/appendices/0.10.0-migration-guide.md)

## 업그레이드 인스트럭션

Dokku를 `apt-get install dokku`나 `bootstrap.sh`(가장 흔한 방법)를 이용해 설치했다면, 아래의 명령어로 업그레이드 하시기 바랍니다:

```shell
# 로컬의 apt cache를 업데이트
sudo apt-get update

# 실행중인 앱들을 중단
# 0.11.4 이상의 버전의 경우
dokku ps:stopall
# 0.8.1 ~ 0.11.3 버전의 경우
dokku --quiet apps:list | xargs -L1 dokku ps:stop
# 0.8.1 이전 버전의 경우
dokku --quiet apps | xargs -L1 dokku ps:stop

# dokku와 dependency들의 업데이트
sudo apt-get install -qq -y dokku herokuish sshcommand plugn

# 모든 앱들을 다시 빌드
dokku ps:rebuildall # rebuilds all applications
```

> `tags`나 `tar` 명령어로 배포된 어플리케이션이 있다면, `ps:rebuildall`를 하지 마시고, 
> 배포된 어플리케이션의 `git`을 수동으로 각각 `ps:rebuild` 해주시기 바랍니다.
>
> ```
> dokku ps:rebuild APP
> ```
>
> `tags`나 `tar`으로 배포한 앱들의 재빌드와 관련한 인스트럭션은 
> [images documentation](/docs/deployment/methods/images.md)와 [tar documentation](/docs/deployment/methods/tar.md)를 참고하세요.


### 소스로 업그레이드 하기

소스로 업그레이드 하시려면 아래의 명령어를 입력하세요:

```shell
dokku --quiet apps | xargs -L1 dokku ps:stop # 실행중인 앱들을 중단
cd ~/dokku
git pull --tags origin master

# 소스로 업그레이드
sudo DOKKU_BRANCH=master make install

# debian 패키지 기반의 업그레이드
sudo make install
dokku ps:rebuildall # 모든 앱들을 다시 빌드
```

Herokuish를 소스로 업그레이드 하시려면 아래의 명령어를 사용하세요:

```shell
cd /tmp
git clone https://github.com/gliderlabs/herokuish.git
cd herokuish
git pull origin master
IMAGE_NAME=gliderlabs/herokuish BUILD_TAG=latest VERSION=master make -e build-in-docker
```
