---
title: "サービス管理モードでの Azure CLI コマンド |Microsoft Docs"
description: "Azure コマンドライン インターフェイス (CLI) コマンドをサービス管理モードで使用して、クラシック デプロイ モデルのデプロイを管理する方法について説明します"
services: virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: b9ccdeed-0ab8-4de2-937f-4046c7918bd8
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 01/03/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 9a2ab28a2dc456d5f21242ded1d348f411d3d994
ms.openlocfilehash: c8cc44cf132bb82c217a81ab94f27ed391a4248d


---
# <a name="azure-cli-commands-in-azure-service-management-asm-mode"></a>Azure サービス管理 (asm) モードでの Azure CLI コマンド


この記事では、クラシック デプロイ モデルでの Azure リソースの作成と管理に一般的に使用される Azure CLI コマンドの構文とオプションを説明します。 これらのコマンドにアクセスするには、Azure サービス管理 (asm) モードで CLI を実行します。 これは完全な参照資料ではありません。ご使用の CLI バージョンで異なるコマンドやパラメーターが表示される場合もあります。 

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../articles/azure-resource-manager/resource-manager-deployment-model.md)の&2; 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルの CLI コマンドに関する記事](virtual-machines/azure-cli-arm-commands.md)を確認し、CLI を使用して、クラシックから Resource Manager モデルへの[リソースの移行](virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)を行うこともできます。
>
>Resource Manager モードでの操作は、次世代のマルチプラットフォーム CLI である [Azure CLI 2.0 (プレビュー)](/cli/azure/install-az-cli2) で試すこともできます。
>


最初に、[Azure CLI をインストール](xplat-cli-install.md)し、[Azure サブスクリプションに接続](xplat-cli-connect.md)します。

コマンド ラインの最新のコマンド構文とオプションを確認するには「`azure help`」と入力します。特定のコマンドのヘルプを表示するには、「`azure help [command]`」と入力します。 ドキュメントには、特定の Azure サービスを作成および管理するための CLI の例もあります。

オプション パラメーターは、ブラケットで囲んで表記しています (例 `[parameter]`)。 その他のパラメーターはすべて指定する必要があります。

ここに記載している、コマンド固有のオプション パラメーターに加えて、要求オプションや状態コードなどの詳細出力の表示に使用できるオプション パラメーターが&3; つあります。 `-v` パラメーターでは詳細な出力を、`-vv` パラメーターではより詳細な出力を得ることができます。 `--json` オプションを使用すると、結果が未整形の json 形式で出力されます。

## <a name="setting-asm-mode"></a>ASM モードの設定
次のコマンドを使用して Azure CLI サービス管理モード コマンドを有効にします。

    azure config mode asm

> [!NOTE]
> CLI の Azure リソース マネージャー モードと Azure サービス管理モードは互いに排他的です。 つまり、どちらか一方のモードで作成されたリソースは、他方のモードは管理できません。
> 
> 

## <a name="manage-your-account-information-and-publish-settings"></a>アカウント情報と発行設定の管理
CLI でアカウントに接続する方法の&1; つは、Azure サブスクリプションの情報を使用するものです  (他の方法については、「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](xplat-cli-connect.md)」を参照してください)。この情報は、以下に説明するとおり、Azure クラシック ポータルから発行設定ファイルとして入手できます。 発行設定ファイルは、CLI の以降の操作で永続的なローカル構成設定としてインポートすることができます。インポートすると、以降の操作ではこの発行設定ファイルが使用されます。 発行設定のインポートは&1; 回だけ行う必要があります。

**account download [options]**

このコマンドは、ブラウザーを起動して、Azure クラシック ポータルから .publishsettings ファイルをダウンロードします。

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**account import [options] &lt;file>**

このコマンドにより、publishsettings ファイルまたは証明書がインポートされ、以降のセッションで使用できるようになります。

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

> [!NOTE]
> publishsettings ファイルには、複数のサブスクリプションの詳細 (サブスクリプション名と ID) を含めることができます。 publishsettings ファイルをインポートすると、最初のサブスクリプションが既定の説明として使用されます。 別のサブスクリプションを使用するには、次のコマンドを実行します。
> <code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>
> 
> 

**account clear [options]**

このコマンドは、インポート済みの保存されている publishsettings を削除します。 このマシンでのツールの使用を完了した後のセッションで自分のアカウントによってツールが使われないようにするには、次のコマンドを使用します。

    ~$ azure account clear
    Clearing account info.
    info:   OK

**account list [options]**

インポートされたサブスクリプションを一覧表示します

    ~$ azure account list
    info:    Executing command account list
    data:    Name                                    Id
           Current
    data:    --------------------------------------  -------------------------------
    -----  -------
    data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
    data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
    data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account set [options] &lt;subscription&gt;**

現在のサブスクリプションを設定します

### <a name="commands-to-manage-your-affinity-groups"></a>アフィニティ グループの管理用コマンド
**account affinity-group list [options]**

このコマンドは、Azure のアフィニティ グループを一覧表示します。

アフィニティ グループは、仮想マシンのグループが複数の物理マシン上にわたって存在する場合に設定できます。 アフィニティ グループでは、ネットワークの待ち時間を減らすため、複数の物理マシンをできる限り近くに配置します。

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**account affinity-group create [options] &lt;name&gt;**

このコマンドは、アフィニティ グループを作成します。

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**account affinity-group show [options] &lt;name&gt;**

このコマンドは、アフィニティ グループの詳細を表示します。

    ~$ azure account affinity-group show opentec
    info:    Executing command account affinity-group show
    + Getting affinity groups
    data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
    data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
    data:    Name "opentec"
    data:    Label "b3BlbnRlYw=="
    data:    Description $ i:nil "true"
    data:    Location "West US"
    data:    HostedServices ""
    data:    StorageServices ""
    data:    Capabilities Capability 0 "PersistentVMRole"
    data:    Capabilities Capability 1 "HighMemory"
    info:    account affinity-group show command OK

**account affinity-group delete [options] &lt;name&gt;**

