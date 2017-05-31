---
title: "Azure Cosmos DB Automation - リージョンの管理 | Microsoft Docs"
description: "Azure CLI 1.0 と Azure Resource Manager を使用して、Azure Cosmos DB データベース アカウントのリージョンを管理します。"
services: cosmosdb
author: dmakwana
manager: jhubbard
editor: 
tags: azure-resource-manager
documentationcenter: 
ms.assetid: 7f765c17-8549-4108-9475-46394fc3a218
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: dimakwan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 961336e94dca6672dfd6751c4ba27c3997058377
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="automate-azure-cosmos-db-account-region-management-using-azure-cli-10-and-azure-resource-manager-templates"></a>Azure CLI 1.0 と Azure Resource Manager テンプレートを使用して Azure Cosmos DB アカウントのリージョン管理を自動化する

この記事では、Azure CLI 1.0 コマンドと Azure Resource Manager テンプレートを使用して、Azure Cosmos DB DocumentDB API アカウントのリージョンを追加または削除する方法について説明します。 リージョン管理は [Azure Portal](../cosmos-db/tutorial-global-distribution-documentdb.md) でも行うことができます。 以下のチュートリアルのコマンドでは、さまざまなリージョンのフェールオーバー優先度を変更することはできませんので注意してください。 追加または削除できるのは、読み取りリージョンのみです。 データベース アカウントの書き込みリージョン (フェールオーバー優先度が 0) は、追加も削除もできません。

現時点では、Azure Cosmos DB データベース アカウントは、[Azure Resource Manager テンプレートと Azure CLI 1.0](documentdb-automation-resource-manager-cli.md) を使用して作成および変更できる唯一の Azure Cosmos DB リソースです。 

CLI 2.0 を使用して、Azure Cosmos DB DocumentDB API、Table API、Graph API、または Mongo DB のアカウントを作成する方法については、[Azure CLI を使用した Azure DocumentDB アカウントの作成](documentdb-automation-resource-manager-cli.md)に関する記事をご覧ください。

## <a name="getting-ready"></a>準備

Azure リソース グループで Azure CLI 1.0 を使用するには、適切な Azure CLI 1.0 バージョンと Azure アカウントを用意する必要があります。 Azure CLI 1.0 をインストールしていない場合は、[インストールします](../cli-install-nodejs.md)。

### <a name="update-your-azure-cli-10-version"></a>Azure CLI 1.0 のバージョンを更新する

コマンド プロンプトで「`azure --version`」と入力し、バージョン 0.10.4 以降が既にインストールされているかどうかを確認します。 この手順で Microsoft Azure CLI 1.0 のデータ収集に参加するように求められる場合があります。y または n を選択して、オプトインまたはオプトアウトできます。

    azure --version
    0.10.4 (node: 4.2.4)

バージョンが 0.10.4 以降ではない場合、[Azure CLI 1.0 をインストール](../cli-install-nodejs.md)するか、いずれかのネイティブ インストーラーまたは **npm** で更新する必要があります。更新する場合は「`npm update -g azure-cli`」と入力し、インストールする場合は「`npm install -g azure-cli`」と入力します。

### <a name="set-your-azure-account-and-subscription"></a>Azure アカウントとサブスクリプションを設定する

Azure サブスクリプションをまだ持っていない場合でも、Visual Studio サブスクリプションがあれば、 [Visual Studio サブスクライバー向けの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)をアクティブ化できます。 または、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

Azure リソース管理テンプレートを使用するには、職場のアカウント、学校のアカウント、Microsoft アカウントのいずれかの ID を所有している必要があります。 いずれかのアカウントを所有している場合、次のコマンドを入力します。

    azure login

次の出力が生成されます。 

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [!NOTE]
> Azure アカウントを持っていない場合、別の種類のアカウントが必要であることを示すエラー メッセージが表示されます。 現在の Azure アカウントから作成する方法については、「 [Azure Active Directory で職場または学校の ID を作成する](../virtual-machines/windows/create-aad-work-id.md)」を参照してください。

