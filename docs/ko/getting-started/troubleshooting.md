# 트러블 슈팅

> 0.17.0 기준 새로운 기능

```
trace:on                                       # trace모드 on 
trace:off                                      # trace모드 off
```

## Trace 모드

기본적으로 Dokku는 명령어 실행 결과의 출력량을 제한합니다. trace 모드를 활성화하면 더 자세한 출력을 볼 수 있습니다. trace 모드는 bash 플러그인에 대해`set -x` 플래그를 설정하는 반면, 다른 플러그인은 환경 변수`DOKKU_TRACE`를 존중하고 적절하게 다르게 로깅합니다. 플러그인이 예기치 않은 명령을 실행하고 있는 위치를 확인하고 싶을 때 trace 모드를 유용하게 활용할 수 있습니다.

trace 모드를 활성화하기 위해서는 `trace:on` 인스트럭션을 입력하세요.

```shell
dokku trace:on
```

```
-----> Enabling trace mode
```

trace 모드를 비활성화 하려면 `trace:off` 인스트럭션을 입력하세요.

```shell
dokku trace:off
```

```
-----> Disabling trace mode
```

## 자주 발생하는 문제들

__증상:__ 앱을 배포했지만 nginx 기본 페이지만 보여요.

__해결책:__

해당 문제는 주로 nginx의 새로운 버전에 의해 발생합니다. 우선 다음의 명령어를 입력하고 결과를 확인해 보세요: 

```shell
nginx -t
## nginx: [emerg] could not build the server_names_hash, you should increase server_names_hash_bucket_size: 32
```

만약 위와 유사한 출력을 얻었다면 `/etc/nginx/nginx.conf`에서 `http` 섹션의 마지막에 다음을 추가하세요:

```nginx
http {
    (... existing content ...)
    server_names_hash_bucket_size 64;
    (...)
}
```

`server_names_hash_bucket_size` 설정은 도메인 네임의 최대 길이를 정의합니다.
`server_names_hash_bucket_size 64`은 도메인 네임을 최대 64자까지 허용함을 의미합니다. 더 긴 도메인 네임이 필요하다면 128자까지 설정 가능합니다.

파일을 저장하고 nginx을 재실행 하십시오:

```shell
/etc/init.d/nginx stop
## * Stopping nginx nginx                                        [ OK ]
/etc/init.d/nginx start
## * Starting nginx nginx                                        [ OK ]
```

***

__증상:__ 앱을 배고하고 싶은데 다음과 같은 에러가 나와요.

    ! [remote rejected] master -> master (pre-receive hook declined)

__해결책:__