このコマンドは、指定されたアフィニティ グループを削除します。

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec? [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

### <a name="commands-to-manage-your-account-environment"></a>アカウント環境の管理用コマンド
**account env list [options]**

アカウント環境の一覧です。

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Name
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**account env show [options] [environment]**

アカウント環境の詳細を表示します。

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
    info:    account env show command OK

**account env add [options] [environment]**

このコマンドは、アカウントに環境を追加します。

**account env set [options] [environment]**

このコマンドは、アカウント環境を設定します。

**account env delete [options] [environment]**

このコマンドは、指定された環境をアカウントから削除します。

## <a name="commands-to-manage-your-classic-virtual-machines"></a>クラシック仮想マシンの管理用コマンド
次の図は、クラシック Azure の仮想マシンが Azure クラウド サービスの運用デプロイ環境でホストされるしくみを示しています。

![Azure の技術解説図](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**create-new** は BLOB ストレージ (この図では e:\) にドライブを作成します。**attach** は作成済みでまだ接続していないディスクを、仮想マシンに接続します。

**vm create [options] &lt;dns-name> &lt;image> &lt;userName> [password]**

このコマンドは、Azure の仮想マシンを作成します。 既定では、各仮想マシンは (VM) はそれぞれのクラウド サービス上に作成されます。 ただし、ここで説明しているように -c オプションを使用すると、仮想マシンを既存のクラウド サービスに追加するように指定できます。

vm create コマンドは、Azure クラシック ポータルと同様、特定の運用デプロイ環境上にのみ仮想マシンを作成します。 クラウド サービスのステージング デプロイ環境に仮想マシンを作成するオプションはありません。 サブスクリプションに既存の Azure ストレージ アカウントが含まれていない場合、このコマンドは Azure ストレージ アカウントを作成します。

--location パラメーターで場所を指定するか、--affinity-group パラメーターでアフィニティ グループを指定します。 どちらも指定しない場合は、有効な場所の一覧から&1; つを選択するように求められます。

パスワードは、8 文字以上 123 以下で指定し、仮想マシンで使用しているオペレーティング システムに適用されているパスワードの複雑さの要件を満たす必要があります。

(一般的なケースのように) デプロイした Linux 仮想マシンの管理に SSH を使用するには、仮想マシンを作成するときに -e オプションを使用して SSH を有効にします。 仮想マシンを作成した後に SSH を有効にすることはできません。

Windows 仮想マシンでは、エンドポイントとしてポート 3389 を追加することによって、後から RDP を有効にできます。

このコマンドでは、次のオプション パラメーターがサポートされています。

**-c, --connect** ホスティング サービスで作成済みのデプロイ内に仮想マシンを作成します。 -vmname をこのオプションと使用しない場合、新しい仮想マシンの名前が自動的に生成されます。<br />
**-n, --vm-name** 仮想マシンの名前を指定します。 このパラメーターは既定でホスティング サービス名を参照します。 -vmname を指定しなかった場合、新しい仮想マシンの名前は &lt;service-name>&lt;id> になります。&lt;id> は、サービス内にある既存の仮想マシンの数に 1 を足した数です。 たとえば、このコマンドを使用して、1 つの既存の仮想マシンのあるホスティング サービス MyService に仮想マシンを追加した場合、新しい仮想マシンは MyService2 という名前になります。<br />
**-u, --blob-url** 仮想マシン システム ディスクの作成元となる BLOB ストレージの URL を指定します。 <br />
**-z, --vm-size** 仮想マシンのサイズを指定します。 有効な値を次に示します。"ExtraSmall"、"Small"、"Medium"、"Large"、"ExtraLarge"、"A5"、"A6"、"A7"、"A8"、"A9"、"A10"、"A11"、"Basic_A0"、"Basic_A1"、"Basic_A2"、"Basic_A3"、"Basic_A4"、"Standard_D1"、"Standard_D2"、"Standard_D3"、"Standard_D4"、"Standard_D11"、"Standard_D12"、"Standard_D13"、"Standard_D14"、"Standard_DS1"、"Standard_DS2"、"Standard_DS3"、"Standard_DS4"、"Standard_DS11"、"Standard_DS12"、"Standard_DS13"、"Standard_DS14"、"Standard_G1"、"Standard_G2"、"Standard_G3"、"Standard_G4"、"Standard_G55"。 既定値は "Small" です。 <br />
**-r** RDP 接続を Windows 仮想マシンに追加します。 <br />
**-e, --ssh** SSH 接続を Windows 仮想マシンに追加します。 <br />
**-t, --ssh-cert**SSH 証明書を指定します。 <br />
**-s** サブスクリプション <br />
**-o, --community**指定されるイメージはコミュニティのイメージです <br />
**-w** 仮想ネットワーク名 <br/>
**-l, --location**場所を指定します (たとえば、"North Central US")。 <br />
**-a, --affinity-group** アフィニティ グループを指定します。<br />
**-w, --virtual-network-name** 新しい仮想マシンを追加する仮想ネットワークを指定します。 仮想ネットワークは、Azure クラシック ポータルから設定および管理できます。<br />
**-b, --subnet-names** 仮想マシンを割り当てるサブネット名を指定します。

この例では、MSFT__Win2K8R2SP1-120514-1520-141205-01-en-us-30GB が、プラットフォームによって提供されるイメージです。 オペレーティング システムのイメージの詳細については、「vm image list」を参照してください。

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************
    info:   vm create command OK

**vm create-from &lt;dns-name> &lt;role-file>**

このコマンドは、JSON ロール ファイルから Azure 仮想マシンを作成します。

    ~$ azure vm create-from my-vm example.json
    info:   OK

**vm list [options]**

このコマンドは、Azure の仮想マシンの一覧を表示します。 -json オプションは、結果が raw JSON 形式で返されるように指定します。

    ~$ azure vm list
    info:   Executing command vm list
    data:   DNS Name                          VM Name      Status
    data:   --------------------------------  -----------  ---------
    data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
    info:   vm list command OK

**vm location list [options]**

このコマンドは、Azure アカウントの使用可能な場所の一覧を表示します。

    ~$ azure vm location list
    info:   Executing command vm location list
    data:   Name                   Display Name
    data:   ---------------------  ------------
    data:   Azure Preview  West US
    info:   account location list command OK

**vm show [options] &lt;name>**

このコマンドは、Azure の仮想マシンの詳細を表示します。 -json オプションは、結果が raw JSON 形式で返されるように指定します。

    ~$ azure vm show my-vm
    info:   Executing command vm show
    data:   {
    data:       InstanceSize: 'Small',
    data:       InstanceStatus: 'ReadyRole',
    data:       DataDisks: [],
    data:       IPAddress: '10.26.192.206',
    data:       DNSName: 'my-vm.cloudapp.net',
    data:       InstanceStateDetails: {},
    data:       VMName: 'my-vm',
    data:       Network: {
    data:           Endpoints: [
    data:               {
    data:                   Protocol: 'tcp',
    data:                   Vip: '65.52.250.250',
    data:                   Port: '63238' ,
    data:                   LocalPort: '3389',
    data:                   Name: 'RemoteDesktop'
    data:               }
    data:           ]
    data:       },
    data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
    data:   }
    info:   vm show command OK

**vm delete [options] &lt;name>**

このコマンドは、Azure の仮想マシンを削除します。 既定では、オペレーティング システム ディスクとデータ ディスクを作成した Azure BLOB は削除されません。 元の仮想マシンと共に BLOB も削除するには、-b オプションを指定します。

    ~$ azure vm delete my-vm
    info:   Executing command vm delete
    info:   vm delete command OK

**vm start [options] &lt;name>**

このコマンドは、Azure の仮想マシンを起動します。

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**vm restart [options] &lt;name>**

このコマンドは、Azure の仮想マシンを再起動します。

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**vm shutdown [options] &lt;name>**

このコマンドは、Azure の仮想マシンをシャットダウンします。 -p オプションを使用して、シャットダウン時にコンピューティング リソースが解放されないように指定することができます。

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**vm capture &lt;vm-name> &lt;target-image-name>**

このコマンドは、Azure の仮想マシン イメージを取得します。

仮想マシンの状態が **[停止]** の場合にのみ仮想マシン イメージをキャプチャできます。 続行する前に、仮想マシンをシャットダウンします。

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**vm export [options] &lt;vm-name> &lt;file-path>**

このコマンドは、Azure の仮想マシン イメージをファイルにエクスポートします。

    ~$ azure vm export "myvm" "C:\"
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK

## <a name="commands-to-manage-your-azure-virtual-machine-endpoints"></a>Azure の仮想マシン エンドポイントの管理用コマンド
次の図は、クラシック仮想マシンのインスタンスが複数ある一般的なデプロイのアーキテクチャを示しています。 この例では、各仮想マシンのポート 3389 が RDP アクセス用に開かれています。 また、ロード バランサーが仮想マシンへのトラフィックのルーティングで使用する内部 IP アドレス (たとえば、168.55.11.1) が、仮想マシンごとに設定されています。 この内部 IP アドレスは、仮想マシン間の通信にも使用されます。

![Azure のネットワーク図](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

仮想マシンへの外部要求は、ロード バランサーを経由します。 そのため、複数の仮想マシンのデプロイにおいては、特定の仮想マシンに対する要求は指定できません。 複数の仮想マシンがあるデプロイについては、仮想マシン (vm-port) とロード バランサー (lb-port) 間のポート マッピングを構成する必要があります。

**vm endpoint create &lt;vm-name> &lt;lb-port> [vm-port]**

このコマンドは、仮想マシンのエンドポイントを作成します。 -u または --enable-direct-server-return を使用して、このエンドポイントで Direct Server Return を有効にするかどうかを指定することもできます。既定では無効です。

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**vm endpoint create-multiple [options] &lt;vm-name> &lt;lb-port>[:&lt;vm-port>[:&lt;protocol>[:&lt;enable-direct-server-return>[:&lt;lb-set-name>[:&lt;probe-protocol>[:&lt;probe-port>[:&lt;probe-path>[:&lt;internal-lb-name>]]]]]]]] {1-*}**

複数の VM エンドポイントを作成します。

**vm endpoint delete [options] &lt;vm-name> &lt;endpoint-name>**

このコマンドは、仮想マシンのエンドポイントを削除します。

    ~$ azure vm endpoint delete my-vm http
    azure vm endpoint delete my-vm http
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**vm endpoint list &lt;vm-name>**

このコマンドは、仮想マシンのエンドポイントをすべて列挙します。 -json オプションは、結果が raw JSON 形式で返されるように指定します。

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port
    data:   ----  -------------  ----------
    data:   ssh   22             22

**vm endpoint update [options] &lt;vm-name> &lt;endpoint-name>**

このコマンドは、以下のオプションを使用して、VM エンドポイントを新しい値に更新します。

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**vm endpoint show [options] &lt;vm-name>**

このコマンドは、VM 上のエンドポイントの詳細を表示します。

    ~$ azure vm endpoint show "mycouchvm"
    info:    Executing command vm endpoint show
    + Getting virtual machines
    data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
    data:    Network Endpoints 0 LocalPort "5984"
    data:    Network Endpoints 0 Name "CouchDB_EP"
    data:    Network Endpoints 0 Port "5984"
    data:    Network Endpoints 0 Protocol "tcp"
    data:    Network Endpoints 0 Vip "168.61.9.97"
    data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
    data:    Network Endpoints 1 LocalPort "2020"
    data:    Network Endpoints 1 Name "CouchEP_2"
    data:    Network Endpoints 1 Port "2020"
    data:    Network Endpoints 1 Protocol "tcp"
    data:    Network Endpoints 1 Vip "168.61.9.97"
    data:    Network Endpoints 2 LocalPort "3389"
    data:    Network Endpoints 2 Name "RemoteDesktop"
    data:    Network Endpoints 2 Port "3389"
    data:    Network Endpoints 2 Protocol "tcp"
    data:    Network Endpoints 2 Vip "168.61.9.97"
    info:    vm endpoint show command OK

## <a name="commands-to-manage-your-azure-virtual-machine-images"></a>Azure の仮想マシン イメージの管理用コマンド
仮想マシン イメージは、構成済みの仮想マシンのキャプチャしたものであり、必要に応じて複製できます。

**vm image list [options]**

このコマンドは、仮想マシン イメージの一覧を表示します。 イメージには、Microsoft が作成したイメージ (プレフィックスは "MSFT")、サード パーティが作成したイメージ (プレフィックスはベンダーの名前)、およびユーザーが作成したイメージの&3; 種類があります。 イメージを作成するには、既存の仮想マシンをキャプチャするか、BLOB ストレージにアップロードされているカスタム .vhd からイメージを作成します。 カスタム .vhd の詳細については、「vm image create」を参照してください。
-json オプションは、結果が raw JSON 形式で返されるように指定します。

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK

**vm image show [options] &lt;name>**

このコマンドは、仮想マシン イメージの詳細を表示します。

    ~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
    + Fetching VM image
    info:   Executing command vm image show
    data:   {
    data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
    data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
    data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
    data:       Category: 'Microsoft',
    data:       OS: 'Windows',
    data:       Eula: 'http://www.microsoft.com',
    data:       LogicalSizeInGB: '30'
    data:   }
    info:   vm image show command OK

**vm image delete [options] &lt;name>**

このコマンドは、仮想マシン イメージを削除します。

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image
    info:   vm image delete command OK

**vm image create &lt;name> [source-path]**

このコマンドは、仮想マシン イメージを作成します。 ユーザーがカスタム .vhd ファイルを BLOB ストレージにアップロードした後、そこから仮想マシン イメージが作成されます。 この仮想マシン イメージを使って、仮想マシンを作成できます。 Location パラメーターと OS パラメーターは必須です。

> [!NOTE]
> 現在、このコマンドは、静的な .vhd ファイルのアップロードのみをサポートしています。 動的な .vhd ファイルをアップロードするには、[Go 用の Azure VHD ユーティリティ](https://github.com/Microsoft/azure-vhd-utils-for-go)を使用します。
> 
> 

一部のシステムでは、プロセスごとにファイル記述子の制限が適用されます。 制限を超えると、ファイル記述子の制限エラーが表示されます。 &lt;-p number> パラメーターを使用してコマンドを再度実行し、並列アップロードの最大数を減らすことができます。 並列アップロードの既定の最大数は 96 です。

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

## <a name="commands-to-manage-your-azure-virtual-machine-data-disks"></a>Azure の仮想マシン データ ディスクの管理用コマンド
データ ディスクとは、仮想マシンが使用できる、BLOB ストレージ内の .vhd ファイルのことです。 BLOB ストレージへのデータ ディスクのデプロイに関する詳細については、前記の Azure 技術解説図を参照してください。

データ ディスクの接続用コマンド (azure vm disk attach および azure vm disk attach-new) は、SCSI プロトコルで求められているように、接続されるデータ ディスクに論理ユニット番号 (LUN) を割り当てます。 仮想マシンに接続されている最初のデータ ディスクには LUN 0、次のデータ ディスクには LUN 1 のように、順に割り当てられます。

azure vm disk detach コマンドでデータ ディスクを切断する場合、&lt;lun&gt; パラメーターを使用して、切断するディスクを指定します。

> [!NOTE]
> データ ディスクの切断は、必ず逆の順序で行ってください。割り当てられている LUN の番号が&1; 番大きいものから切断していきます。 Linux SCSI レイヤーでは、番号が大きい LUN が接続されている場合、それより小さい番号の LUN を切断することはできません。 たとえば、LUN 1 が接続されていると、LUN 0 は切断できません。
> 
> 

**vm disk show [options] &lt;name>**

このコマンドは、Azure ディスクの詳細を表示します。

    ~$ azure vm disk show anucentos-anucentos-0-20120524070008
    info:   Executing command vm disk show
    data:   AttachedTo DeploymentName "mycentos"
    data:   AttachedTo HostedServiceName "myanucentos"
    data:   AttachedTo RoleName "myanucentos"
    data:   OS "Linux"
    data:   Location "Azure Preview"
    data:   LogicalDiskSizeInGB "30"
    data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
    data:   Name "mycentos-mycentos-0-20120524070008"
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**vm disk list [options] [vm-name]**

このコマンドは、Azure ディスク、または指定された仮想マシンに接続されているディスクの一覧を表示します。 実行時に仮想マシン名のパラメーターが指定された場合は、その仮想マシンに接続されているすべてのディスクを返します。 Lun 1 がこの仮想マシンで作成されており、一覧上の他のディスクはすべて、個別に接続されています。

    ~$ azure vm disk list mycentos
    info:   Executing command vm disk list
    data:   Lun  Size(GB)  Blob-Name
    data:   ---  --------  --------------------------------
    data:   1    30        mycentos-cb39b8223b01f95c.vhd
    data:   2    10        mycentos-e3f0d717950bb78d.vhd
    info:   vm disk list command OK

仮想マシン名のパラメーターを指定せずに実行すると、すべてのディスクが返されます。

    ~$ azure vm disk list
    data:   Name                                        OS
    data:   ------------------------------------------  -------
    data:   mycentos-mycentos-0-20120524070008          Linux
    data:   mycentos-mycentos-2-20120525055052
    data:   mywindows-winvm-20120522223119              Windows
    info:   vm disk list command OK

**vm disk delete [options] &lt;name>**

このコマンドは、Azure ディスクを個人用リポジトリから削除します。 仮想マシンを削除する前に、接続されているディスクを削除する必要があります。

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052
    info:   vm disk delete command OK

**vm disk create &lt;name> [source-path]**

このコマンドは、Azure ディスクをアップロードして登録します。 --blob-url、--location、または --affinity-group を指定する必要があります。 このコマンドを [source-path] と共に使用すると、指定した .vhd ファイルがアップロードされ、イメージが作成されます。 その後、vm disk attach を使用して、このイメージを仮想マシンに接続できます。

一部のシステムでは、プロセスごとにファイル記述子の制限が適用されます。 制限を超えると、ファイル記述子の制限エラーが表示されます。 &lt;-p number> パラメーターを使用してコマンドを再度実行し、並列アップロードの最大数を減らすことができます。 並列アップロードの既定の最大数は 96 です。

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**vm disk upload [options] &lt;source-path> &lt;blob-url> &lt;storage-account-key>**

このコマンドでは、VM ディスクをアップロードすることができます。

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**vm disk attach &lt;vm-name> &lt;disk-image-name>**

このコマンドは、BLOB ストレージ内の既存のディスクを、クラウド サービスにデプロイ済みの仮想マシンに接続します。

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**vm disk attach-new &lt;vm-name> &lt;size-in-gb> [blob-url]**

このコマンドは、データ ディスクを Azure の仮想マシンに接続します。 この例では、20 ギガバイドの新しいディスクを接続しています。 必要に応じて、BLOB の URL を最後の引数として使用し、作成するターゲット BLOB を明示的に指定することもできます。 BLOB の URL を指定しない場合、BLOB オブジェクトが自動的に生成されます。

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**vm disk detach &lt;vm-name> &lt;lun>**

このコマンドは、Azure の仮想マシンに接続されているデータ ディスクを切断します。 &lt;lun> で、切断するディスクを指定します。 切断する前に、関連付けられているディスクの一覧を表示するには、vm disk-list &lt;vm-name> を使用します。

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

## <a name="commands-to-manage-your-azure-cloud-services"></a>Azure クラウド サービスの管理用コマンド
Azure クラウド サービスは、Web ロールや Worker ロールでホストされるアプリケーションおよびサービスです。 Azure クラウド サービスの管理には、次のコマンドを使用できます。

**service create [options] &lt;serviceName>**

このコマンドは、クラウド サービスを作成します。

    ~$ azure service create newservicemsopentech
    info:    Executing command service create
    + Getting locations
    help:    Location:
      1) East Asia
      2) Southeast Asia
      3) North Europe
      4) West Europe
      5) East US
      6) West US
      : 6
    + Creating cloud service
    data:    Cloud service name newservicemsopentech
    info:    service create command OK

