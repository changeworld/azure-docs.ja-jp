---
title: "Azure での Docker コンテナー クラスターのデプロイ | Microsoft Docs"
description: "Azure Portal または Azure Resource Manager テンプレートを使用して、Azure Container Service クラスターをデプロイします。"
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
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 01fe5302e1c596017755c4669103bac910e3452c
ms.openlocfilehash: 470bf39bf0e61325f36a2f45316f57545c69e3de


---
# <a name="deploy-an-azure-container-service-cluster"></a>Azure コンテナー サービス クラスターのデプロイ



Azure コンテナー サービスでは、人気のオープン ソースのコンテナー クラスタリングやオーケストレーション ソリューションを短期間でデプロイできます。 このドキュメントでは、Azure Portal または Azure Resource Manager クイックスタート テンプレートを使用して Azure Container Service クラスターをデプロイする手順について説明します。 

> [!NOTE]
> Azure Container Service での Kubernetes のサポートは、現在はプレビューの段階です。

[Azure CLI 2.0 (プレビュー)](container-service-create-acs-cluster-cli.md) または Azure Container Service API を使用して、Azure Container Service クラスターをデプロイすることもできます。



## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**: お持ちでない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)にサインアップしてください。

* **SSH 公開キー**: ポータルまたはいずれかの Azure クイックスタート テンプレートを通じてデプロイする場合は、Azure Container Service 仮想マシンに対する認証のための公開キーを指定する必要があります。 Secure Shell (SSH) キーを作成するには、[OS X と Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) または [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md) のガイダンスを参照してください。 

* **サービス プリンシパル クライアント ID とシークレット** (Kubernetes のみ): サービス プリンシパルの作成の詳細とガイダンスについては、[Kubernetes クラスターのサービス プリンシパル](container-service-kubernetes-service-principal.md)に関するページを参照してください。



## <a name="create-a-cluster-by-using-the-azure-portal"></a>Azure Portal を使用してクラスターを作成する
1. Azure Portal にサインインし、**[新規]** を選択して、Azure Marketplace で **Azure Container Service** を検索します。

    ![Marketplace の Azure Container Service](media/container-service-deployment/acs-portal1.png)  <br />

2. **[Azure Container Service]** を選択し、**[作成]** をクリックします。

    ![コンテナー サービスを作成する](media/container-service-deployment/acs-portal2.png)  <br />

3. 次の情報を入力します。

    * **[ユーザー名]**: Azure Container Service クラスターのそれぞれの仮想マシンと仮想マシン スケール セットのアカウントに使用されるユーザー名です。
    * **[サブスクリプション]**: Azure サブスクリプションを選択します。
    * **[リソース グループ]**: 既存のリソース グループを選択するか、新しいリソース グループを作成します。
    * **[場所]**: Azure コンテナー サービスのデプロイの Azure リージョンを選択します。
    * **[SSH 公開キー]**: Azure Container Service の仮想マシンに対する認証に使用される公開キーを追加します。 このキーに改行を含めないことと、`ssh-rsa` プレフィックスを付けることが重要です。 `username@domain` ポストフィックスは省略可能です。 キーは、**ssh-rsa AAAAB3Nz...<...>...UcyupgH azureuser@linuxvm** のようになります。 

4. 準備が完了したら、 **[OK]** をクリックします。

    ![[基本設定]](media/container-service-deployment/acs-portal3.png)  <br />

5. オーケストレーションの種類を選択します。 オプションは次のとおりです。

  * **[DC/OS]**: DC/OS クラスターをデプロイします。
  * **[Swarm]**: Docker Swarm クラスターをデプロイします。
  * **[Kubernetes]**: Kubernetes クラスターをデプロイします。


6. 準備が完了したら、 **[OK]** をクリックします。

    ![オーケストレーターを選択する](media/container-service-deployment/acs-portal4-new.png)  <br />

