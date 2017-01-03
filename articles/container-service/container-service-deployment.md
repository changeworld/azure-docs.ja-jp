---
title: "Azure Container Service クラスターのデプロイ | Microsoft Docs"
description: "Azure ポータル、Azure CLI、PowerShell を利用して Azure コンテナー サービスをデプロイします。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Mesos, Azure, dcos, swarm, kubernetes, Azure Container Service, acs"
ms.assetid: 696a736f-9299-4613-88c6-7177089cfc23
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 52f158fd50ee8427cf567889d584e342ea42abb3
ms.openlocfilehash: b52f7b36a28a345e8693ecbafd3771c27c683a37


---
# <a name="deploy-an-azure-container-service-cluster"></a>Azure コンテナー サービス クラスターのデプロイ
Azure コンテナー サービスでは、人気のオープン ソースのコンテナー クラスタリングやオーケストレーション ソリューションを短期間でデプロイできます。 Azure Container Service のほか、Azure Resource Manager テンプレートまたは Azure Portal を利用することで、DC/OS クラスター、Kubernetes クラスター、Docker Swarm クラスターをデプロイできます。 これらのクラスターは Azure 仮想マシン スケール セットでデプロイされ、Azure ネットワーキングとストレージ サービスが活用されます。 Azure コンテナー サービスにアクセスするには、Azure サブスクリプションが必要です。 サブスクリプションがない場合でも、 [無料試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)にサインアップできます。