**service show [options] &lt;serviceName>**

このコマンドは、Azure クラウド サービスの詳細を表示します。

    ~$ azure service show newservicemsopentech
    info:    Executing command service show
    + Getting cloud service
    data:    Name newservicemsopentech
    data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
    data:    Properties location West US
    data:    Properties label newservicemsopentech
    data:    Properties status Created
    data:    Properties dateCreated
    data:    Properties dateLastModified
    info:    service show command OK

**service list [options]**

このコマンドは、Azure クラウド サービスの一覧を表示します。

    ~$ azure service list
    info:   Executing command service list
    data:   Name         Status
    data:   -----------  -------
    data:   service1     Created
    data:   service2     Created
    info:   service list command OK

**service delete [options] &lt;name>**

このコマンドは、Azure クラウド サービスを削除します。

    ~$ azure service delete myservice
    info:   Executing command service delete myservice
    info:   cloud-service delete command OK

強制的に削除するには、`-q` パラメーターを使用します。

## <a name="commands-to-manage-your-azure-certificates"></a>Azure 証明書の管理用コマンド
Azure 証明書は、Azure アカウントに接続される SSL 証明書です。 Azure 証明書の詳細については、「[証明書の管理](http://msdn.microsoft.com/library/azure/gg981929.aspx)」を参照してください。

**service cert list [options]**

このコマンドは、Azure 証明書の一覧を表示します。

    ~$ azure service cert list
    info:   Executing command service cert list
    + Fetching cloud services
    + Fetching certificates
    data:   Service   Thumbprint                                Algorithm
    data:   --------  ----------------------------------------  ---------
    data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
    info:   service cert list command OK

**service cert create &lt;dns-prefix> &lt;file> [password]**

このコマンドは、証明書をアップロードします。 パスワードで保護されていない証明書の場合は、パスワードの入力ダイアログを空白のままにします。

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password:
    + Creating certificate
    info:   service cert create command OK

**service cert delete [options] &lt;thumbprint>**

このコマンドは、証明書を削除します。

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

## <a name="commands-to-manage-your-web-apps"></a>Web アプリの管理用コマンド
Azure の Web アプリは、URI でアクセスできる Web 構成です。 Web アプリは仮想マシンでホストされますが、仮想マシン自体の作成とデプロイについて詳細を考慮する必要はありません。 これらの詳細は、Azure によって処理されます。

**site list [options]**

このコマンドは、Web アプリの一覧を表示します。

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**site set [options] [name]**

このコマンドは、[name] という名前の Web アプリの構成オプションを設定します。

    ~$ azure site set
    info:    Executing command site set
    Web site name: mydemosite
    + Getting sites
    + Updating site config information
    info:    site set command OK

**site deploymentscript [options]**

このコマンドは、カスタム デプロイ スクリプトを生成します。

    ~$ azure site deploymentscript --node
    info:    Executing command site deploymentscript
    info:    Generating deployment script for node.js Web Site
    info:    Generated deployment script files
    info:    site deploymentscript command OK

**site create [options] [name]**

このコマンドは、新しい Web アプリとローカル ディレクトリを作成します。

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

> [!NOTE]
> サイトの名前は一意にする必要があります。 既存のサイトと同じ DNS 名のサイトは作成できません。
> 
> 

**site browse [options] [name]**

このコマンドは、Web アプリをブラウザーで開きます。

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**site show [options] [name]**

このコマンドは、Web アプリの詳細を表示します。

    ~$ azure site show mysite
    info:   Executing command site show
    info:   Showing details for site
    data:   Site AdminEnabled true
    data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
    data:   Site Name mysite
    data:   Site Owner 00060000814EDDEE
    data:   Site RepositorySiteName mysite
    data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
    data:   Site State Running
    data:   Site UsageState Normal
    data:   Site WebSpace northeuropewebspace
    data:   Config AppSettings
    data:   Config ConnectionStrings
    data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
    data:   Config DetailedErrorLoggingEnabled false
    data:   Config HttpLoggingEnabled false
    data:   Config Metadata
    data:   Config NetFrameworkVersion v4.0
    data:   Config NumberOfWorkers 1
    data:   Config PhpVersion 5.3
    data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
    data:   Config RequestTracingEnabled false
    data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
    info:   site show command OK

**site delete [options] [name]**

このコマンドは、Web アプリを削除します。

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

 **site swap [options] [name]**

このコマンドは、2 つの Web アプリ スロットをスワップします。

このコマンドでは、次の追加のオプションがサポートされています。

**-q または **--quiet**: 確認のダイアログを表示しません。 このオプションは自動スクリプトに使用します。

**site start [options] [name]**

このコマンドは、Web アプリを起動します。

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**site stop [options] [name]**

このコマンドは、Web アプリを停止します。

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**site restart [options] [name]**

このコマンドは、指定された Web アプリを停止し、その後、起動します。

このコマンドでは、次の追加のオプションがサポートされています。

**--slot** &lt;slot>: 再起動するスロットの名前。

**site location list [options]**

このコマンドは、Web アプリの場所の一覧を表示します。

    ~$ azure site location list
    info:    Executing command site location list
    + Getting locations
    data:    Name
    data:    ----------------
    data:    West Europe
    data:    West US
    data:    North Central US
    data:    North Europe
    data:    East Asia
    data:    East US
    info:    site location list command OK

### <a name="commands-to-manage-your-web-app-application-settings"></a>Web アプリのアプリケーション設定の管理用コマンド
**site appsetting list [options] [name]**

このコマンドは Web アプリに追加されたアプリケーション設定の一覧を表示します。

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test  value
    info:    site appsetting list command OK

**site appsetting add [options] &lt;keyvaluepair> [name]**

このコマンドは、アプリケーション設定をキーと値のペアとして Web アプリに追加します。

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**site appsetting delete [options] &lt;key> [name]**

このコマンドは、指定されたアプリケーション設定を Web アプリから削除します。

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test? [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**site appsetting show [options] &lt;key> [name]**

このコマンドは、指定されたアプリケーション設定の詳細を表示します。

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

### <a name="commands-to-manage-your-web-app-certificates"></a>Web アプリ証明書の管理用コマンド
**site cert list [options] [name]**

このコマンドは、Web アプリ証明書の一覧を表示します。

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                      Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**site cert add [options] &lt;certificate-path> [name]**

**site cert delete [options] &lt;thumbprint> [name]**

**site cert show [options] &lt;thumbprint> [name]**

このコマンドは、証明書の詳細を表示します。

    ~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    Executing command site cert show
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Certificate hostNames 0=msopentech.azurewebsites.net
    data:    Certificate expirationDate
    data:    Certificate friendlyName msopentech.azurewebsites.net
    data:    Certificate issueDate
    data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
    data:    Certificate subjectName msopentech.azurewebsites.net
    data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    site cert show command OK

### <a name="commands-to-manage-your-web-app-connection-strings"></a>Web アプリの接続文字列の管理用コマンド
**site connectionstring list [options] [name]**

**site connectionstring add [options] &lt;connectionname> &lt;value> &lt;type> [name]**

**site connectionstring delete [options] &lt;connectionname> [name]**

**site connectionstring show [options] &lt;connectionname> [name]**

### <a name="commands-to-manage-your-web-app-default-documents"></a>Web アプリの既定のドキュメントの管理用コマンド
**site defaultdocument list [options] [name]**

**site defaultdocument add [options] &lt;document> [name]**

**site defaultdocument delete [options] &lt;document> [name]**

### <a name="commands-to-manage-your-web-app-deployments"></a>Web アプリのデプロイの管理用コマンド
**site deployment list [options] [name]**

**site deployment show [options] &lt;commitId> [name]**

**site deployment redeploy [options] &lt;commitId> [name]**

**site deployment github [options] [name]**

**site deployment user set [options] [username] [pass]**

### <a name="commands-to-manage-your-web-app-domains"></a>Web アプリ ドメインの管理用コマンド
**site domain list [options] [name]**

**site domain add [options] &lt;dn> [name]**

**site domain delete [options] &lt;dn> [name]**

### <a name="commands-to-manage-your-web-app-handler-mappings"></a>Web アプリのハンドラー マッピングの管理用コマンド
**site handler list [options] [name]**

**site handler add [options] &lt;extension> &lt;processor> [name]**

**site handler delete [options] &lt;extension> [name]**

### <a name="commands-to-manage-your-web-jobs"></a>Web ジョブの管理用コマンド
**site job list [options] [name]**

このコマンドは、Web アプリ内のすべての Web ジョブの一覧を表示します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--job-type** &lt;job-type>: 省略可能。 Web ジョブの種類。 有効な値は、「triggered」または「continuous」です。 既定では、すべての種類の Web ジョブを返します。
* **--slot** &lt;slot>: 再起動するスロットの名前。

**site job show [options] &lt;jobName> &lt;jobType> [name]**

このコマンドは、特定の Web ジョブの詳細を表示します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--job-name** &lt;job-name>: 必須。 Web ジョブの名前。
* **--job-type** &lt;job-type>: 必須。 Web ジョブの種類。 有効な値は、「triggered」または「continuous」です。
* **--slot** &lt;slot>: 再起動するスロットの名前。

**site job delete [options] &lt;jobName> &lt;jobType> [name]**

このコマンドは、指定された Web ジョブを削除します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--job-name** &lt;job-name>    必須。 Web ジョブの名前。
* **--job-type** &lt;job-type>    必須。 Web ジョブの種類。 有効な値は、「triggered」または「continuous」です。
* **-q** または **--quiet**: 確認のダイアログを表示しません。 このオプションは自動スクリプトに使用します。
* **--slot** &lt;slot>: 再起動するスロットの名前。

**site job upload [options] &lt;jobName> &lt;jobType> <jobFile> [name]**

このコマンドは、指定された Web ジョブを削除します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--job-name** &lt;job-name>: 必須。 Web ジョブの名前。
* **--job-type** &lt;job-type>: 必須。 Web ジョブの種類。 有効な値は、「triggered」または「continuous」です。
* **--job-file** &lt;job-file>: 必須。 ジョブ ファイル。
* **--slot** &lt;slot>: 再起動するスロットの名前。

**site job start [options] &lt;jobName> &lt;jobType> [name]**

このコマンドは、指定された Web ジョブを開始します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--job-name** &lt;job-name>: 必須。 Web ジョブの名前。
* **--job-type** &lt;job-type>: 必須。 Web ジョブの種類。 有効な値は、「triggered」または「continuous」です。
* **--slot** &lt;slot>: 再起動するスロットの名前。

**site job stop [options] &lt;jobName> &lt;jobType> [name]**

このコマンドは、指定された Web ジョブを停止します。 継続中のジョブのみ停止できます。

このコマンドでは、次の追加のオプションがサポートされています。

* **--job-name** &lt;job-name>: 必須。 Web ジョブの名前。
* **--slot** &lt;slot>: 再起動するスロットの名前。

### <a name="commands-to-manage-your-web-jobs-history"></a>Web ジョブ履歴の管理用コマンド
**site job history list [options] [jobName] [name]**

このコマンドは、指定された Web ジョブの実行履歴を表示します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--job-name** &lt;job-name>: 必須。 Web ジョブの名前。
* **--slot** &lt;slot>: 再起動するスロットの名前。

**site job history show [options] [jobName] [runId] [name]**

このコマンドは、指定された Web ジョブのジョブの実行の詳細を表示します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--job-name** &lt;job-name>: 必須。 Web ジョブの名前。
* **--run-id** &lt;run-id>: 省略可能。 実行履歴の ID。 このパラメーターを指定しない場合は、最後に実行されたときの詳細が表示されます。
* **--slot** &lt;slot>: 再起動するスロットの名前。

### <a name="commands-to-manage-your-web-app-diagnostics"></a>Web アプリ診断の管理用コマンド
**site log download [options] [name]**

このコマンドは、Web アプリの診断が含まれている .zip ファイルをダウンロードします。

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**site log tail [options] [name]**

このコマンドは、ターミナルをログ ストリーミング サービスに接続します。

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**site log set [options] [name]**

このコマンドは、Web アプリの診断オプションを構成します。

    ~$ azure site log set -a
    info:    Executing command site log set
    + Getting output options
    help:    Output:
      1) file
      2) storage
      : 1
    Web site name: mydemosite
    + Getting locations
    + Getting sites
    + Getting site information
    + Getting diagnostic settings
    + Updating diagnostic settings
    info:    site log set command OK

