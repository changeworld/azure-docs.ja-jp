<properties
   pageTitle="Azure コンテナー サービス クラスターのデプロイ | Microsoft Azure"
   description="Azure ポータル、Azure CLI、PowerShell を利用して Azure コンテナー サービスをデプロイします。"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、コンテナー、マイクロ サービス、Mesos、Azure"/>
   
<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="rogardle"/>
   
# Azure コンテナー サービス クラスターのデプロイ

Azure コンテナー サービスでは、人気のオープン ソースのコンテナー クラスタリングやオーケストレーション ソリューションを短期間でデプロイできます。Azure コンテナー サービスと Azure リソース マネージャーまたはポータルを利用し、Marathon Mesos クラスターと Docker Swarm クラスターをデプロイできます。これらのクラスターは Azure 仮想マシン スケール セットでデプロイされ、Azure ネットワーキングとストレージ サービスが活用されます。Azure コンテナー サービスにアクセスするには、Azure サブスクリプションが必要です。お持ちでない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)にサインアップしてください。

このドキュメントでは、[Azure Portal](#creating-a-service-using-the-azure-portal)、[Azure CLI](#creating-a-service-using-the-azure-cli)、[Azure PowerShell モジュール](#creating-a-service-using-powershell)を利用し、Azure コンテナー サービスをデプロイする方法を段階的に説明します。
   
## Azure ポータルを使用してサービスを作成する
 
次のテンプレートのいずれかを選択し、Mesos または Docker Swarm クラスターをデプロイします。**注** - 既定のオーケストレーターの選択を除き、これらのテンプレートは同じです。
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
 
いずれのテンプレート ページにも「deploy to Azure (Azure にデプロイする)」ボタンがあります。このボタンをクリックすると、Azure ポータルが起動します。フォームは次のようになります。 <br />

![Create deployment](media/create-mesos-params.png) <br />

このガイダンスを使用してフォームに入力し、完了したら [OK] を選択します。 <br />

フィールド | 説明
----------------|-----------
DNSNAMEPREFIX | これは世界で一意の値にする必要があります。サービスの重要な部分で DNS 名を作成するために使用されます。詳細は下にあります。
AGENTCOUNT | これは ACS エージェント スケール セットで作成される仮想マシンの数です。
AGENTVMSIZE | エージェントの仮想マシンのサイズを指定します。最も大きなコンテナーをホストできるだけのリソースを提供するサイズを選択してください。
ADMINUSERNAME | これは、ACS クラスターのそれぞれの仮想マシンと仮想マシン スケール セットのアカウントに使用されるユーザー名です。
ORCHESTRATORTYPE| ACS クラスターで使用するオーケストレーターを選択します。
MASTERCOUNT | これはクラスターのマスターとして構成する仮想マシンの数です。1 を選択できますが、クラスターに回復力が与えられません。テストのみで推奨されます。運用環境のクラスターに推奨される数は 3 または 5 です。 
SSHRSAPUBLICKEY | 仮想マシンの認証には SSH を使用する必要があります。これは公開キーを追加する場所です。このボックスにキーの値を貼り付けるときは注意する必要があります。エディターによってはコンテンツに改行が挿入され、キーが壊れることがあります。キーに改行がないことと接頭辞の「ssh-rsa」と接尾辞の「username@domain」が含まれていることを確認します。「ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'」のようになります。SSH キーを作成する必要がある場合、Azure ドキュメント サイトに [Windows](../virtual-machines/virtual-machines-windows-use-ssh-key.md) と [Linux](../virtual-machines/virtual-machines-linux-use-ssh-key.md) のガイダンスがあります。
  
パラメーターに適切な値を設定したら、[OK] をクリックします。次に、リソース グループ名を指定し、リージョンを選択し、法的規約を確認し、同意します。

> プレビューの際は、Azure コンテナー サービスの課金がなく、仮想マシン、ストレージ、ネットワーキングなど、標準のコンピューティング課金だけです。
 
![リソース グループの選択](media/resourcegroup.png)
 
最後に、[作成] をクリックします。ダッシュボードに戻ります。デプロイ ブレードで [ダッシュボードにピン留めする] を選択していなければ、次のようなアニメーション付きのタイトルが表示されます。

![デプロイ](media/deploy.png)
 
クラスターが作成されるまでしばらくお待ちください。完了すると、一部のブレードに ACS クラスターを構成するリソースが表示されます。
 
![Finished](media/final.png)

## Azure CLI を使用してサービスを作成する

コマンドラインを使用して Azure コンテナー サービスのインスタンスを作成するには、Azure サブスクリプションが必要です。サブスクリプションがない場合でも、無料試用版にサインアップできます。また、Azure CLI をインストールし、構成する必要があります。
 
次のテンプレートのいずれかを選択し、Mesos または Docker Swarm クラスターをデプロイします。**注** - 既定のオーケストレーターの選択を除き、これらのテンプレートは同じです。
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
 
次に、Azure CLI が Azure サブスクリプションに接続されたことを確認します。これは次のコマンドを使用して行うことができます。

```bash
Azure account show
```
Azure アカウントが返されない場合、次を使用して CLI を Azure にログインさせます。
 
```bash
azure login -u user@domain.com
```

次に、Azure リソース マネージャーを使用するように Azure CLI ツールを構成します。
 
```bash
azure config mode arm
```
 
新しいリソース グループにクラスターを作成する場合、最初にリソース グループを作成する必要があります。このコマンドを使用します。`GROUP_NAME` は作成するリソース グループの名前です。`REGION` はリソース グループを作成するリージョンです。
 
```bash
azure group create GROUP_NAME REGION
```
 
リソース グループが用意できたら、このコマンドでクラスターを作成できます。

- **RESOURCE\_GROUP** は、このサービスで使用するリソース グループの名前です。
- **DEPLOYMENT\_NAME** はこのデプロイの名前です。
- **TEMPLATE\_URI** はデプロイ ファイルの場所です。**注** - これは RAW ファイルになります。GitHub UI のポインターではありません。この URL を見つけるには、GitHub で azuredeploy.json ファイルを選択し、RAW ボタンをクリックします。

> 注 - このコマンドを実行すると、シェルからデプロイのパラメーター値の入力が求められます。
 
```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI
```
 
### テンプレート パラメーターを指定する
 
このバージョンのコマンドでは、パラメーターを対話で定義する必要があります。JSON で書式設定された文字列としてパラメーターを指定する場合、`-p` スイッチでそれを実行できます。次に例を示します。
 
 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
 ```

あるいは、`-e` スイッチを利用し、JSON で書式設定されたパラメーターを指定できます。

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON'
 ```
 
GitHub には、「`azuredeploy.parameters.json`」という名前のサンプル パラメーター ファイルと ACS テンプレートがあります。
 
## PowerShell を使用してサービスを作成する

ACS クラスターは PowerShell でデプロイすることもできます。このドキュメントはバージョン 1.0 以降の Azure [PowerShell モジュール](https://azure.microsoft.com/blog/azps-1-0/)に基づいています。

次のテンプレートのいずれかを選択し、Mesos または Docker Swarm クラスターをデプロイします。**注** - 既定のオーケストレーターの選択を除き、これらのテンプレートは同じです。
 
* Mesos: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* Swarm: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Azure サブスクリプションでクラスターを作成する前に、PowerShell セッションが Azure にログインしていることを確認します。これは `Get-AzureRMSubscription` コマンドで完了できます。

```powershell
Get-AzureRmSubscription
```

Azure にログインする必要がある場合、`Login-AzureRMAccount` コマンドを使用します。

```powershell
Login-AzureRmAccount
```
 
新しいリソース グループにデプロイする場合、最初にリソース グループを作成する必要があります。新しいリソース グループを作成するには、`New-AzureRmResourceGroup` コマンドを使用し、リソース グループの名前とターゲット リージョンを指定します。

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

リソース グループが用意できたら、次のコマンドでクラスターを作成できます。目的のテンプレートの URI は `-TemplateUri` パラメーターに指定されます。このコマンドを実行すると、PowerShell からデプロイのパラメーター値の入力が求められます。

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```
 
### テンプレート パラメーターを指定する
 
PowerShell に慣れている場合は、マイナス記号 (-) を入力して Tab キーを押すことで、コマンドレットで利用可能なパラメーターを順番に表示できることをご存じのことと思われます。この機能は、テンプレートで定義するパラメーターでも同様に使用できます。 テンプレート名を入力するとすぐに、コマンドレットがそのテンプレートをフェッチし、パラメーターを解析して、テンプレート パラメーターをコマンドに動的に追加します。 Tこれにより、テンプレート パラメーターの値の指定が非常に簡単になります。 また、必須のパラメーター値を忘れた場合は、PowerShell から値を求められます。
 
パラメーターが含まれている完全なコマンドを以下に示します。 リソースの名前には独自の値を指定できます。

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE\_GROUP\_NAME-TemplateURI TEMPLATE\_URI -adminuser value1 -adminpassword value2 ....
```
 
## 次のステップ
 
これでクラスターが機能します。接続と管理の詳細については、次のドキュメントを参照してください。
 
- [ACS クラスターに接続する](./container-service-connect.md)
- [ACS と Mesos を使用する](./container-service-mesos-marathon-rest.md)

 

<!-----HONumber=AcomDC_0224_2016-->