このドキュメントでは、[Azure Portal](#creating-a-service-using-the-azure-portal)、[Azure コマンド ライン インターフェイス (CLI)](#creating-a-service-using-the-azure-cli)、[Azure PowerShell モジュール](#creating-a-service-using-powershell)を使用して、Azure Container Service クラスターをデプロイする方法を段階的に説明します。  

## <a name="create-a-service-by-using-the-azure-portal"></a>Azure ポータルを使用してサービスを作成する
Azure Portal にサインインし、**[新規]** を選択して、Azure Marketplace で **Azure Container Service** を検索します。

![Create deployment 1](media/acs-portal1.png)  <br />

**[Azure Container Service]** を選択し、**[作成]** をクリックします。

![Create deployment 2](media/acs-portal2.png)  <br />

次の情報を入力します。

* **[ユーザー名]**: Azure コンテナー サービス クラスターの各仮想マシンと仮想マシン スケール セットのアカウントに使用されるユーザー名です。
* **[サブスクリプション]**: Azure サブスクリプションを選択します。
* **[リソース グループ]**: 既存のリソース グループを選択するか、新しいリソース グループを作成します。
* **[場所]**: Azure コンテナー サービスのデプロイの Azure リージョンを選択します。
* **[SSH 公開キー]**: Azure Container Service の Virtual Machines に対する認証に使用する公開キーを追加します。 このキーには改行を含めないでください。また、先頭に 'ssh-rsa'、末尾に 'username@domain' を付ける必要があります。 **ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm** のようになります。 Secure Shell (SSH) キーの作成方法については、[Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-linux/) の記事と [Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-ssh-from-windows/) の記事を参照してください。

準備が完了したら、 **[OK]** をクリックします。

![Create deployment 3](media/acs-portal3.png)  <br />

オーケストレーションの種類を選択します。 オプションは次のとおりです。

* **[DC/OS]**: DC/OS クラスターをデプロイします。
* **[Swarm]**: Docker Swarm クラスターをデプロイします。
* **[Kubernetes]**: Kubernetes クラスターをデプロイします。

準備が完了したら、 **[OK]** をクリックします。

![Create deployment 4](media/acs-portal4-new.png)  <br />

ドロップダウン ボックスの一覧で **[Kubernetes]** を選択した場合は、サービス プリンシパルのクライアント ID とクライアント シークレットを入力する必要があります。 詳細については、[Kubernetes クラスターのサービス プリンシパル](container-service-kubernetes-service-principal.md)に関するページを参照してください。 

![デプロイ 4.5 の作成](media/acs-portal10.PNG)  <br />

次の情報を入力します。

* **[Master count (マスター数)]**: クラスターのマスター数。 [Kubernetes] を選択すると、マスターの数は既定の 1 に設定されます。
* **[Agent count (エージェント数)]**: Docker Swarm と Kubernetes の場合、エージェント スケール セットのエージェントの初期数になります。 DC/OS の場合、プライベート スケール セットのエージェントの初期数です。 また、事前に決められた数のエージェントを含むパブリック スケール セットが作成されます。 このパブリック スケール セットのエージェント数は、クラスターに作成されたマスター数によって決まります (1 マスターに 1 パブリック エージェント、3 または 5 マスターに 2 パブリック エージェント)。
* **[Agent virtual machine size (エージェント仮想マシン サイズ)]**: エージェント仮想マシンのサイズ。
* **[DNS プレフィックス]**: サービス名の完全修飾ドメイン名の主要部分の先頭に付ける世界で一意の名前。

準備が完了したら、 **[OK]** をクリックします。

![Create deployment 5](media/acs-portal5.png)  <br />

サービスの検証が完了したら、 **[OK]** をクリックします。

![Create deployment 6](media/acs-portal6.png)  <br />

**[作成]** をクリックしてデプロイ プロセスを開始します。

![Create deployment 7](media/acs-portal7.png)  <br />

デプロイを Azure ポータルに固定した場合、デプロイの状態を確認できます。

![Create deployment 8](media/acs-portal8.png)  <br />

デプロイが完了したら、Azure コンテナー サービス クラスターを利用できます。

## <a name="create-a-service-by-using-the-azure-cli"></a>Azure CLI を使用してサービスを作成する
コマンド ラインを使用して Azure コンテナー サービスのインスタンスを作成するには、Azure サブスクリプションが必要です。 サブスクリプションがない場合でも、 [無料試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)にサインアップできます。 また、Azure CLI を[インストール](../xplat-cli-install.md)し、[構成](../xplat-cli-connect.md)する必要があります。

DC/OS、Docker Swarm、または Kubernetes のクラスターをデプロイするには、GitHub から次のテンプレートのいずれかを選択します。 

* [DC/OS テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Swarm テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
* [Kubernetes テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

次に、Azure CLI が Azure サブスクリプションに接続されたことを確認します。 この処理には、次のコマンドを使用できます。

```bash
azure account show
```
Azure アカウントが返されない場合、次のコマンドを使用して CLI を Azure にサインインさせます。

```bash
azure login -u user@domain.com
```

次に、Azure リソース マネージャーを使用するように Azure CLI ツールを構成します。

```bash
azure config mode arm
```

次のコマンドを使用して、Azure リソース グループとコンテナー サービス クラスターを作成します。

* **RESOURCE_GROUP** は、このサービスで使用するリソース グループの名前です。
* **LOCATION** は、リソース グループと Azure コンテナー サービスのデプロイを作成する Azure リージョンです。
* **TEMPLATE_URI** は、デプロイ ファイルの場所です。 これは Raw ファイルになります。GitHub UI のポインターではありません。 この URL を見つけるには、GitHub で azuredeploy.json ファイルを選択し、**[Raw]** ボタンをクリックします。

> [!NOTE]
> このコマンドを実行すると、シェルからデプロイのパラメーター値の入力が求められます。
> 
> 

```bash
azure group create -n RESOURCE_GROUP DEPLOYMENT_NAME -l LOCATION --template-uri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>テンプレート パラメーターを指定する
このバージョンのコマンドでは、パラメーターを対話で定義する必要があります。 JSON で書式設定された文字列などのパラメーターを指定する場合、 `-p` スイッチでそれを実行できます。 次に例を示します。

 ```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -p '{ "param1": "value1" … }'
```

または、 `-e` スイッチを利用し、JSON で書式設定されたパラメーター ファイルを指定することもできます。

```bash
azure group deployment create RESOURCE_GROUP DEPLOYMENT_NAME --template-uri TEMPLATE_URI -e PATH/FILE.JSON
```

`azuredeploy.parameters.json`という名前のサンプル パラメーター ファイルを参照するには、GitHub で Azure コンテナー サービス テンプレートと共にこのファイルを検索します。

## <a name="create-a-service-by-using-powershell"></a>PowerShell を使用してサービスを作成する
PowerShell を使用して Azure コンテナー サービス クラスターをデプロイすることもできます。 このドキュメントはバージョン 1.0 の [Azure PowerShell モジュール](https://azure.microsoft.com/blog/azps-1-0/)に基づいています。

DC/OS、Docker Swarm、または Kubernetes のクラスターをデプロイするには、次のテンプレートのいずれかを選択します。 既定のオーケストレーターの選択を除き、これらのテンプレートは同じです。

* [DC/OS テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
* [Swarm テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
* [Kubernetes テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

Azure サブスクリプションでクラスターを作成する前に、PowerShell セッションが Azure にサインインしていることを確認します。 そのためには、 `Get-AzureRMSubscription` コマンドを使用します。

```powershell
Get-AzureRmSubscription
```

Azure にサインインする必要がある場合、 `Login-AzureRMAccount` コマンドを使用します。

```powershell
Login-AzureRmAccount
```

新しいリソース グループにデプロイする場合、最初にリソース グループを作成する必要があります。 新しいリソース グループを作成するには、 `New-AzureRmResourceGroup` コマンドを使用し、リソース グループの名前とターゲット リージョンを指定します。

```powershell
New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
```

リソース グループを作成したら、次のコマンドでクラスターを作成できます。 目的のテンプレートの URI は `-TemplateUri` パラメーターに指定されます。 このコマンドを実行すると、PowerShell からデプロイのパラメーター値の入力が求められます。

```powershell
New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
```

### <a name="provide-template-parameters"></a>テンプレート パラメーターを指定する
PowerShell に慣れている場合は、マイナス記号 (-) を入力して Tab キーを押すことで、コマンドレットで利用可能なパラメーターを順番に表示できることをご存じのことと思われます。 この機能は、テンプレートで定義するパラメーターでも同様に使用できます。 テンプレート名を入力すると、コマンドレットがすぐにテンプレートをフェッチし、パラメーターを解析して、テンプレート パラメーターをコマンドに動的に追加します。 これにより、テンプレート パラメーターの値の指定が非常に簡単になります。 また、必須のパラメーター値を忘れた場合は、PowerShell から値を求められます。

パラメーターが含まれている完全なコマンドを以下に示します。 リソースの名前には独自の値を指定できます。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName RESOURCE_GROUP_NAME-TemplateURI TEMPLATE_URI -adminuser value1 -adminpassword value2 ....
```

## <a name="next-steps"></a>次のステップ
これでクラスターが機能します。接続と管理の詳細については、次のドキュメントを参照してください。

* [Azure コンテナー サービス クラスターに接続する](container-service-connect.md)
* [Azure コンテナー サービスと DC/OS の使用](container-service-mesos-marathon-rest.md)
* [Azure コンテナー サービスと Docker Swarm の使用](container-service-docker-swarm.md)
* [Azure Container Service と Kubernetes の使用](container-service-kubernetes-walkthrough.md)




<!--HONumber=Dec16_HO3-->