### <a name="commands-to-manage-your-web-app-repositories"></a>Web アプリ リポジトリの管理用コマンド
**site repository branch [options] &lt;branch> [name]**

**site repository delete [options] [name]**

**site repository sync [options] [name]**

### <a name="commands-to-manage-your-web-app-scaling"></a>Web アプリのスケーリングの管理用コマンド
**site scale mode [options] &lt;mode> [name]**

**site scale instances [options] &lt;instances> [name]**

## <a name="commands-to-manage-azure-mobile-services"></a>Azure Mobile Services の管理用コマンド
Azure Mobile Services は、アプリケーションのバックエンド機能を有効にする Azure サービスのセットです。 Mobile Services のコマンドは以下のように分類されます。

* [モバイル サービス インスタンスの管理用コマンド](#Mobile_Services)
* [モバイル サービス構成の管理用コマンド](#Mobile_Configuration)
* [モバイル サービス テーブルの管理用コマンド](#Mobile_Tables)
* [モバイル サービス スクリプトの管理用コマンド](#Mobile_Scripts)
* [スケジュールされたジョブの管理用コマンド](#Mobile_Jobs)
* [モバイル サービスのスケール用コマンド](#Mobile_Scale)

以下のオプションはほとんどの Mobile Services 用のコマンドで使用できます。

* **-h** または **--help**: 出力の使用法を表示します。
* **-s `<id>`** または **--subscription `<id>`**: `<id>` で指定したサブスクリプションを使用します。
* **-v** または **--verbose**: 詳細な出力を書き込みます。
* **--json**: JSON の出力を書き込みます。

### <a name="a-namemobileservicesacommands-to-manage-mobile-service-instances"></a><a name="Mobile_Services"></a>モバイル サービス インスタンスの管理用コマンド
**mobile locations [options]**

このコマンドは、Mobile Services がサポートする場所の一覧を表示します。

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US
    info:    North Europe

**mobile create [options] [servicename] [sqlAdminUsername] [sqlAdminPassword]**

このコマンドは、モバイル サービスを SQL Database およびサーバーと共に作成します。

    ~$ azure mobile create todolist your_login_name Secure$Password
    info:    Executing command mobile create
    + Creating mobile service
    info:    Overall application state: Healthy
    info:    Mobile service (todolist) state: ProvisionConfigured
    info:    SQL database (todolist_db) state: Provisioned
    info:    SQL server (e96ean1c6v) state: ProvisionConfigured
    info:    mobile create command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-r `<sqlServer>`** または **--sqlServer`<sqlServer>`**: `<sqlServer>` で指定された既存の SQL Database サーバーを使用します。
* **-d `<sqlDb>`** または **--sqlDb `<sqlDb>`**: `<sqlDb>` で指定された既存の SQL データベースを使用します。
* **-l `<location>`** または **--location `<location>`**: `<location>` で指定された特定の場所にサービスを作成します。 利用可能な場所の一覧を取得するには、azure mobile locations を実行します。
* **--sqlLocation `<location>`**: `<location>` の場所に SQL Server を作成します。この場所がモバイル サービスの既定の場所になります。

**mobile delete [options] [servicename]**

このコマンドは、モバイル サービスを SQL データベースおよびサーバーと共に削除します。

    ~$ azure mobile delete todolist -a -q
    info:    Executing command mobile delete
    data:    Mobile service todolist
    data:    SQL database todolistAwrhcL60azo1C401
    data:    SQL server fh1kvbc7la
    + Deleting mobile service
    info:    Deleted mobile service
    + Deleting SQL server
    info:    Deleted SQL server
    + Deleting mobile application
    info:    Deleted mobile application
    info:    mobile delete command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-d** または **--deleteData**: データベースからこのモバイル サービスのデータをすべて削除します。
* **-a** または **--deleteAll**: SQL Database とサーバーを削除します。
* **-q** または **--quiet**: 確認のダイアログを表示しません。 このオプションは自動スクリプトに使用します。

**mobile list [options]**

このコマンドは、モバイル サービスの一覧を表示します。

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**mobile show [options] [servicename]**

このコマンドは、モバイル サービスの詳細を表示します。

    ~$ azure mobile show todolist
    info:    Executing command mobile show
    + Getting information
    info:    Mobile application
    data:    status Healthy
    data:    Mobile service name todolist
    data:    Mobile service status ProvisionConfigured
    data:    SQL database name todolistAwrhcL60azo1C401
    data:    SQL database status Linked
    data:    SQL server name fh1kvbc7la
    data:    SQL server status Linked
    info:    Mobile service
    data:    name todolist
    data:    state Ready
    data:    applicationUrl https://todolist.azure-mobile.net/
    data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    webspace WESTUSWEBSPACE
    data:    region West US
    data:    tables TodoItem
    info:    mobile show command OK

**mobile restart [options] [servicename]**

モバイル サービス インスタンスを再起動します。

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**mobile log [options] [servicename]**

このコマンドは、`error` 以外のすべての種類のログをフィルターで除いたモバイル サービス ログを返します。

    ~$ azure mobile log todolist -t error
    info:    Executing command mobile log
    data:
    data:    timeCreated 2013-01-07T16:04:43.351Z
    data:    type error
    data:    source /scheduler/TestingLogs.js
    data:    message This is an error.
    data:
    info:    mobile log command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-r `<query>`** または **--query `<query>`**: 指定されたログ クエリを実行します。
* **-t `<type>`** または **--type `<type>`**: 返されたログをエントリ `<type>` によってフィルター処理します。`information`、`warning`、または `error` を指定できます。
* **-k `<skip>`** または **--skip `<skip>`**: `<skip>` で指定された数の行をスキップします。
* **-p `<top>`** または **--top `<top>`**: `<top>` で指定された数の行を返します。

> [!NOTE]
> **--query** パラメーターは、**--type**、**--skip**、および **--top** よりも優先されます。
> 
> 

**mobile recover [options] [unhealthyservicename] [healthyservicename]**

このコマンドは、異常な状態のモバイル サービスを、別のリージョンの正常なモバイル サービスに移動して、復旧します。

このコマンドでは、次の追加のオプションがサポートされています。

**-q** または **--quiet**: 復旧確認のダイアログを非表示にします。

**mobile key regenerate [options] [servicename] [type]**

このコマンドは、モバイル サービス アプリケーション キーを再生成します。

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

キーの種類は `master` と `application` です。

> [!NOTE]
> キーを再生成すると、古いキーを使用するクライアントはモバイル サービスにアクセスできなくなります。 アプリケーション キーを再生成する場合、アプリケーションを新しいキーで更新する必要があります。
> 
> 

**mobile key set [options] [servicename] [type] [value]**

このコマンドは、モバイル サービス キーに指定された値を設定します。

### <a name="a-namemobileconfigurationacommands-to-manage-mobile-service-configuration"></a><a name="Mobile_Configuration"></a>モバイル サービス構成の管理用コマンド
**mobile config list [options] [servicename]**

このコマンドは、モバイル サービスの構成オプションの一覧を表示します。

    ~$ azure mobile config list todolist
    info:    Executing command mobile config list
    + Getting mobile service configuration
    data:    dynamicSchemaEnabled true
    data:    microsoftAccountClientSecret Not configured
    data:    microsoftAccountClientId Not configured
    data:    microsoftAccountPackageSID Not configured
    data:    facebookClientId Not configured
    data:    facebookClientSecret Not configured
    data:    twitterClientId Not configured
    data:    twitterClientSecret Not configured
    data:    googleClientId Not configured
    data:    googleClientSecret Not configured
    data:    apnsMode none
    data:    apnsPassword Not configured
    data:    apnsCertifcate Not configured
    info:    mobile config list command OK

**mobile config get [options] [servicename] [key]**

このコマンドは、モバイル サービスの特定の構成オプション (この例では動的スキーマ) を取得します。

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**mobile config set [options] [servicename] [key] [value]**

このコマンドは、モバイル サービスの特定の構成オプション (この例では動的スキーマ) を設定します。

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK


### <a name="a-namemobiletablesacommands-to-manage-mobile-service-tables"></a><a name="Mobile_Tables"></a>モバイル サービス テーブルの管理用コマンド
**mobile table list [options] [servicename]**

このコマンドは、モバイル サービスの全テーブルの一覧を表示します。

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**mobile table show [options] [servicename] [tablename]**

このコマンドは、特定のテーブルの詳細を表示します。

    ~$azure mobile table show todolist
    info:    Executing command mobile table show
    + Getting table information
    info:    Table statistics:
    data:    Number of records 5
    info:    Table operations:
    data:    Operation  Script       Permissions
    data:    ---------  -----------  -----------
    data:    insert     1900 bytes   user
    data:    read       Not defined  user
    data:    update     Not defined  user
    data:    delete     Not defined  user
    info:    Table columns:
    data:    Name  Type           Indexed
    data:    ----  -------------  -------
    data:    id    bigint(MSSQL)  Yes
    data:    text      string
    data:    complete  boolean
    info:    mobile table show command OK

**mobile table create [options] [servicename] [tablename]**

このコマンドは、テーブルを作成します。

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-p `&lt;permissions>`** または **--permissions `&lt;permissions>`**: `<operation>`=`<permission>` のペアのコンマ区切りのリストです。`<operation>` には `insert`、`read`、`update`、または `delete` を、`&lt;permissions>` には `public`、`application` (既定)、`user`、または `admin` をそれぞれ指定できます。

**mobile data read [options] [servicename] [tablename] [query]**

このコマンドは、データをテーブルから読み取ります。

    ~$azure mobile data read todolist TodoItem
    info:    Executing command mobile data read
    data:    id  text     complete
    data:    --  -------  --------
    data:    1   item #1  false
    data:    2   item #2  true
    data:    3   item #3  false
    data:    4   item #4  true
    info:    mobile data read command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-k `<skip>`** または **--skip `<skip>`**: `<skip>` で指定された数の行をスキップします。
* **-t `<top>`** または **--top `<top>`**: `<top>` で指定された数の行を返します。
* **-l** または **--list**: 一覧形式でデータを返します。

**mobile table update [options] [servicename] [tablename]**

このコマンドは、テーブルの削除権限を管理者のみが持つように変更します。

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-p `&lt;permissions>`** または **--permissions `&lt;permissions>`**: `<operation>`=`<permission>` のペアのコンマ区切りのリストです。`<operation>` には `insert`、`read`、`update`、または `delete` を、`&lt;permissions>` には `public`、`application` (既定)、`user`、または `admin` をそれぞれ指定できます。
* **--deleteColumn `<columns>`**: 削除する列のコンマ区切りのリスト。`<columns>` として指定します。
* **-q** または **--quiet**: 確認メッセージを表示せずに列を削除します。
* **--addIndex `<columns>`**: インデックスに含める列のコンマ区切りのリスト。
* **--deleteIndex `<columns>`**: インデックスから除外する列のコンマ区切りのリスト。

**mobile table delete [options] [servicename] [tablename]**

このコマンドは、テーブルを削除します。

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

確認なしでテーブルを削除するには、-q パラメーターを指定します。 このパラメーターは、自動スクリプトがブロックされるのを防ぐために指定します。

**mobile data truncate [options] [servicename] [tablename]**

このコマンドは、テーブルからすべてのデータの行を削除します。

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table? (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK


### <a name="a-namemobilescriptsacommands-to-manage-scripts"></a><a name="Mobile_Scripts"></a>スクリプトの管理用コマンド
このセクションのコマンドは、モバイル サービスに属するサーバー スクリプトの管理に使用します。 詳細については、[Mobile Services のサーバー スクリプトの操作に関するページ](https://github.com/Azure/azure-mobile-services/blob/master/docs/mobile-services-how-to-use-server-scripts.md)を参照してください。

**mobile script list [options] [servicename]**

このコマンドは、テーブル スクリプトとスケジューラ スクリプト両方を含め、登録済みのスクリプトの一覧を表示します。

    ~$azure mobile script list todolist
    info:    Executing command mobile script list
    + Getting script information
    info:    Table scripts
    data:    Name                   Size
    data:    ---------------------  ----
    data:    table/TodoItem.delete  256
    data:    table/Devices.insert   1660
    error:   Unable to get shared scripts
    info:    Scheduler scripts
    data:    Name                 Status     Interval   Last run   Next run
    data:    -------------------  ---------  ---------  ---------  ---------
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    info:    mobile script list command OK

**mobile script download [options] [servicename] [scriptname]**

このコマンドは、挿入スクリプトを、TodoItem テーブルから `table` サブフォルダーの `todoitem.insert.js` というファイルにダウンロードします。

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-p `<path>`** または **--path `<path>`**: スクリプトを保存するファイル内の場所。既定では、現在の作業ディレクトリです。
* **-f `<file>`** または **--file `<file>`**: スクリプトを保存するファイルの名前。
* **-o** または **--override**: 既存のファイルを上書きします。
* **-c** または **--console**: ファイルではなくコンソールにスクリプトを書き込みます。

**mobile script upload [options] [servicename] [scriptname]**

次のコマンドは、`table` サブフォルダーから `todoitem.insert.js` というスクリプトをアップロードします。

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

ファイルの名前は、テーブル名と操作名で構成する必要があります。 ファイルは、コマンドの実行場所にある table サブフォルダー内に配置する必要があります。 **-f `<file>`** または **--file `<file>`** パラメーターを使用して、別のファイル名や、登録するスクリプトが保存されているファイルへのパスを指定することもできます。

**mobile script delete [options] [servicename] [scriptname]**

このコマンドは、既存の挿入スクリプトを TodoItem テーブルから削除します。

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### <a name="a-namemobilejobsacommands-to-manage-scheduled-jobs"></a><a name="Mobile_Jobs"></a>スケジュールされたジョブの管理用コマンド
このセクションのコマンドは、モバイル サービスに属するスケジュールされたジョブの管理に使用します。 詳細については、「[ジョブのスケジュール](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx)」を参照してください。

**mobile job list [options] [servicename]**

このコマンドはスケジュールされたジョブを一覧します。

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**mobile job create [options] [servicename] [jobname]**

次のコマンドは、1 時間ごとに実行するようにスケジュールされた `getUpdates` という名前のジョブを作成します。

    ~$azure mobile job create -i 1 -u hour todolist getUpdates
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-i `<number>`** または **--interval `<number>`**: ジョブの実行間隔を示す整数値です。 既定値は `15` です。
* **-u `<unit>`** または **--intervalUnit `<unit>`**: *interval* の単位で、次の値のいずれかを指定できます。
  * **minute** (既定)
  * **hour**
  * **day**
  * **month**
  * **none** (オンデマンド ジョブ)
* **-t `<time>`** **--startTime `<time>`** ISO 形式によるスクリプトの最初の実行開始時刻です。 既定値は `now` です。

> [!NOTE]
> スクリプトをアップロードする必要があるため、新しいジョブは無効な状態で作成されます。 **mobile script upload** コマンドを使用してスクリプトをアップロードし、**mobile job update** コマンドでジョブを有効にします。
> 
> 

**mobile job update [options] [servicename] [jobname]**

次のコマンドは、無効な状態の `getUpdates` ジョブを有効にします。

    ~$azure mobile job update -a enabled todolist getUpdates
    info:    Executing command mobile job update
    info:    mobile job update command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-i `<number>`** または **--interval `<number>`**: ジョブの実行間隔を示す整数値です。 既定値は `15` です。
* **-u `<unit>`** または **--intervalUnit `<unit>`**: *interval* の単位で、次の値のいずれかを指定できます。
  * **minute** (既定)
  * **hour**
  * **day**
  * **month**
  * **none** (オンデマンド ジョブ)
* **-t `<time>`** **--startTime `<time>`** ISO 形式によるスクリプトの最初の実行開始時刻です。 既定値は `now` です。
* **-a `<status>`** または **--status `<status>`**: `enabled` または `disabled` で指定されるジョブの状態です。

**mobile job delete [options] [servicename] [jobname]**

このコマンドは、スケジュールされたジョブである getUpdates を TodoList サーバーから削除します。

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

> [!NOTE]
> ジョブを削除すると、アップロードされたスクリプトも削除されます。
> 
> 

### <a name="a-namemobilescaleacommands-to-scale-a-mobile-service"></a><a name="Mobile_Scale"></a>モバイル サービスのスケール用コマンド
このセクションのコマンドは、モバイル サービスの規模を設定するために使用されます。 詳細については、「[モバイル サービスの拡張](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx)」を参照してください。

**mobile scale show [options] [servicename]**

このコマンドは、現在のコンピューティング モード、インスタンスの数など、規模の情報を表示します。

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**mobile scale change [options] [servicename]**

このコマンドは、モバイル サービスの規模を無料モードからプレミアム モードに変更します。

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-c `<mode>`** または **--computeMode `<mode>`**: コンピューティング モードは `Free` または `Reserved` にする必要があります。
* **-i `<count>`** または **--numberOfInstances `<count>`**: 占有モードでの実行時に使用されるインスタンスの数です。

> [!NOTE]
> コンピューティング モードを `Reserved` に設定すると、同じリージョンのモバイル サービスすべてがプレミアム モードで実行されます。
> 
> 

### <a name="commands-to-enable-preview-features-for-your-mobile-service"></a>モバイル サービスのプレビュー機能の有効化のためのコマンド
**mobile preview list [options] [servicename]**

このコマンドは、指定されたサービスで利用可能なプレビュー機能とそれらのプレビュー機能が有効になっているかどうかを表示します。

    ~$ azure mobile preview list mysite
    info:    Executing command mobile preview list
    + Getting preview features
    data:    Preview feature  Enabled
    data:    ---------------  -------
    data:    SourceControl    No
    data:    Users            No
    info:    You can enable preview features using the 'azure mobile preview enable' command.
    info:    mobile preview list command OK

**mobile preview enable [options] [servicename] [featurename]**

このコマンドは、モバイル サービスの指定されたプレビュー機能を有効にします。 モバイル サービスのプレビュー機能は、有効にした後で無効にすることはできません。

### <a name="commands-to-manage-your-mobile-service-apis"></a>モバイル サービス API の管理用コマンド
**mobile api list [options] [servicename]**

このコマンドは、モバイル サービス用に作成されたモバイル サービス カスタム API の一覧を表示します。

    ~$ azure mobile api list mysite
    info:    Executing command mobile api list
    + Retrieving list of APIs
    info:    APIs
    data:    Name                  Get          Put          Post         Patch        Delete
    data:    --------------------  -----------  -----------  -----------  -----------  -----------
    data:    myCustomRetrieveAPI   application  application  application  application  application
    info:    You can manipulate API scripts using the 'azure mobile script' command.
    info:    mobile api list command OK

**mobile api create [options] [servicename] [apiname]**

モバイル サービス カスタム API を作成します。

    ~$ azure mobile api create mysite myCustomRetrieveAPI
    info:    Executing command mobile api create
    + Creating custom API: 'myCustomRetrieveAPI'
    info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
    info:    mobile api create command OK

このコマンドでは、次の追加のオプションがサポートされています。

**-p** または **--permissions** &lt;permissions>:&lt;method>=&lt;permission> ペアのコンマ区切りリスト。

**mobile api update [options] [servicename] [apiname]**

このコマンドは、指定されたモバイル サービス カスタム API を更新します。

このコマンドでは、次の追加のオプションがサポートされています。

このコマンドでは、次の追加のオプションがサポートされています。

* **-p** または **--permissions** &lt;permissions>: &lt;method>=&lt;permission> ペアのコンマ区切りリスト。
* **-f** または **--force**: アクセス許可のメタデータ ファイルに加えられたカスタムの変更を上書きします。

**mobile api delete [options] [servicename] [apiname]**

    ~$ azure mobile api delete mysite myCustomRetrieveAPI
    info:    Executing command mobile api delete
    + Deleting API: 'myCustomRetrieveAPI'
    info:    mobile api delete command OK

このコマンドは、指定されたモバイル サービス カスタム API を削除します。

### <a name="commands-to-manage-your-mobile-application-app-settings"></a>モバイル アプリケーションのアプリケーション設定の管理用コマンド
**mobile appsetting list [options] [servicename]**

このコマンドは、指定されたサービスのモバイル アプリケーションのアプリケーション設定を表示します。

    ~$ azure mobile appsetting list mysite
    info:    Executing command mobile appsetting list
    + Retrieving app settings
    data:    Name               Value
    data:    -----------------  -----
    data:    enablebetacontent  true
    info:    mobile appsetting list command OK

**mobile appsetting add [options] [servicename] [name] [value]**

このコマンドは、モバイル サービスのカスタム アプリケーション設定を追加します。

    ~$ azure mobile appsetting add mysite enablebetacontent true
    info:    Executing command mobile appsetting add
    + Retrieving app settings
    + Adding app setting
    info:    mobile appsetting add command OK

**mobile appsetting delete [options] [servicename] [name]**

このコマンドは、モバイル サービスの指定されたアプリケーション設定を削除します。

    ~$ azure mobile appsetting delete mysite enablebetacontent
    info:    Executing command mobile appsetting delete
    + Retrieving app settings
    + Removing app setting 'enablebetacontent'
    info:    mobile appsetting delete command OK

**mobile appsetting show [options] [servicename] [name]**

このコマンドは、モバイル サービスの指定されたアプリケーション設定を削除します。

    ~$ azure mobile appsetting show mysite enablebetacontent
    info:    Executing command mobile appsetting show
    + Retrieving app settings
    info:    enablebetacontent: true
    info:    mobile appsetting show command OK

## <a name="manage-tool-local-settings"></a>ツールのローカル設定の管理
ローカル設定とは、サブスクリプションの ID と既定のストレージ アカウント名のことを指します。

**config list [options]**

このコマンドは、構成設定を表示します。

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**config set [options] &lt;name&gt;,&lt;value&gt;**

このコマンドは、構成設定を変更します。

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

## <a name="commands-to-manage-service-bus"></a>Service Bus の管理用コマンド
これらのコマンドを使用して、Service Bus アカウントを管理します。

**sb namespace check [options] &lt;name>**

Service Bus 名前空間が有効で利用可能であることを確認します。

**sb namespace create &lt;name> &lt;location>**

Service Bus 名前空間を作成します。

    ~$ azure sb namespace create mysbnamespacea-test "West US"
    info:    Executing command sb namespace create
    + Creating namespace mysbnamespacea-test in region West US
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Activating
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    _: [object Object]
    info:    sb namespace create command OK


**sb namespace delete &lt;name>**

名前空間を削除します。

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test? [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**sb namespace list**

自分のアカウント用に作成されたすべての名前空間の一覧を表示します。

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK


**sb namespace location list**

使用できるすべての名前空間の場所の一覧を表示します。

    ~$ azure sb namespace location list
    info:    Executing command sb namespace location list
    + Getting locations
    data:    Name              Code
    data:    ----------------  ----------------
    data:    East Asia         East Asia
    data:    West Europe       West Europe
    data:    North Europe      North Europe
    data:    East US           East US
    data:    Southeast Asia    Southeast Asia
    data:    North Central US  North Central US
    data:    West US           West US
    data:    South Central US  South Central US
    info:    sb namespace location list command OK

**sb namespace show &lt;name>**

指定された名前空間に関する詳細を表示します。

    ~$ azure sb namespace show mysbnamespacea-test
    info:    Executing command sb namespace show
    + Getting namespace
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Active
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    UpdatedAt: 2013-11-14T16:25:37.85Z
    info:    sb namespace show command OK

**sb namespace verify &lt;name>**

名前空間が使用可能かどうかを確認します。

## <a name="commands-to-manage-your-storage-objects"></a>ストレージ オブジェクトの管理用コマンド
### <a name="commands-to-manage-your-storage-accounts"></a>ストレージ アカウントの管理用コマンド
**storage account list [options]**

このコマンドは、サブスクリプションのストレージ アカウントを表示します。

    ~$ azure storage account list
    info:    Executing command storage account list
    + Getting storage accounts
    data:    Name             Label  Location
    data:    ---------------  -----  --------
    data:    mybasestorage           West US
    info:    storage account list command OK

**storage account show [options] <name>**

このコマンドは、URI や アカウントのプロパティなど、指定されたストレージ アカウントに関する情報を表示します。

**storage account create [options] <name>**

このコマンドは、指定されたオプションに基づいて、ストレージ アカウントを作成します。

    ~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-e** または **--label** &lt;label>: ストレージ アカウントのラベル。
* **-d** または **--description** &lt;description>: ストレージ アカウントの記述。
* **-l** または **--location** &lt;name>: ストレージ アカウントを作成するリージョン。
* **-a** または **--affinity-group** &lt;name>: ストレージ アカウントと関連付けるアフィニティ グループ。 
* **--type**: 作成するアカウントの種類を示します。冗長性オプション付きの Standard Storage (LRS、ZRS、GRS、RAGRS) または Premium Storage (PLRS)。

**storage account set [options] <name>**

このコマンドは、指定されたストレージ アカウントを更新します。

    ~$ azure storage account set mybasestorage --kind Storage --sku-name GRS
    info:    Executing command storage account set
    + Updating storage account
    info:    storage account set command OK

このコマンドでは、次の追加のオプションがサポートされています。

* **-e** または **--label** &lt;label>: ストレージ アカウントのラベル。
* **-d** または **--description** &lt;description>: ストレージ アカウントの記述。
* **-l** または **--location** &lt;name>: ストレージ アカウントを作成するリージョン。
* **--type**: アカウントの新しい種類を示します。冗長性オプション付きの Standard Storage (LRS、ZRS、GRS、RAGRS) または Premium Storage (PLRS)。

**storage account delete [options] <name>**

このコマンドは、指定されたストレージ アカウントを削除します。

このコマンドでは、次の追加のオプションがサポートされています。

**-q** または **--quiet**: 確認のダイアログを表示しません。 このオプションは自動スクリプトに使用します。

### <a name="commands-to-manage-your-storage-account-keys"></a>ストレージ アカウント キーの管理用コマンド
**storage account keys list [options] <name>**

このコマンドは、指定されたストレージ アカウントのプライマリ アクセス キーとセカンダリ アクセス キーの一覧を表示します。

**storage account keys renew [options] <name>**

### <a name="commands-to-manage-your-storage-container"></a>ストレージ コンテナーの管理用コマンド
**storage container list [options] [prefix]**

このコマンドは、指定されたストレージ アカウントのストレージ コンテナーの一覧を表示します。 ストレージ アカウントは、接続文字列、またはストレージ アカウント名とアカウント キーで指定します。

このコマンドでは、次の追加のオプションがサポートされています。

* **-p** または **-prefix** &lt;prefix>: ストレージ コンテナー名のプレフィックス。
* **-a** または **--account-name** &lt;accountName>: ストレージ アカウント名。
* **-k** または **--account-key** &lt;accountKey>: ストレージ アカウント キー。
* **-c** または **--connection-string** &lt;connectionString>: ストレージ接続文字列。
* **--debug**: デバッグ モードでストレージ コマンドを実行します。

**storage container show [options] [container]**
**storage container create [options] [container]**

このコマンドは、指定されたストレージ アカウントのストレージ コンテナーを作成します。 ストレージ アカウントは、接続文字列、またはストレージ アカウント名とアカウント キーで指定します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--container** &lt;container>: 作成するストレージ コンテナーの名前。
* **-p** または **-prefix** &lt;prefix>: ストレージ コンテナー名のプレフィックス。
* **-a** または **--account-name** &lt;accountName>: ストレージ アカウント名
* **-k** または **--account-key** &lt;accountKey>: ストレージ アカウント キー
* **-c** または **--connection-string** &lt;connectionString>: ストレージ接続文字列
* **--debug**: デバッグ モードでストレージ コマンドを実行します。

**storage container delete [options] [container]**

このコマンドは、指定されたストレージ コンテナーを削除します。 ストレージ アカウントは、接続文字列、またはストレージ アカウント名とアカウント キーで指定します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--container** &lt;container>: 作成するストレージ コンテナーの名前。
* **-p** または **-prefix** &lt;prefix>: ストレージ コンテナー名のプレフィックス。
* **-a** または **--account-name** &lt;accountName>: ストレージ アカウント名。
* **-k** または **--account-key** &lt;accountKey>: ストレージ アカウント キー。
* **-c** または **--connection-string** &lt;connectionString>: ストレージ接続文字列。
* **--debug**: デバッグ モードでストレージ コマンドを実行します。

**storage container set [options] [container]**

このコマンドは、ストレージ コンテナーのアクセス制御リストを指定します。 ストレージ アカウントは、接続文字列、またはストレージ アカウント名とアカウント キーで指定します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--container** &lt;container>: 作成するストレージ コンテナーの名前。
* **-p** または **-prefix** &lt;prefix>: ストレージ コンテナー名のプレフィックス。
* **-a** または **--account-name** &lt;accountName>: ストレージ アカウント名。
* **-k** または **--account-key** &lt;accountKey>: ストレージ アカウント キー。
* **-c** または **--connection-string** &lt;connectionString>: ストレージ接続文字列。
* **--debug**: デバッグ モードでストレージ コマンドを実行します。

### <a name="commands-to-manage-your-storage-blob"></a>ストレージ BLOB の管理用コマンド
**storage blob list [options] [container] [prefix]**

このコマンドは、指定されたストレージ コンテナーのストレージ BLOB の一覧を返します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--container** &lt;container>: 作成するストレージ コンテナーの名前。
* **-p** または **-prefix** &lt;prefix>: ストレージ コンテナー名のプレフィックス。
* **-a** または **--account-name** &lt;accountName>: ストレージ アカウント名。
* **-k** または **--account-key** &lt;accountKey>: ストレージ アカウント キー。
* **-c** または **--connection-string** &lt;connectionString>: ストレージ接続文字列。
* **--debug**: デバッグ モードでストレージ コマンドを実行します。

**storage blob show [options] [container] [blob]**

このコマンドは、指定されたストレージ BLOB の詳細を表示します。

このコマンドでは、次の追加のオプションがサポートされています。

* **--container** &lt;container>: 作成するストレージ コンテナーの名前。
* **-p** または **-prefix** &lt;prefix>: ストレージ コンテナー名のプレフィックス。
* **-a** または **--account-name** &lt;accountName>: ストレージ アカウント名。
* **-k** または **--account-key** &lt;accountKey>: ストレージ アカウント キー。
* **-c** または **--connection-string** &lt;connectionString>: ストレージ接続文字列。
* **--debug**: デバッグでストレージ コマンドを実行します。

**storage blob delete [options] [container] [blob]**

このコマンドでは、次の追加のオプションがサポートされています。

* **--container** &lt;container>: 作成するストレージ コンテナーの名前。
* **-b** または **--blob** &lt;blobName>: 削除するストレージ BLOB の名前。
* **-q** または **--quiet**: 指定されたストレージ BLOB を、確認なしで削除します。
* **-a** または **--account-name** &lt;accountName>: ストレージ アカウント名。
* **-k** または **--account-key** &lt;accountKey>: ストレージ アカウント キー。
* **-c** または **--connection-string** &lt;connectionString>: ストレージ接続文字列。
* **--debug**: デバッグでストレージ コマンドを実行します。

**storage blob upload [options] [file] [container] [blob]**

このコマンドは、指定されたファイルを指定されたストレージ BLOB にアップロードします。

このコマンドでは、次の追加のオプションがサポートされています。

* **--container** &lt;container>: 作成するストレージ コンテナーの名前。
* **-b** または **--blob** &lt;blobName>: アップロードするストレージ BLOB の名前。
* **-t** または **--blobtype** &lt;blobtype>: ストレージ BLOB の種類: Page または Block。
* **-p** または **--properties** &lt;properties>: アップロードされたファイルのストレージ BLOB のプロパティ。 プロパティは、キーと値のペアで、セミコロン (;) で区切られています。 使用可能なプロパティは、contentType、contentEncoding、contentLanguage、cacheControl です。
* **-m** または **--metadata** &lt;metadata>: アップロードされたファイルのストレージ BLOB のメタデータ。 メタデータは、キーと値のペアで、セミコロン (;) で区切られています。
* **--concurrenttaskcount** &lt;concurrenttaskcount>: 同時アップロード要求の最大数。
* **-q** または **--quiet**: 指定されたストレージ BLOB を、確認なしで上書きします。
* **-a** または **--account-name** &lt;accountName>: ストレージ アカウント名。
* **-k** または **--account-key** &lt;accountKey>: ストレージ アカウント キー。
* **-c** または **--connection-string** &lt;connectionString>: ストレージ接続文字列。
* **--debug**: デバッグでストレージ コマンドを実行します。

**storage blob download [options] [container] [blob] [destination]**

このコマンドは、指定されたストレージ BLOB をダウンロードします。

このコマンドでは、次の追加のオプションがサポートされています。

* **--container** &lt;container>: 作成するストレージ コンテナーの名前。
* **-b** または **--blob** &lt;blobName>: ストレージ BLOB の名前。
* **-d** または **--destination** [destination]: ダウンロード先のファイルまたはディレクトリのパス。
* **-m** または **--checkmd5**: ダウンロードされたファイルの MD5 チェックサム。
* **--concurrenttaskcount** &lt;concurrenttaskcount>: 同時アップロード要求の最大数。
* **-q** または **--quiet**: 対象ファイルを、確認なしで上書きします。
* **-a** または **--account-name** &lt;accountName>: ストレージ アカウント名。
* **-k** または **--account-key** &lt;accountKey>: ストレージ アカウント キー。
* **-c** または **--connection-string** &lt;connectionString>: ストレージ接続文字列。
* **--debug**: デバッグでストレージ コマンドを実行します。

## <a name="commands-to-manage-sql-databases"></a>SQL Database の管理用コマンド
これらのコマンドを使用して、Azure SQL Database を管理します。

### <a name="commands-to-manage-sql-servers"></a>SQL サーバーの管理用コマンド
これらのコマンドを使用して、SQL サーバーを管理します。

**sql server create &lt;administratorLogin> &lt;administratorPassword> &lt;location>**

データベース サーバーを作成します。

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**sql server show &lt;name>**

サーバーの詳細を表示します。

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**sql server list**

サーバーの一覧を取得します。

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**sql server delete &lt;name>**

サーバーを削除します。

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts? [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

### <a name="commands-to-manage-sql-databases"></a>SQL Database の管理用コマンド
以下のコマンドを使用して、SQL Database を管理します。

**sql db create [options] &lt;serverName> &lt;databaseName> &lt;administratorPassword>**

データベース インスタンスを作成します。

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**sql db show [options] &lt;serverName> &lt;databaseName> &lt;administratorPassword>**

データベースの詳細を表示します。

    C:\windows\system32>azure sql db show fr8aelne00 newdb test
    info:    Executing command sql db show
    Administrator password: ********
    + Getting SQL server databases
    data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
    ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
    m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
    icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
    ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
    ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
    /Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
    ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
    Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
    services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
    tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
    om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
    pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
    d=2013-11-18T19:48:27Z, name=
    data:    Database Id 4
    data:    Database Name newdb
    data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database ServiceObjectiveAssignmentState 1
    data:    Database ServiceObjectiveAssignmentStateDescription Complete
    data:    Database ServiceObjectiveAssignmentErrorCode
    data:    Database ServiceObjectiveAssignmentErrorDescription
    data:    Database ServiceObjectiveAssignmentSuccessDate
    data:    Database Edition Web
    data:    Database MaxSizeGB 1
    data:    Database MaxSizeBytes 1073741824
    data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
    data:    Database CreationDate
    data:    Database RecoveryPeriodStartDate
    data:    Database IsSystemObject
    data:    Database Status 1
    data:    Database IsFederationRoot
    data:    Database SizeMB -1
    data:    Database IsRecursiveTriggersOn
    data:    Database IsReadOnly
    data:    Database IsFederationMember
    data:    Database IsQueryStoreOn
    data:    Database IsQueryStoreReadOnly
    data:    Database QueryStoreMaxSizeMB
    data:    Database QueryStoreFlushPeriodSeconds
    data:    Database QueryStoreIntervalLengthMinutes
    data:    Database QueryStoreClearAll
    data:    Database QueryStoreStaleQueryThresholdDays
    info:    sql db show command OK

**sql db list [options] &lt;serverName> &lt;administratorPassword>**

データベースの一覧を表示します。

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**sql db delete [options] &lt;serverName> &lt;databaseName> &lt;administratorPassword>**

データベースを削除します。

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb? [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

### <a name="commands-to-manage-your-sql-server-firewall-rules"></a>SQL Server のファイアウォール ルールの管理用コマンド
これらのコマンドを使用して、SQL Server のファイアウォール ルールを管理します。

**sql firewallrule create [options] &lt;serverName> &lt;ruleName> &lt;startIPAddress> &lt;endIPAddress>**

SQL Server のファイアウォール ルールを作成します。

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**sql firewallrule show [options] &lt;serverName> &lt;ruleName>**

ファイアウォール ルールの詳細を表示します。

    ~$ azure sql firewallrule show fr8aelne00 allowed
    info:    Executing command sql firewallrule show
    + Getting firewall rule
    data:    Firewall rule Name allowed
    data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
    data:    Firewall rule State Normal
    data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
    5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
    data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
    055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
    data:    Firewall rule StartIPAddress 131.107.0.0
    data:    Firewall rule EndIPAddress 131.107.255.255
    info:    sql firewallrule show command OK

**sql firewallrule list [options] &lt;serverName>**

ファイアウォール ルールの一覧を表示します。

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**sql firewallrule delete [options] &lt;serverName> &lt;ruleName>**

このコマンドは、ファイアウォール ルールを削除します。

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed? [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

## <a name="commands-to-manage-your-virtual-networks"></a>仮想ネットワークの管理用コマンド
これらのコマンドを使用して、仮想ネットワークを管理します。

**network vnet create [options] &lt;location>**

仮想ネットワークを作成します。

    ~$ azure network vnet create vnet1 --location "West US" -v
    info:    Executing command network vnet create
    info:    Using default address space start IP: 10.0.0.0
    info:    Using default address space cidr: 8
    info:    Using default subnet start IP: 10.0.0.0
    info:    Using default subnet cidr: 11
    verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
    verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
    verbose: Fetching Network Configuration
    verbose: Fetching or creating affinity group
    verbose: Fetching Affinity Groups
    verbose: Fetching Locations
    verbose: Creating new affinity group AG1
    info:    Using affinity group AG1
    verbose: Updating Network Configuration
    info:    network vnet create command OK

**network vnet show &lt;name>**

Virtual Network の詳細を表示します。

    ~$ azure network vnet show vnet1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "vnet1"
    data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
    data:    AffinityGroup "AG1"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
    data:    Subnets 0 Name "subnet-1"
    data:    Subnets 0 AddressPrefix "10.0.0.0/11"
    info:    network vnet show command OK

**network vnet list**

既存のすべての仮想ネットワークの一覧を表示します。

    ~$ azure network vnet list
    info:    Executing command network vnet list
    + Fetching Virtual Networks
    data:    Name        Status   AffinityGroup
    data:    ----------  -------  -------------
    data:    vnet1      Created  AG1
    data:    vnet2      Created  AG1
    data:    vnet3      Created  AG1
    data:    vnet4      Created  AG1
    info:    network vnet list command OK


**network vnet delete &lt;name>**

指定された Virtual Network を削除します。

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 ?  (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**network export [file-path]**

高度なネットワーク構成では、ネットワーク構成をローカルにエクスポートできます。 エクスポートされるネットワーク構成には、DNS サーバー設定、仮想ネットワーク設定、ローカル ネットワーク サイト設定などが含まれます。

**network import [file-path]**

ローカル ネットワーク構成をインポートします。

**network dnsserver register [options] &lt;dnsIP>**

ネットワーク構成に、名前の解決に使用する DNS サーバーを登録します。

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**network dnsserver list**

ネットワーク構成に登録されているすべての DNS サーバーの一覧を表示します。

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**network dnsserver unregister [options] &lt;dnsIP>**

ネットワーク構成から DNS サーバー エントリを削除します。

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK




<!--HONumber=Jan17_HO1-->