7. ドロップダウン ボックスの一覧で **[Kubernetes]** を選択した場合は、サービス プリンシパルのクライアント ID とクライアント シークレットを入力する必要があります。 詳細については、[Kubernetes クラスターのサービス プリンシパル](container-service-kubernetes-service-principal.md)に関するページを参照してください。

    ![Kubernetes のサービス プリンシパルを入力する](media/container-service-deployment/acs-portal10.png)  <br />

7. **Azure Container Service** 設定ブレードで、次の情報を入力します。

    * **[Master count (マスター数)]**: クラスターのマスター数。 Kubernetes を選択すると、マスターの数は既定の 1 に設定されます。
    * **[Agent count (エージェント数)]**: Docker Swarm と Kubernetes の場合、この値はエージェント スケール セットのエージェントの初期数になります。 DC/OS の場合は、プライベート スケール セットのエージェントの初期数です。 また、事前に決められた数のエージェントを含むパブリック スケール セットが、DC/OS 用に作成されます。 このパブリック スケール セットのエージェント数は、クラスターに作成されたマスター数によって決まります (1 マスターに&1; パブリック エージェント、3 または&5; マスターに&2; パブリック エージェント)。
    * **[Agent virtual machine size (エージェント仮想マシン サイズ)]**: エージェント仮想マシンのサイズ。
    * **[DNS プレフィックス]**: サービスの完全修飾ドメイン名の主要部分の先頭に付ける、世界で一意の名前。
    * **[VM diagnostics (VM 診断)]**: 一部のオーケストレーターでは、VM 診断を有効にすることができます。

8. 準備が完了したら、 **[OK]** をクリックします。

    ![Container Service の設定](media/container-service-deployment/acs-portal5.png)  <br />

9. サービスの検証が完了したら、 **[OK]** をクリックします。

    ![検証](media/container-service-deployment/acs-portal6.png)  <br />

10. 使用条件を確認します。 デプロイ プロセスを開始するには、**[購入]** をクリックします。

    ![Purchase](media/container-service-deployment/acs-portal7.png)  <br />

    デプロイを Azure ポータルに固定した場合、デプロイの状態を確認できます。

    ![[デプロイ ステータス]](media/container-service-deployment/acs-portal8.png)  <br />

デプロイが完了するまで、数分間かかります。 その後、Azure Container Service クラスターを利用できるようになります。



## <a name="create-a-cluster-by-using-a-quickstart-template"></a>クイックスタート テンプレートを使用してクラスターを作成する
Azure クイックスタート テンプレートは、Azure Container Service でクラスターをデプロイするために使用できます。 提供されたクイック スタート テンプレートを変更して、Azure の追加または高度な構成を含めることができます。 Azure クイックスタート テンプレートを使用して Azure Container Service クラスターを作成するには、Azure サブスクリプションが必要です。 お持ちでない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AA4C1C935)にサインアップしてください。 

テンプレートと Azure CLI 2.0 (プレビュー) を使用してクラスターをデプロイするには、以下の手順に従ってください ([インストールとセットアップの手順](/cli/azure/install-az-cli2.md)を参照してください)。

> [!NOTE] 
> Windows システムの場合は、同様の手順で、Azure PowerShell を使用してテンプレートをデプロイすることができます。 このセクションの後の方の手順を参照してください。 [ポータル](../azure-resource-manager/resource-group-template-deploy-portal.md)またはその他の方法でテンプレートをデプロイすることもできます。

