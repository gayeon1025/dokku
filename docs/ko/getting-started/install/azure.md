# Microsoft Azure Installation Notes

1. 아직 SSH 키 쌍이 없다면 [SSH 키 생성하기](https://help.github.com/articles/generating-ssh-keys/).

2. [Azure에 Dokku 배포하기](https://github.com/azure/azure-quickstart-templates/tree/master/dokku-vm)페이지에서  **Deploy to Azure**를 클릭하세요.

3. 고유한 스토리지 계정 이름 및 퍼블릭 IP를 가리킬 서브 도메인의 이름을 입력하세요. `sshKeyData` 파라미터의 경우 위에서 생성한 *퍼블릭* 키를 붙여넣기 하세요. 입력을 완료하고 나면 몇 분 이내에 Dokku 인스턴스가 배포될 것입니다.

4. 브라우저에서, `http://[dnsNameForPublicIP].[location].cloudapp.azure.com`를 입력하세요. `[dnsNameForPublicIP]`와 `[location]`은 템플릿을 배포할 때 사용한 템플릿 파라미터입니다.

5. `ssh-keygen`을 사용하여 배포를 위해 *새로운* 공개/개인 키 쌍을 생성하고 일반적으로하는 것처럼 Dokku 설정을 완료하세요 (*주의* 첫 번째 단계에서 생성 한 것과 동일한 것을 사용하지 마십시오). **Use Virtual Host Naming**을 클릭하고 들어가 [Namecheap](http://namecheap.com)과 같은 곳에서 구매한 *public DNS name*에 대한 **Hostname**을 설정하세요. [xip.io](http://xip.io/)을 이용한다면 `[yourAzurePublicIP].xip.io`로(예를 들어 `44.44.44.44`라는 퍼블릭 IP라면 `44.44.44.44.xip.io`와 같이) 무료 *public DNS name*도 이용할 수 있습니다. 
