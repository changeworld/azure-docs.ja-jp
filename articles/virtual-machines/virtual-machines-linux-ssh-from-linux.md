<properties 
	pageTitle="Linux と Mac で SSH を使用する | Microsoft Azure" 
	description="Azure で、リソース マネージャーと従来のデプロイメント モデル用に、Linux と Mac の SSH 鍵を生成し、使用します。" 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2016" 
	ms.author="rasquill"/>

#Azure 上の Linux または Mac における SSH の使用方法

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-ssh-from-linux.md)


このトピックでは、**ssh-keygen** と **openssl** を Linux や Mac で使用し、**ssh-rsa** 形式または **.pem** 形式のファイルを作成して使用し、Linux を基盤とする Azure VM と安全に通信する方法について紹介します。リソース マネージャーのデプロイメント モデルによる Linux ベースの Azure Virtual Machines の作成は新しいデプロイメントの場合に推奨され、*ssh-rsa* タイプの公開鍵ファイルや文字列が使われます (デプロイメント クライアントにもよります)。[Azure ポータル](https://portal.azure.com)では現在のところ、従来のデプロイメントでも Resource Manager デプロイメントでも、**ssh-rsa** 形式の文字列のみを受け取ります。


> [AZURE.NOTE] お時間がございましたら、Azure Linux VM ドキュメントの向上のため、こちらの[アンケート](https://aka.ms/linuxdocsurvey)にご回答ください。いただいた回答は、今後のドキュメントの改善に活用させていただきます。


> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)] Windows コンピューターが Azure で Linux VM と安全に通信するためにこのような種類のファイルを作成する方法については、「[Windows で SSH 鍵を使用する](virtual-machines-linux-ssh-from-windows.md)」を参照してください。

## 必要なファイル

Azure の基本的 ssh 設定には **ssh-rsa** の 2048 ビットの公開/秘密鍵ペア (既定を変更しない限り、**ssh-keygen** にはこれらのファイルが **~/.ssh/id\_rsa** と **~/.ssh/id-rsa.pub** として保存されます) と従来のポータルの従来のデプロイメント モデルで使用するために **id\_rsa** 秘密鍵ファイルから生成された `.pem` ファイルが含まれます。

次にデプロイメント シナリオとそれぞれのシナリオで使用されるファイルの種類を紹介します。