ブラウザーで [https://aka.ms/devicelogin](https://aka.ms/devicelogin) を開き、コマンド出力で生成されたコードを入力します。

![Microsoft Azure CLI 1.0 のデバイス ログイン画面のスクリーンショット](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

コードを入力したら、ブラウザーで使用する ID を選択し、必要に応じてユーザー名とパスワードを入力します。

![使用する Azure サブスクリプションに関連付けられている Microsoft ID アカウントを選択する場所のスクリーンショット](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

ログインすると、次の確認画面が表示されます。表示されたら、ブラウザーのウィンドウを閉じることができます。

![Microsoft Azure クロスプラットフォーム コマンド ライン インターフェイスへのログインを確認するスクリーンショット](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

コマンド シェルでは、次の出力も提供します。

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

ここで説明する対話式のログイン方法以外に、Azure CLI 1.0 でログインする方法もあります。 他の方法と複数のサブスクリプションを処理する方法に関する詳細については、「[Azure コマンド ライン インターフェイス (Azure CLI 1.0) から Azure サブスクリプションに接続する](../xplat-cli-connect.md)」を参照してください。

### <a name="switch-to-azure-cli-10-resource-group-mode"></a>Azure CLI 1.0 リソース グループ モードに切り替える

既定では、Azure CLI 1.0 はサービス管理モード (**asm** モード) で起動します。 以下を入力してリソース グループ モードに切り替えます。

    azure config mode arm

次の出力が生成されます。

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

必要に応じて、「 `azure config mode asm`」と入力することで、既定のコマンド セットに戻すことができます。

### <a name="create-or-retrieve-your-resource-group"></a>リソース グループを作成または取得する

Azure Cosmos DB アカウントを作成するには、まずリソース グループが必要です。 使用するリソース グループの名前がわかっている場合、 [手順 2](#create-documentdb-account-cli)に移動してください。 

現在のすべてのリソース グループの一覧を確認するには、次のコマンドを実行し、使用するリソース グループの名前をメモします。 

    azure group list

新しいリソース グループを作成するには、作成する新しいリソース グループの名前とリソース グループを作成するリージョンを指定します。 

    azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` に使用できる文字は、英数字、ピリオド、アンダースコア、「-」、かっこのみです。末尾にピリオドを指定することはできません。 
 - `<resourcegrouplocation>` は、Azure Cosmos DB が一般公開されているリージョンのいずれかである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

入力例:

    azure group create new_res_group westus

次の出力が生成されます。

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。 

## <a name="understanding-azure-resource-manager-templates-and-resource-groups"></a>Azure Resource Manager テンプレートおよびリソース グループについて

ほとんどのアプリケーションは、さまざまなリソースの種類の組み合わせ (1 つ以上の Azure Cosmos DB アカウント、ストレージ アカウント、仮想ネットワーク、コンテンツ配信ネットワークなど) から構築されます。 既定の Azure サービス管理 API と Azure ポータルでは、サービス単位のアプローチを使用してこれらの項目を表していました。 この方法では、個々のサービスを 1 つの論理的なデプロイ単位としてではなく、個別にデプロイ、管理 (またはこのことを実行するその他のツールを検索) する必要があります。

*Azure リソース マネージャー テンプレート* では、これらの異なるリソースを 1 つの論理的なデプロイ単位として、宣言型の方法でデプロイし、管理することが可能になります。 何をデプロイするのかを Azure に 1 コマンドずつ命令するのではなく、JSON ファイル内にデプロイメント全体、つまりすべてのリソースと、関連する構成およびデプロイメント パラメーターを記述し、Azure にそれらのリソースを 1 つのグループとしてデプロイするよう指示します。

Azure リソース グループとその機能の詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。 テンプレートの作成に興味がある場合は、「 [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。


## <a id="add-region-documentdb-account"></a>作業: Azure Cosmos DB DocumentDB API アカウントにリージョンを追加する

Azure Cosmos DB は、さまざまな [Azure リージョン](https://azure.microsoft.com/regions/#services)に[データをグローバルに分散する][distribute-globally]機能を備えています。 このセクションの手順では、Azure CLI 1.0 と Resource Manager テンプレートを使用して、既存の Azure Cosmos DB アカウントに読み取りリージョンを追加する方法について説明します。 この作業は、Resource Manager テンプレートの有無にかかわらず、Azure CLI 1.0 を使用して行うことができます。

### <a id="add-region-documentdb-account-cli"></a>Azure CLI 1.0 を使用して Resource Manager テンプレートを使わずに Azure Cosmos DB アカウントにリージョンを追加する

コマンド プロンプトで下記のコマンドを入力して、新規または既存のリソース グループの既存の Azure Cosmos DB アカウントにリージョンを追加します。 "locations" 配列は、追加する新しいリージョンを除き、Azure Cosmos DB アカウントにおける現在のリージョン構成を反映している必要があります。 下記の例では、2 つ目のリージョンをアカウントに追加するコマンドを示します。

フェールオーバー優先度の値は既存の構成と一致させます。 いずれかのリージョンの failoverPriority 値が 0 である必要があります。これは、このリージョンが [Azure Cosmos DB アカウントの書き込みリージョン][scaling-globally]として保持されることを示します。 フェールオーバー優先度の値は、各場所間で一意である必要があり、フェールオーバー優先度の最大値は、リージョンの合計数よりも小さくする必要があります。 新しいリージョンは "読み取り" リージョンとなり、フェールオーバー優先度の値は 0 より大きくする必要があります。

> [!TIP]
> Azure PowerShell または Windows PowerShell でこのコマンドを実行する場合、予想外のコマンドに関するエラーを受け取ります。 代わりに、Windows PowerShell コマンド プロンプトでこのコマンドを実行します。

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority1>\"},{\"locationName\":\"<newdatabaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority2>\"}"]}"

 - `<resourcegroupname>` に使用できる文字は、英数字、ピリオド、アンダースコア、「-」、かっこのみです。末尾にピリオドを指定することはできません。
 - `<resourcegrouplocation>` は現在のリソース グループのリージョンです。
 - `<ip-range-filter>` 特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式で指定する必要があります。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません。 詳細については、「[Azure Cosmos DB のファイアウォール サポート](documentdb-firewall-support.md)」をご覧ください。
 - `<databaseaccountname>` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。
 - `<databaseaccountlocation>` は、Azure Cosmos DB が一般公開されているリージョンのいずれかである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。
 - `<newdatabaseaccountlocation>` は追加される新しいリージョンであり、Azure Cosmos DB が一般公開されているリージョンのいずれかである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。


Azure Cosmos DB アカウントの読み取りリージョンとして "米国東部" リージョンを追加するときの入力例を次に示します。 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"},{\"locationName\":\"eastus\",\"failoverPriority\":\"1\"}"]}"

新しいアカウントがプロビジョニングされるとき、次の出力が生成されます。

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。 

コマンドが終了すると、アカウントは数分間 "**作成中**" の状態になります。その後、"**オンライン**" の状態に変化し、使用する準備ができます。 アカウントの状態は、[Azure Portal](https://portal.azure.com) の **[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)]** ブレードで確認できます。

### <a id="add-region-documentdb-account-cli-arm"></a>Azure CLI 1.0 と Resource Manager テンプレートを使用して Azure Cosmos DB アカウントにリージョンを追加する

このセクションの手順では、Azure Resource Manager テンプレートとオプションのパラメーター ファイル (いずれも JSON ファイル) を使用して、既存の Azure Cosmos DB アカウントにリージョンを追加する方法について説明します。 テンプレートを利用すると、誤りなく必要なものを正確に表現し、それを繰り返すことができます。

フェールオーバー優先度の値は既存の構成と一致させます。 いずれかのリージョンの failoverPriority 値が 0 である必要があります。これは、このリージョンが [Azure Cosmos DB アカウントの書き込みリージョン][scaling-globally]として保持されることを示します。 フェールオーバー優先度の値は、各場所間で一意である必要があり、フェールオーバー優先度の最大値は、リージョンの合計数よりも小さくする必要があります。 新しいリージョンは "読み取り" リージョンとなり、フェールオーバー優先度の値は 0 より大きくする必要があります。

現在の Azure Cosmos DB のリージョン構成と一致する、次のようなローカルのテンプレート ファイルを作成します。 "locations" 配列には、追加する新しいリージョンと共に、データベース アカウント内のすべての既存のリージョンが含まれている必要があります。 azuredeploy.json ファイルに名前を付けます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            },
            "locationName2": {
                "type": "string"
            },
            "newLocationName": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        },
                        {
                            "failoverPriority": 1,
                            "locationName": "[parameters('locationName2')]"
                        },
                        {
                            "failoverPriority": 2,
                            "locationName": "[parameters('newLocationName')]"
                        }
                    ]
                }
            }
        ]
    }

上記のテンプレート ファイルは、既に 2 つのリージョンがある Azure Cosmos DB アカウントに新しいリージョンを追加する例を示しています。

コマンド ラインでパラメーターの値を入力するか、パラメーター ファイルを作成して値を指定できます。

パラメーター ファイルを作成するには、新しいファイルに次の内容をコピーし、azuredeploy.parameters.json ファイルに名前を付けます。 コマンド プロンプトでデータベース アカウント名を指定する場合、このファイルを作成せずに続行できます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            },
            "locationName2": {
                "value": "eastus"
            },
            "newLocationName": {
                "value": "northeurope"
            }
        }
    }

azuredeploy.parameters.json ファイルの `"databaseAccountName"` の値フィールドを、使用するデータベースの名前に更新し、ファイルを保存します。 `"databaseAccountName"` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。 `"locationName1"` と `"locationName2"` の value フィールドを、Azure Cosmos DB アカウントがあるリージョンに更新します。 `"newLocationName"` の値フィールドを、追加するリージョンに更新します。

リソース グループに Azure Cosmos DB アカウントを作成するには、次のコマンドを実行し、テンプレート ファイルのパス、パラメーター ファイルのパスまたはパラメーター値、デプロイ先のリソース グループの名前、デプロイ名を指定します (-n は省略可能です)。 

パラメーター ファイルを使用するには:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` は、手順 1 で作成した azuredeploy.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
 - `<PathToParameterFile>` は、手順 1 で作成した azuredeploy.parameters.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
 - `<resourcegroupname>` は、Azure Cosmos DB データベース アカウントを追加する既存のリソース グループの名前です。 
 - `<deploymentname>` は、デプロイメント名 (省略可能) です。

入力例: 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

あるいは、パラメーター ファイルなしでデータベース アカウント名パラメーターを指定し、代わりに値の入力を求めさせるには、次のコマンドを実行します。

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

入力例 ("samplearmacct" というデータベース アカウントのプロンプトとエントリが表示されます):

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

アカウントがプロビジョニングされると、次の情報を受け取ります。 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.Azure Cosmos DB/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    locationName1        String  westus
    data:    locationName2        String  eastus
    data:    newLocationName      String  eastus
    data:    databaseAccountName  String  samplearmacct
    info:    group deployment create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。  

コマンドが終了すると、アカウントは数分間 "**作成中**" の状態になります。その後、"**オンライン**" の状態に変化し、使用する準備ができます。 アカウントの状態は、[Azure Portal](https://portal.azure.com) の **[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)]** ブレードで確認できます。

## <a id="remove-region-documentdb-account"></a>作業: Azure Cosmos DB アカウントからリージョンを削除する

Azure Cosmos DB は、さまざまな [Azure リージョン](https://azure.microsoft.com/regions/#services)に[データをグローバルに分散する][distribute-globally]機能を備えています。 このセクションの手順では、Azure CLI 1.0 と Resource Manager テンプレートを使用して、既存の Azure Cosmos DB アカウントからリージョンを削除する方法について説明します。 この作業は、Resource Manager テンプレートの有無にかかわらず、Azure CLI 1.0 を使用して行うことができます。

### <a id="remove-region-documentdb-account-cli"></a>Azure CLI 1.0 を使用して Resource Manager テンプレートを使わずに Azure Cosmos DB アカウントからリージョンを削除する

既存の Azure Cosmos DB アカウントからリージョンを削除するには、Azure CLI 1.0 で下記のコマンドを実行します。 "locations" 配列には、そのリージョンを削除した後も残されるリージョンだけが含まれている必要があります。 **省略された場所は、Azure Cosmos DB アカウントから削除されます**。 コマンド プロンプトで下記のコマンドを入力します。

いずれかのリージョンの failoverPriority 値が 0 である必要があります。これは、このリージョンが [Azure Cosmos DB アカウントの書き込みリージョン][scaling-globally]として保持されることを示します。 フェールオーバー優先度の値は、各場所間で一意である必要があり、フェールオーバー優先度の最大値は、リージョンの合計数よりも小さくする必要があります。 

> [!TIP]
> Azure PowerShell または Windows PowerShell でこのコマンドを実行する場合、予想外のコマンドに関するエラーを受け取ります。 代わりに、Windows PowerShell コマンド プロンプトでこのコマンドを実行します。

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l <resourcegrouplocation> -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"<ip-range-filter>\",\"locations\":["{\"locationName\":\"<databaseaccountlocation>\",\"failoverPriority\":\"<failoverPriority>\"}"]}"

 - `<resourcegroupname>` に使用できる文字は、英数字、ピリオド、アンダースコア、「-」、かっこのみです。末尾にピリオドを指定することはできません。
 - `<resourcegrouplocation>` は現在のリソース グループのリージョンです。
 - `<ip-range-filter>` 特定のデータベース アカウントのクライアント IP の許可リストとして追加する一連の IP アドレスまたは IP アドレス範囲を CIDR 形式で指定する必要があります。 IP アドレス/範囲は、コンマで区切る必要があり、スペースを含めることはできません。 詳細については、「[Azure Cosmos DB のファイアウォール サポート](documentdb-firewall-support.md)」をご覧ください。
 - `<databaseaccountname>` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。
 - `<databaseaccountlocation>` は、Azure Cosmos DB が一般公開されているリージョンのいずれかである必要があります。 最新のリージョン一覧については、 [Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

入力例: 

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08 -l westus -p "{\"databaseAccountOfferType\":\"Standard\",\"ipRangeFilter\":\"\",\"locations\":["{\"locationName\":\"westus\",\"failoverPriority\":\"0\"}"]}"

新しいアカウントがプロビジョニングされるとき、次の出力が生成されます。

    info:    Executing command resource create
    + Getting resource samplecliacct
    + Creating resource samplecliacct
    info:    Resource samplecliacct is updated
    data:
    data:    Id:        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group/providers/Microsoft.DocumentDB/databaseAccounts/samplecliacct
    data:    Name:      samplecliacct
    data:    Type:      Microsoft.DocumentDB/databaseAccounts
    data:    Parent:
    data:    Location:  West US
    data:    Tags:
    data:
    info:    resource create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。 

コマンドが終了すると、アカウントは数分間 "**更新中**" の状態になります。その後、"**オンライン**" の状態に変化し、使用できるようになります。 アカウントの状態は、[Azure Portal](https://portal.azure.com) の **[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)]** ブレードで確認できます。

### <a id="remove-region-documentdb-account-cli-arm"></a>Azure CLI 1.0 と Resource Manager テンプレートを使用して Azure Cosmos DB アカウントからリージョンを削除する

このセクションの手順では、Azure Resource Manager テンプレートとオプションのパラメーター ファイル (いずれも JSON ファイル) を使用して、既存の Azure Cosmos DB アカウントからリージョンを削除する方法について説明します。 テンプレートを利用すると、誤りなく必要なものを正確に表現し、それを繰り返すことができます。

いずれかのリージョンの failoverPriority 値が 0 である必要があります。これは、このリージョンが [Azure Cosmos DB アカウントの書き込みリージョン][scaling-globally]として保持されることを示します。 フェールオーバー優先度の値は、各場所間で一意である必要があり、フェールオーバー優先度の最大値は、リージョンの合計数よりも小さくする必要があります。 

現在の Azure Cosmos DB のリージョン構成と一致する、次のようなローカルのテンプレート ファイルを作成します。 "locations" 配列には、そのリージョンを削除した後も残されるリージョンだけが含まれている必要があります。 **省略された場所は、Azure Cosmos DB アカウントから削除されます**。 azuredeploy.json ファイルに名前を付けます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            },
            "locationName1": {
                "type": "string"
            }
        },
        "variables": {},
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "databaseAccountOfferType": "Standard",
                    "ipRangeFilter": "",
                    "locations": [
                        {
                            "failoverPriority": 0,
                            "locationName": "[parameters('locationName1')]"
                        }
                    ]
                }
            }
        ]
    }

コマンド ラインでパラメーターの値を入力するか、パラメーター ファイルを作成して値を指定できます。

パラメーター ファイルを作成するには、新しいファイルに次の内容をコピーし、azuredeploy.parameters.json ファイルに名前を付けます。 コマンド プロンプトでデータベース アカウント名を指定する場合、このファイルを作成せずに続行できます。 Azure Resource Manager テンプレートで定義されている必要なパラメーターを必ず追加してください。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            },
            "locationName1": {
                "value": "westus"
            }
        }
    }