`remote rejected`는 오류에 대한 자세한 설명이 되지 못합니다. trace 모드를 활성화하고 디버깅 해보시기 바랍니다. 만약 디버깅으로도 해결할 수 없다면, 로그 전문을 포함하는 [gist](https://gist.github.com)를 생성하고 이슈를 남겨주세요.

***

__증상:__ (Dokku 트레이스 모드를 활성화한 후에) 빌드 단계에서 위의 문제를 겪고 있어요.

빌드 단계에서 발생하는 문제들은 대부분 일시적인 (원격 혹은 로컬의) 네트워크 문제로 발생합니다.

__해결책 (간단하지만 유용한 트러블 슈팅 방법):__

build 단계에서 실패한 컨테이너 이미지를 확인하세요.(예시의 경우 `077581956a92`가 해당)

```shell
docker ps -a  | grep build
## 94d9515e6d93        077581956a92                "/build"       29 minutes ago      Exited (0) 25 minutes ago                       cocky_bell
```

해당 이미지에 대해 새로운 컨테이너를 생성해 컨테이너 내에서 인터넷에 액세스할 수 있는지 확인해보거나 실패했던 명령어를 다시 입력해보세요.

```shell
docker run -ti 077581956a92 /bin/bash
curl -s -S icanhazip.com
## 192.168.0.1
curl http://s3pository.heroku.com/node/v0.10.30/node-v0.10.30-linux-x64.tar.gz -o node-v0.10.30-linux-x64.tar.gz
tar tzf node-v0.10.30-linux-x64.tar.gz
## ...
```

종종 (특히 DigitalOcean에서) 재배포가 이러한 일시적인 문제의 해결이 되기도 합니다. DNS resolvers가 다른 네트워크로 바꿀 때에도 이러한 문제가 발생할 수 있습니다. 이런 경우 다음의 명령어를 이용해 `resolv.conf`를 수정하세요.

```shell
resolvconf -u
```

https://github.com/dokku/dokku/issues/841와 https://github.com/dokku/dokku/issues/649.를 참고해 주시기 바랍니다.

***

__증상:__ 앱을 배포하고 싶은데 Git 사용자 비밀번호를 요구하고 에러가 나요.

    fatal: 'NAME' does not appear to be a git repository
    fatal: Could not read from remote repository.

__해결책:__

SSH 프라이빗 키에 대해서 찾을 수 없는 경우 SSH 키를 대신하기 위해 비밀번호를 요구합니다. 이 문제는 `sshcommand acl-add`로 등록했던 퍼블릭 키와 짝이 되는 프라이빗 키가 `~/.ssh/id_rsa`에 존재하지 않기 때문입니다.

도메인 네임에 대한 올바른 키를 SSH로 지정해야합니다. `~ / .ssh / config`에 다음을 추가하십시오

```ini
Host DOKKU_HOSTNAME
  IdentityFile "~/.ssh/KEYNAME"
```

[issue #116](https://github.com/dokku/dokku/issues/116)를 참조하세요.

***

__증상:__ 오류없이 앱을 배포했지만 앱에 접속하려고 하면 **Bad Gateway**라는 에러가 떠요.

__해결책:__

대부분의 경우 앱들은 지정포트와 별개로 `process.env.PORT`라는 포트가 필요합니다.

원하는 포트로 지정하려면 다음과 같이 작성해야 합니다:

```javascript
var port = process.env.PORT || 3000
```

https://github.com/dokku/dokku/issues/282를 참조하세요.

***

__증상:__ 인터넷 연결이 느려 배포에 실패하고, `gzip: stdin: unexpected end of file`라는 메세지를 출력합니다.

__해결책:__

If you see output similar this when deploying:
만약 배포할 때 다음과 같은 출력이 나온다면:

```
 Command: 'set -o pipefail; curl --fail --retry 3 --retry-delay 1 --connect-timeout 3 --max-time 30 https://s3-external-1.amazonaws.com/heroku-buildpack-ruby/ruby-2.0.0-p451-default-cache.tgz -s -o - | tar zxf -' failed unexpectedly:
 !
 !     gzip: stdin: unexpected end of file
 !     tar: Unexpected EOF in archive
 !     tar: Unexpected EOF in archive
 !     tar: Error is not recoverable: exiting now
```

느린 연결 때문에 빌드팩을 패치해야 하는 curl 명령이 너무 오래 걸린다는 뜻입니다. 기본 값(커넥션 타임아웃 : 90초, 최대 수행 시간 : 600 초)을 수정하려면 다음과 같이 환경변수의 값을 수정하세요:

```shell
dokku config:set --global CURL_TIMEOUT=1200
dokku config:set --global CURL_CONNECT_TIMEOUT=180
```

https://github.com/dokku/dokku/issues/509를 참조하세요.

타임아웃이 원인이 아니라면 config에 `SSL_CERT_FILE`라는 이름으로 값을 설정한 게 있는지 확인해 보세요. `SSL_CERT_FILE`로 config를 설정하면 빌드팩의 dependency 다운로드 기능이 방해되므로, `MY_APP_SSL_CERT_FILE` 등의 이름으로 변경하시길 바랍니다.
***

__증상:__ `Killed`라는 메세지를 출력하며 빌드에 실패합니다.

__해결책:__

이 증상은 보통 서버의 메모리 부족으로 발생합니다. 이 문제를 해결하기 위해선 RAM을 더 추가하거나 swap space를 설정해 보세요. 다음은 2 GB의 swap space를 생성하는 명령어입니다.

```shell
sudo install -o root -g root -m 0600 /dev/null /swapfile
dd if=/dev/zero of=/swapfile bs=1k count=2048k
mkswap /swapfile
swapon /swapfile
echo "/swapfile       swap    swap    auto      0       0" | sudo tee -a /etc/fstab
sudo sysctl -w vm.swappiness=10
echo vm.swappiness = 10 | sudo tee -a /etc/sysctl.conf
```

***

__증상:__ 오류없이 배포에 성공했지만 앱을 실행하려고 하면 커넥션 타임아웃이 됩니다.

__해결책:__

UFW(Ubuntu 16.04 등 몇몇 OS에서는 기본적으로 사용하도록 되어 있음)과 같은 방화벽이 설정되어 있다면 발생할 수 있는 문제입니다. 방화벽이 작동하고 있는지는 아래의 명령어를 통해 확인할 수 있습니다:

```shell
sudo ufw status
```

위의 명령어 실행 결과가 `Status: active`이고 해당하는 port 번호가 출력된다면, UFW가 활성화 되어있어 언급했던 증상의 원인이 됩니다. UFW를 비활성화하려면 아래의 명령어를 입력하세요:

```shell
sudo ufw disable
```

***

__증상:__ 서버가 잘못된 응답을 보내거나 보안 연결을 제공 할 수 없어 응용 프로그램에 연결할 수 없습니다.

__해결책:__

해당 증상은 앱의 설정 문제이지 Dokku의 문제는 아닙니다. 앱 보안 연결(HSTS)을 하도록 구성되어 있지만 앱에 SSL을 설정하지 않은 경우에 발생할 수 있는 문제입니다.

`application.rb`나 `environmnents/production.rb`에서 HSTS을 시행하도록 하는 `configure.force_ssl = true`라는 라인을 삭제하고 재배포 해보시기 바랍니다.

이렇게 해서 문제가 해결되었다면, 앞으로를 위해 [configuring SSL](http://dokku.viewdocs.io/dokku/configuration/ssl/)을 고려해 보세요.
