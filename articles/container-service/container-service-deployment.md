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

Azure コンテナー サービスでは、人気のオープン ソースのコンテナー クラスタリングやオーケストレーション ソリューションを短期間でデプロイできます。Azure コンテナー サービスと Azure リソース マネージャーまたは Azure ポータルを利用し、Marathon Mesos クラスターと Docker Swarm クラスターをデプロイできます。これらのクラスターは Azure 仮想マシン スケール セットでデプロイされ、Azure ネットワーキングとストレージ サービスが活用されます。Azure コンテナー サービスにアクセスするには、Azure サブスクリプションが必要です。サブスクリプションがない場合でも、[無料試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)にサインアップできます。

このドキュメントでは、[Azure ポータル](#creating-a-service-using-the-azure-portal)、[Azure コマンド ライン インターフェイス (CLI)](#creating-a-service-using-the-azure-cli)、[Azure PowerShell モジュール](#creating-a-service-using-powershell)を利用し、Azure コンテナー サービスをデプロイする方法を段階的に説明します。

## Azure ポータルを使用してサービスを作成する

Mesos または Docker Swarm クラスターをデプロイするには、GitHub から次のテンプレートのいずれかを選択します。既定のオーケストレーターの選択を除き、これらのテンプレートは同じです。

* [Mesos テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

どちらかのテンプレート ページで **[Azure にデプロイする]** ボタンを選択すると、Azure ポータルが起動します。フォームは次のようになります。 <br />

![フォームを使用してデプロイを作成する](media/create-mesos-params.png) <br />

このガイダンスを使用してフォームに入力し、完了したら **[OK]** を選択します。 <br />

フィールド | 説明
----------------|-----------
DNSNAMEPREFIX | これは世界で一意の値にする必要があります。サービスの重要な部分で DNS 名を作成するために使用されます。詳細については、この記事の後半を参照してください。
AGENTCOUNT | これは、Azure コンテナー サービス エージェント スケール セットで作成される仮想マシンの数です。
AGENTVMSIZE | これは、エージェントの仮想マシンのサイズです。最も大きなコンテナーをホストできるだけのリソースを提供するサイズを選択してください。
ADMINUSERNAME | これは、Azure コンテナー サービス クラスターのそれぞれの仮想マシンと仮想マシン スケール セットのアカウントに使用されるユーザー名です。
ORCHESTRATORTYPE| これは、Azure コンテナー サービス クラスターに使用する orchestrator 型です。
MASTERCOUNT | これは、クラスターのマスターとして構成する仮想マシンの数です。1 を選択できますが、クラスターに回復力が与えられません。テストのみで推奨されます。運用環境のクラスターに推奨される数は 3 または 5 です。
SSHRSAPUBLICKEY | 仮想マシンの認証には Secure Shell (SSH) を使用する必要があります。これは公開キーを追加する場所です。このボックスにキーの値を貼り付けるときは注意する必要があります。エディターによってはコンテンツに改行が挿入され、キーが壊れることがあります。キーに改行がないことと接頭辞の "ssh-rsa" と接尾辞の "username@domain" が含まれていることを確認します。"ssh-rsa AAAAB3Nz...SNIPPEDCONTENT...UcyupgH azureuser@linuxvm'" のようになります。SSH キーを作成する必要がある場合、Azure ドキュメント サイトに [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) と [Linux](../virtual-machines/virtual-machines-linux-ssh-from-linux.md) のガイダンスがあります。

パラメーターに適切な値を設定したら、**[OK]** を選択します。次に、リソース グループ名を指定し、リージョンを選択し、法的規約を確認し、同意します。

> [AZURE.NOTE] プレビューの際は、Azure コンテナー サービスの課金がなく、仮想マシン、ストレージ、ネットワーキングなど、標準のコンピューティング課金だけです。

![リソース グループの選択](media/resourcegroup.png)

最後に、**[作成]** を選択します。ダッシュボードに戻ります。デプロイ ブレードで **[ダッシュボードにピン留めする]** チェック ボックスをオフにしていなければ、次のようなアニメーション付きのタイトルが表示されます。

![テンプレート タイルのデプロイ](media/deploy.png)

クラスターが作成されるまでしばらくお待ちください。クラスターが作成されると、一部のブレードに Azure コンテナー サービス クラスターを構成するリソースが表示されます。

![Finished](media/final.png)

## Azure CLI を使用してサービスを作成する

コマンド ラインを使用して Azure コンテナー サービスのインスタンスを作成するには、Azure サブスクリプションが必要です。サブスクリプションがない場合でも、[無料試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)にサインアップできます。また、Azure CLI をインストールし、構成する必要があります。

Mesos または Docker Swarm クラスターをデプロイするには、GitHub から次のテンプレートのいずれかを選択します。既定のオーケストレーターの選択を除き、これらのテンプレートは同じです。

* [Mesos テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

次に、Azure CLI が Azure サブスクリプションに接続されたことを確認します。この処理には、次のコマンドを使用できます。

```bash
Azure account show
```
Azure アカウントが返されない場合、次のコマンドを使用して CLI を Azure にサインインさせます。

```bash
azure login -u user@domain.com
```

次に、Azure リソース マネージャーを使用するように Azure CLI ツールを構成します。

```bash
azure config mode arm
```

新しいリソース グループにクラスターを作成する場合、最初にリソース グループを作成する必要があります。次のコマンドを使用します。`GROUP_NAME` は作成するリソース グループの名前です。`REGION` はリソース グループを作成するリージョンです。

```bash
azure group create GROUP_NAME REGION
```

リソース グループを作成したら、このコマンドでクラスターを作成できます。

- **RESOURCE\_GROUP** は、このサービスで使用するリソース グループの名前です。
- **DEPLOYMENT\_NAME** はこのデプロイの名前です。
- **TEMPLATE\_URI** はデプロイ ファイルの場所です。これは RAW ファイルになります。GitHub UI のポインターでは*ありません*。この URL を見つけるには、GitHub で azuredeploy.json ファイルを選択し、**[RAW]** ボタンをクリックします。

> [AZURE.NOTE] このコマンドを実行すると、シェルからデプロイのパラメーター値の入力が求められます。

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
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
 ```

`azuredeploy.parameters.json` という名前のサンプル パラメーター ファイルを参照するには、GitHub で Azure コンテナー サービス テンプレートと共にこのファイルを検索します。

## PowerShell を使用してサービスを作成する

PowerShell を使用して Azure コンテナー サービス クラスターをデプロイすることもできます。このドキュメントはバージョン 1.0 の [Azure PowerShell モジュール](https://azure.microsoft.com/blog/azps-1-0/)に基づいています。

Mesos または Docker Swarm クラスターをデプロイするには、次のテンプレートのいずれかを選択します。既定のオーケストレーターの選択を除き、これらのテンプレートは同じです。

* [Mesos テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-mesos)
* [Swarm テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)

Azure サブスクリプションでクラスターを作成する前に、PowerShell セッションが Azure にサインインしていることを確認します。そのためには、`Get-AzureRMSubscription` コマンドを使用します。

```powershell
Get-AzureRmSubscription
```

Azure にサインインする必要がある場合、`Login-AzureRMAccount` コマンドを使用します。

```powershell
Login-AzureRmAccount
```

新しいリソース グループにデプロイする場合、最初にリソース グループを作成する必要があります。新しいリソース グループを作成するには、`New-AzureRmResourceGroup` コマンドを使用し、リソース グループの名前とターゲット リージョンを指定します。

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

リソース グループを作成したら、次のコマンドでクラスターを作成できます。目的のテンプレートの URI は `-TemplateUri` パラメーターに指定されます。このコマンドを実行すると、PowerShell からデプロイのパラメーター値の入力が求められます。

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
 ```

### テンプレート パラメーターを指定する

PowerShell に慣れている場合は、マイナス記号 (-) を入力して Tab キーを押すことで、コマンドレットで利用可能なパラメーターを順番に表示できることをご存じのことと思われます。この機能は、テンプレートで定義するパラメーターでも同様に使用できます。テンプレート名を入力すると、コマンドレットがすぐにテンプレートをフェッチし、パラメーターを解析して、テンプレート パラメーターをコマンドに動的に追加します。これにより、テンプレート パラメーターの値の指定が非常に簡単になります。また、必須のパラメーター値を忘れた場合は、PowerShell から値を求められます。

パラメーターが含まれている完全なコマンドを以下に示します。 リソースの名前には独自の値を指定できます。

```
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## 次のステップ

これでクラスターが機能します。接続と管理の詳細については、次の記事を参照してください。

- [Azure コンテナー サービス クラスターに接続する](./container-service-connect.md)
- [REST API によるコンテナー管理](./container-service-mesos-marathon-rest.md)

<!---HONumber=AcomDC_0413_2016-->