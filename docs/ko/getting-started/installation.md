# Dokku 시작하기

## Dokku가 무엇인가요?

Dokk는 단일 서버에서 실행되는 확장가능한 오픈소스 플랫폼 서비스입니다.

Dokku를 시작하기 위해서는 다음과 같은 최소 환경을 갖추어야 합니다.

- 새로 설치된 [Ubuntu x64 - 현재 지원가능한 모든 버전](https://www.ubuntu.com/download), [Debian 8.2 x64](https://www.debian.org/distrib/) 또는 FQDN이 설정<sup>[1]</sup>된 [CentOS 7 x64](https://www.centos.org/download/) *(시험중)*
- 최소 1 GB의 메모리 <sup>[2]</sup>

선택적으로 호스트 IP에 대한 도메인 네임을 설정할 수 있습니다. (필수사항은 아님)

Dokku는 새로운 VM 환경에서 설치할 것을 권장하며, bootstrap을 통해 설치할 경우 모든 dependency들을 설치해야 합니다.

### 최신 안정화 버전의 Dokku 설치하기

#### 1. Dokku 설치하기

가장 최신의 안정화 버전을 설치하려면, 아래의 명령어를 실행하세요 : 

```shell
# debian 사용자의 경우 apt-get를 이용해 설치하세요
wget https://raw.githubusercontent.com/dokku/dokku/v0.18.3/bootstrap.sh;
sudo DOKKU_TAG=v0.18.3 bash bootstrap.sh
```

인터넷 연결 환경에 따라 설치는 5-10분이 소요됩니다.

Debian 8이나 Ubuntu 14.04의 사용자의 경우 패키지 매니저의 nginx 버전<sup>[3]</sup>을 확인해 주시기 바랍니다. 최신 버전이 아닐 경우 `unmet dependencies` 오류로 설치를 완료할 수 없습니다.

#### 2. SSH 키 및 가상호스트 설정하기

설치 후 브라우저에서 SSH 키 및 가상호스트를 설정할 수 있습니다. 브라우저에 호스트 IP나 추가적으로 설정했던 도메인 네임을 입려하세요.

혹은 [advanced install guide](/docs/getting-started/advanced-installation/#configuring)를 참고하여 자동설치를 진행 할 수도 있습니다.

> **주의:** 웹 설치 관리자를 통해 설치를 완료하지 않을 경우 (다른 방법으로 SSH 키 및 가상호스트를 설정했더라도) 설치 페이지를 찾고 키를 삽입하는 사용자에게 취약한 상태로 유지된다. `ps auxf | grep dokku-installer`를 통해 웹 설치 관리자가 실행중인지 확인할 수 있으며, 당신 서버의 초기화 시스템을 통해 중지할 수 있습니다 - 보통 `service dokku-installer stop` or `stop dokku-installer`의 명령어를 이용함.

> **주의:** CentOS와 Linux에서는 웹 브라우저를 통해 SSH 키 및 가상호스트를 설정할 수 없습니다. [SSH 키](/docs/deployment/user-management.md#adding-ssh-keys) 와 [가상호스트](/docs/configuration/domains.md#customizing-hostnames)를 참고하여 설치를 완료해주시기 바랍니다.

#### 3. 첫 번째 어플리케이션 배포하기

설정을 저장하면 웹 관리자가 자동으로 종료되고, Dokku 설치를 실행하거나 배포할 수 있게 됩니다.

### 다른 방법으로 설치하기

여러 이유로, 어떠한 호스팅 서비스를 이용하느냐에 따라 설치 방법이 달라질 수 있습니다. 만약 아래의 호스팅 서비스 중 하나를 사용하고 있다면 링크를 참조하여 설치해주시기 바랍니다.

- [DigitalOcean Installation Notes](/docs/getting-started/install/digitalocean.md)
- [DreamHost Cloud Installation Notes](/docs/getting-started/install/dreamhost.md)
- [Microsoft Azure Installation Notes](/docs/getting-started/install/azure.md)

더불어 위의 설치방법이 아닌 다른 방법을 원하시거나 Vagrant를 통해 설치하길 원하신다면 아래의 링크를 참조하세요.

- [Debian Package Installation Notes](/docs/getting-started/install/debian.md)
- [Docker-based Installation Notes](/docs/getting-started/install/docker.md)
- [RPM Package Installation Notes](/docs/getting-started/install/rpm.md)
- [Vagrant Installation Notes](/docs/getting-started/install/vagrant.md)
- [Advanced Install Customization](/docs/getting-started/advanced-installation.md)

---

- <sup>[1]: fqdn set이 설정이 되어 있는지 확인하시려면, `sudo hostname -f` 명령어를 입력해보세</sup>
- <sup>[2]: 만약 1GB 미만의 메모리밖에 확보하지 못했다면, [이 방법](/docs/ge요tting-started/advanced-installation.md#vms-with-less-than-1gb-of-memory)을 사용해 보시기 바랍니다.</sup>
- <sup>[3]: Ubuntu 사용자라면, [nginx 레파지토리](https://www.nginx.com/resources/admin-guide/installing-nginx-open-source/)를 참고하거나 [PPA](https://launchpad.net/~nginx/+archive/ubuntu/stable)를 추가하여 1.8.0버전 이상의 nginx를 설치할 수 있습니다. HTTP/2 지원을 위해서는 1.11.5 이상 버전을 설치하세요.