azuredeploy.parameters.json ファイルの `"databaseAccountName"` の値フィールドを、使用するデータベースの名前に更新し、ファイルを保存します。 `"databaseAccountName"` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。 `"locationName1"` の value フィールドを、リージョンの削除後に Azure Cosmos DB アカウントが存在することになるリージョンに更新します。

リソース グループに Azure Cosmos DB アカウントを作成するには、次のコマンドを実行し、テンプレート ファイルのパス、パラメーター ファイルのパスまたはパラメーター値、デプロイ先のリソース グループの名前、デプロイ名を指定します (-n は省略可能です)。 

パラメーター ファイルを使用するには:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` は、手順 1 で作成した azuredeploy.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
 - `<PathToParameterFile>` は、手順 1 で作成した azuredeploy.parameters.json ファイルのパスです。 パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
 - `<resourcegroupname>` は、Azure Cosmos DB データベース アカウントを追加する既存のリソース グループの名前です。 
 - `<deploymentname>` は、デプロイメント名 (省略可能) です。

入力例: 

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

あるいは、パラメーター ファイルなしでデータベース アカウント名パラメーターを指定し、代わりに値の入力を求めさせるには、次のコマンドを実行します。

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

入力例 ("samplearmacct" というデータベース アカウントのプロンプトとエントリが表示されます):

    azure group deployment create -f azuredeploy.json -g new_res_group -n azuredeploy
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    databaseAccountName: samplearmacct

アカウントがプロビジョニングされると、次の情報を受け取ります。 

    info:    Executing command group deployment create
    + Initializing template configurations and parameters
    + Creating a deployment
    info:    Created template deployment "azuredeploy"
    + Waiting for deployment to complete
    + 
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Running
    + 
    info:    Resource 'new_res_group' of type 'Microsoft.DocumentDb/databaseAccounts' provisioning status is Succeeded
    data:    DeploymentName     : azuredeploy
    data:    ResourceGroupName  : new_res_group
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-11-30T18:50:23.6300288Z
    data:    Mode               : Incremental
    data:    CorrelationId      : 4a5d4049-c494-4053-bad4-cc804d454700
    data:    DeploymentParameters :
    data:    Name                 Type    Value
    data:    -------------------  ------  ------------------
    data:    databaseAccountName  String  samplearmacct
    data:    locationName1        String  westus
    info:    group deployment create command OK

エラーが発生した場合、「 [トラブルシューティング](#troubleshooting)」を参照してください。  

コマンドが終了すると、アカウントは数分間 "**更新中**" の状態になります。その後、"**オンライン**" の状態に変化し、使用できるようになります。 アカウントの状態は、[Azure Portal](https://portal.azure.com) の **[Azure Cosmos DB Accounts (Azure Cosmos DB アカウント)]** ブレードで確認できます。

## <a name="troubleshooting"></a>トラブルシューティング

リソース グループまたはデータベース アカウントの作成中に `Deployment provisioning state was not successful` のようなエラーが発生する場合、いくつかのトラブルシューティング方法があります。 

> [!NOTE]
> データベース アカウント名に無効な文字を指定したり、Azure Cosmos DB を使用できない場所を指定したりすると、デプロイ エラーが発生します。 データベース アカウント名に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。 有効なデータベース アカウントの場所一覧については、[Azure リージョンのページ](https://azure.microsoft.com/regions/#services)を参照してください。

- 出力に次の `Error information has been recorded to C:\Users\wendy\.azure\azure.err`が含まれている場合は、azure.err ファイルでエラー情報を確認してください。

- このログ ファイルには、リソース グループの役立つ情報が記録されている場合があります。 ログ ファイルを表示するには、次のコマンドを実行します。

        azure group log show <resourcegroupname> --last-deployment

    入力例:       azure group log show new_res_group --last-deployment

    追加情報については、「 [Azure のリソース グループ デプロイのトラブルシューティング](../azure-resource-manager/resource-manager-common-deployment-errors.md) 」を参照してください。

- 次のスクリーンショットのように、エラー情報は Azure ポータルでも確認できます。 エラー情報に移動するには: ジャンプバーで [リソース グループ] をクリックし、エラーが発生したリソース グループを選択します。次に、[リソース グループ] ブレードの [Essentials] 領域で [前回のデプロイ] の日付をクリックし、[デプロイ履歴] ブレードで失敗したデプロイを選択し、[デプロイ] ブレードで赤い感嘆符が付いた [操作の詳細] 詳細をクリックします。 失敗したデプロイメントの状態メッセージが [操作の詳細] ブレードに表示されます。 

    ![デプロイメント エラー メッセージに移動する方法を示す Azure ポータルのスクリーンショット](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png) 

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB アカウントが用意できたら、次に Azure Cosmos DB データベースを作成します。 データベースを作成するには、次のいずれかを使用します。

- Azure Portal。[Azure Portal を使用した Azure Cosmos DB コレクションとデータベースの作成](documentdb-create-collection.md)に関する記事をご覧ください。
- GitHub の [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) リポジトリの [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) プロジェクトにある C# .NET のサンプル。
- [Azure Cosmos DB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 Azure Cosmos DB DocumentDB API には、.NET、Java、Python、Node.js、JavaScript API の各 SDK があります。 

データベースを作成した後に、データベースに [1 つまたは複数のコレクションを追加](documentdb-create-collection.md)し、それらのコレクションに[ドキュメントを追加する](documentdb-view-json-document-explorer.md)必要があります。 

コレクションにドキュメントを追加したら、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用することで、[SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#ExecutingSqlQueries)できます。

Azure Cosmos DB の詳細については、次のリソースを参照してください。

-    [Azure Cosmos DB の概要](../cosmos-db/introduction.md)

使用できる他のテンプレートについては、「 [Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)」を参照してください。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[distribute-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally
[scaling-globally]: https://azure.microsoft.com/documentation/articles/documentdb-distribute-data-globally/#scaling-across-the-planet