1. DC/OS、Docker Swarm、または Kubernetes のクラスターをデプロイするには、GitHub から次のテンプレートのいずれかを選択します。 既定のオーケストレーターの選択を除き、DC/OS と Swarm のテンプレートは同じです。

    * [DC/OS テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Azure アカウントにログイン (`az login`) し、Azure CLI が Azure サブスクリプションに接続されていることを確認します。 次のコマンドを使用して、既定のサブスクリプションを確認できます。

    ```azurecli
    az account show
    ```
    
    複数のサブスクリプションがあり、別の既定のサブスクリプションを設定する必要がある場合は、`az account set --subscription` を実行し、サブスクリプション ID または名前を指定します。

3. ベスト プラクティスとしては、デプロイ用の新しいリソース グループを使用することをお勧めします。 リソース グループを作成するには、次のように `az group create` コマンドを使用し、リソース グループの名前と場所を指定します。 

    ```azurecli
    az group create --name "RESOURCE_GROUP" --location "LOCATION"
    ```

4. 必要なテンプレート パラメーターが含まれている JSON ファイルを作成します。 `azuredeploy.parameters.json` という名前のパラメーター ファイルをダウンロードします。このファイルは、GitHub の Azure Container Service テンプレート `azuredeploy.json` に付属しています。 クラスターの必須パラメーター値を入力します。 

    たとえば、[DC/OS テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)を使用するには、`dnsNamePrefix` と `sshRSAPublicKey` のパラメーター値を指定します。 他のパラメーターについては、`azuredeploy.json` とオプションの説明を参照してください。  
 

5. 次のコマンドでデプロイ パラメーター ファイルを渡して、Container Service クラスターを作成します。このコマンドで、以下のものを指定します。

    * **RESOURCE_GROUP** は、前の手順で作成したリソース グループの名前です。
    * **DEPLOYMENT_NAME** (省略可能) は、デプロイに付ける名前です。
    * **TEMPLATE_URI** は、デプロイ ファイル `azuredeploy.json` の場所です。 この URI は、GitHub UI へのポインターではなく、Raw ファイルである必要があります。 この URI を調べるには、GitHub で `azuredeploy.json` ファイルを選択し、**[Raw]** ボタンをクリックします。  

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters @azuredeploy.parameters.json
    ```

    コマンド ラインで JSON 形式の文字列としてパラメーターを指定することもできます。 次のようなコマンドを使用します。

    ```azurecli
    az group deployment create -g RESOURCE_GROUP -n DEPLOYMENT_NAME --template-uri TEMPLATE_URI --parameters "{ \"param1\": {\"value1\"} … }"
    ```

    > [!NOTE]
    > デプロイが完了するまで、数分間かかります。
    > 

### <a name="equivalent-powershell-commands"></a>同等の PowerShell コマンド
PowerShell を使用して Azure Container Service クラスター テンプレートをデプロイすることもできます。 このドキュメントはバージョン 1.0 の [Azure PowerShell モジュール](https://azure.microsoft.com/blog/azps-1-0/)に基づいています。

1. DC/OS、Docker Swarm、または Kubernetes のクラスターをデプロイするには、次のテンプレートのいずれかを選択します。 既定のオーケストレーターの選択を除き、DC/OS と Swarm のテンプレートは同じです。

    * [DC/OS テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos)
    * [Swarm テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm)
    * [Kubernetes テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)

2. Azure サブスクリプションでクラスターを作成する前に、PowerShell セッションが Azure にサインインしていることを確認します。 そのためには、 `Get-AzureRMSubscription` コマンドを使用します。

    ```powershell
    Get-AzureRmSubscription
    ```

3. Azure にサインインする必要がある場合、 `Login-AzureRMAccount` コマンドを使用します。

    ```powershell
    Login-AzureRmAccount
    ```

4. ベスト プラクティスとしては、デプロイ用の新しいリソース グループを使用することをお勧めします。 リソース グループを作成するには、`New-AzureRmResourceGroup` コマンドを使用し、リソース グループの名前とターゲット リージョンを指定します。

    ```powershell
    New-AzureRmResourceGroup -Name GROUP_NAME -Location REGION
    ```

5. リソース グループを作成したら、次のコマンドでクラスターを作成できます。 目的のテンプレートの URI は `-TemplateUri` パラメーターに指定されます。 このコマンドを実行すると、PowerShell からデプロイのパラメーター値の入力が求められます。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DEPLOYMENT_NAME -ResourceGroupName RESOURCE_GROUP_NAME -TemplateUri TEMPLATE_URI
    ```

#### <a name="provide-template-parameters"></a>テンプレート パラメーターを指定する
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



<!--HONumber=Feb17_HO1-->