1. **ssh-rsa** キーは、デプロイメント モデルに関係なく、[Azure ポータル](https://portal.azure.com)を使用したどのデプロイでも必須です。
2. .pem ファイルは[従来のポータル](https://manage.windowsazure.com)で VM を作成する際に必須です。.pem ファイルは、[Azure CLI](../xplat-cli-install.md) を使用する従来のデプロイメントでもサポートされます。

## SSH で使用する鍵の作成

SSH キーが既にある場合は、Azure VM の作成時に公開キー ファイルを渡します。

ファイルを作成する必要がある場合:

1. **ssh-keygen** と **openssl** の実装が最新のものであることを確認してください。これはプラットフォームによって異なります。

	- Mac の場合、[Apple の製品セキュリティ Web サイト](https://support.apple.com/HT201222)にアクセスし、必要に応じて適切な更新プログラムを選択してください。
	- Ubuntu、Debian、Mint など、Debian 基盤の Linux 製品の場合:

			sudo apt-get install --upgrade-only openssl

	- CentOS や Oracle Linux など、RPM 基盤の Linux 製品の場合:

			sudo yum update openssl

	- SLES と OpenSUSE の場合:

			sudo zypper update openssl

2. **ssh-keygen** を使用して 2048 ビット RSA 公開/秘密鍵ファイルを作成し、そのファイルに特定の場所や名前を使用するのでなければ、`~/.ssh/id_rsa` の既定の場所と名前をそのまま使用します。基本的なコマンド:

		ssh-keygen -t rsa -b 2048 

	通常、**ssh-keygen** の実装でコメントが追加されます。多くの場合、コンピューターのユーザー名とホスト名です。`-C` オプションを利用し、特定のコメントを指定できます。

3. 従来のポータルを利用できるように、`~/.ssh/id_rsa` ファイルから .pem ファイルを作成します。次のように **openssl** を使用します。

		openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

	別の秘密鍵ファイルから .pem ファイルを作成する場合、`-key` 引数を変更します。

> [AZURE.NOTE] 従来のデプロイメント モデルでデプロイされたサービスを管理する場合、**.cer** 形式ファイルを作成し、ポータルにアップロードすることもあります。ただし、その場合、**ssh** は使われず、この記事の主題である Linux VMS に接続しません。Linux または Mac で、.pem ファイルを DER エンコードされた X509 証明書ファイルに変換するには、「<br /> openssl x509 -outform der -in myCert.pem -out myCert.cer」と入力します。

## 既にある SSH 鍵を使用する

特にリソース マネージャー デプロイメント モデルとプレビュー ポータルで、すべての新しい作業に ssh-rsa (`.pub`) キーを使用できます。従来のポータルを利用する場合、状況によっては、鍵から `.pem` ファイルを作成する必要があります。

## 公開鍵ファイルで VM を作成する

必要なファイルを作成したら、さまざまな方法で VM を作成し、公開/秘密鍵の交換で安全に接続できます。ほとんどすべての状況で、特にリソース マネージャー デプロイメントの利用で、ssh 鍵ファイル パスまたはファイルのコンテンツを文字列として求められたら、.pub ファイルを渡します。

### 例: id\_rsa.pub ファイルで VM を作成する

最も一般的な使用方法は VM を強制的に作成するか、VM を作成するためのテンプレートをアップロードするときです。次のコード例では、パブリック ファイル名 (この場合、既定の `~/.ssh/id_rsa.pub` ファイル) を `azure vm create` コマンドに渡し、Azure で新しい安全な Linux VM が作成されます。(リソース グループやストレージ アカウントなどの他の引数は以前に作成されました)。この例では、Resource Manager デプロイ方法を使用しているので、Azure CLI が `azure config mode arm` を使用して設定されていることを確認します。

	azure vm create \
	--nic-name testnic \
	--public-ip-name testpip \
	--vnet-name testvnet \
	--vnet-subnet-name testsubnet \
	--storage-account-name computeteststore 
	--image-urn canonical:UbuntuServer:14.04.4-LTS:latest \
	--username ops \
	-ssh-publickey-file ~/.ssh/id_rsa.pub \
	testrg testvm westeurope linux

次の例では、リソース マネージャー テンプレートと Azure CLI と共に **ssh-rsa** 形式を利用し、文字列として `~/.ssh/id_rsa.pub` のユーザー名とコンテンツで保護される Ubuntu VM が作成されます。(この場合、公開鍵の文字列が読みやすいように短くされます。)

	azure group deployment create \
	--resource-group test-sshtemplate \
	--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
	--name mysshdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	testnewStorageAccountName: testsshvmtemplate3
	adminUserName: ops
	sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQA+/L+rHIjz+nXTzxApgnP+iKDZco9 user@macbookpro
	dnsNameForPublicIP: testsshvmtemplate
	location: West Europe
	vmName: sshvm
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "mysshdeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mysshdeployment
	data:    ResourceGroupName  : test-sshtemplate
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-08T00:12:12.2529678Z
	data:    Mode               : Incremental
	data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	data:    ContentVersion     : 1.0.0.0
	data:    Name                   Type    Value

	data:    newStorageAccountName  String  testtestsshvmtemplate3
	data:    adminUserName          String  ops
	data:    sshKeyData             String  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAkek3P6V3EhmD+xP+iKDZco9 user@macbookpro
	data:    dnsNameForPublicIP     String  testsshvmtemplate
	data:    location               String  West Europe
	data:    vmSize                 String  Standard_A2
	data:    vmName                 String  sshvm
	data:    ubuntuOSVersion        String  14.04.4-LTS
	info:    group deployment create command OK


### 例: .pem ファイルで VM を作成する

次の例のように、クラシック ポータルまたはクラシック デプロイ モード (`azure config mode asm`) と `azure vm create` で .pem ファイルを使用できます。

	azure vm create \
	-l "West US" -n testpemasm \
	-P -t myCert.pem -e 22 \
	testpemasm \
	b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160406-ja-JP-30GB \
	ops
	info:    Executing command vm create
	warn:    --vm-size has not been specified. Defaulting to "Small".
	+ Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160406-ja-JP-30GB
	+ Looking up cloud service
	info:    cloud service testpemasm not found.
	+ Creating cloud service
	+ Retrieving storage accounts
	+ Configuring certificate
	+ Creating VM
	info:    vm create command OK


## VM に接続する

**ssh** コマンドは、ログオンするためのユーザー名、コンピューターのネットワーク アドレス、アドレスに接続するポート、その他さまざまな特殊バリエーションを受け取ります。(**ssh** の詳細については、こちらの [Secure Shell に関する記事](https://en.wikipedia.org/wiki/Secure_Shell)などを参考にしてください)。

サブドメインとデプロイメント場所を指定しているだけの場合、リソース マネージャー デプロイメントの一般的な使用方法は次のようになります。

	ssh user@subdomain.westus.cloudapp.azure.com -p 22

あるいは、従来のデプロイメント クラウド サービスに接続している場合、使用するアドレスは次のようになります。

	ssh user@subdomain.cloudapp.net -p 22

アドレス形式は変更されることがあるので (いつでも IP アドレスを使用できます。あるいは、カスタムのドメイン名を割り当てることがあります。)、自分の Azure VM のアドレスを見つける必要があります。

### 従来のデプロイメントで Azure VM SSH を見つける

`azure vm show` コマンドと VM 名を使用し、VM と従来のデプロイメント モデルで使用するアドレスを見つけることができます。

	azure vm show testpemasm
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "testpemasm.cloudapp.net"
	data:    Location "West US"
	data:    VMName "testpemasm"
	data:    IPAddress "100.116.160.154"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Small"
	data:    Image "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ja-JP-30GB"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "testpemasm-testpemasm-0-201510102050230517"
	data:    OSDisk mediaLink "https://portalvhds4blttsxgjj1rf.blob.core.windows.net/vhd-store/testpemasm-2747c9c432b043ff.vhd"
	data:    OSDisk sourceImageName "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-ja-JP-30GB"
	data:    OSDisk operatingSystem "Linux"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "40.83.178.221"
	data:    VirtualIPAddresses 0 name "testpemasmContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 localPort 22
	data:    Network Endpoints 0 name "ssh"
	data:    Network Endpoints 0 port 22
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "40.83.178.221"
	data:    Network Endpoints 0 enableDirectServerReturn false
	info:    vm show command OK

### リソース マネージャー デプロイメントで Azure SSH アドレスを見つける

	azure vm show testrg testvm
	info:    Executing command vm show
	+ Looking up the VM "testvm"
	+ Looking up the NIC "testnic"
	+ Looking up the public ip "testpip"

ネットワーク プロファイル セクションを調べます。

	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-21-8E-AE
	data:          Provisioning State        :Succeeded
	data:          Name                      :testnic
	data:          Location                  :westeurope
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.115.48.189
	data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
	data:
	data:    Diagnostics Instance View:
	info:    vm show command OK

VM の作成時に既定の SSH ポート 22 を使用しなかった場合、次のように、`azure network nsg show` コマンドを利用し、受信ルールが与えられているポートを見つけることができます。

	azure network nsg show testrg testnsg
	info:    Executing command network nsg show
	+ Looking up the network security group "testnsg"
	data:    Id                              : /subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testnsg
	data:    Name                            : testnsg
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westeurope
	data:    Provisioning state              : Succeeded
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    testnsgrule                    *                  *            *               22                Tcp       Inbound    Allow   1000
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

### 例: .pem 鍵と従来のデプロイメントを使用した SSH セッションの出力

`~/.ssh/id_rsa` ファイルから作成した .pem ファイルを利用して VM を作成した場合、その VM に直接 ssh 接続できます。その場合、`~/.ssh/id_rsa` で証明書ハンドシェイクに秘密鍵が使用されることに注意してください。(VM 作成プロセスでは、.pem から公開鍵が計算されて、その ssh-rsa 形式が `~/.ssh/authorized_users` に格納されます)。 接続は次の例のようになります。

	ssh ops@testpemasm.cloudapp.net -p 22
	The authenticity of host 'testpemasm.cloudapp.net (40.83.178.221)' can't be established.
	RSA key fingerprint is dc:bb:e4:cc:59:db:b9:49:dc:71:a3:c8:37:36:fd:62.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'testpemasm.cloudapp.net,40.83.178.221' (RSA) to the list of known hosts.
	
    Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.19.0-49-generic x86_64)
	
	* Documentation:  https://help.ubuntu.com/

    System information as of Fri Apr 15 18:51:42 UTC 2016

    System load: 0.31              Memory usage: 2%   Processes:       213
    Usage of /:  42.1% of 1.94GB   Swap usage:   0%   Users logged in: 0

    Graph this data and manage this system at:
    https://landscape.canonical.com/

    Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

    0 packages can be updated.
	0 updates are security updates.
	
	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.
	
	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.


## 接続に問題がある場合

「[SSH 接続のトラブルシューティング](virtual-machines-linux-troubleshoot-ssh-connection.md)」の提案を参照し、状況を解消できるか確認できます。

## 次のステップ
 
これで VM に接続できたので、選択したディストリビューションを必ず更新してから使用を続けます。

<!---HONumber=AcomDC_0817_2016-->