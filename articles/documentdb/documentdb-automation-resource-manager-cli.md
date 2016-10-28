<properties
	pageTitle="DocumentDB Automation - リソース マネージャー - CLI | Microsoft Azure"
	description="Azure リソース マネージャーのテンプレートまたは CLI を使用し、DocumentDB データベース アカウントをデプロイします。DocumentDB は、JSON データ用のクラウドベースの NoSQL データベースです。"
	services="documentdb"
	authors="mimig1"
	manager="jhubbard"
	editor=""
    tags="azure-resource-manager"
	documentationCenter=""/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/03/2016" 
	ms.author="mimig"/>

# Azure リソース マネージャーのテンプレートと Azure CLI を利用し、DocumentDB アカウントを自動作成する

> [AZURE.SELECTOR]
- [Azure ポータル](documentdb-create-account.md)
- [Azure CLI と ARM](documentdb-automation-resource-manager-cli.md)

この記事では、Azure Resource Manager のテンプレートと Azure CLI (コマンド ライン インターフェイス) を利用し、Azure DocumentDB データベース アカウントを自動作成する方法について説明します。Azure ポータルを使用して DocumentDB アカウントを作成する方法については、「[DocumentDB データベース アカウントの作成](documentdb-create-account.md)」を参照してください。

- [CLI を利用して DocumentDB アカウントを作成する](#quick-create-documentdb-account)
- [ARM テンプレートを利用して DocumentDB アカウントを作成する](#deploy-documentdb-from-a-template)

現在のところ、DocumentDB データベース アカウントは、ARM テンプレートと Azure CLI で作成できる唯一の DocumentDB リソースです。

## 準備

Azure リソース グループで Azure CLI を使用するには、適切な Azure CLI のバージョンと Azure アカウントを用意する必要があります。Azure CLI をインストールしていない場合は、[インストールします](../xplat-cli-install.md)。

### Azure CLI のバージョンを更新する

コマンド プロンプトで、「`azure --version`」と入力し、バージョン 0.9.11 以降が既にインストールされているかどうかを確認します。この手順で Microsoft Azure CLI のデータ収集に参加するように求められる場合があります。y または n を選択して、オプトインまたはオプトアウトできます。

	azure --version
    0.9.11 (node: 0.12.7)

バージョンが 0.9.11 以降ではない場合、[Azure CLI をインストール](../xplat-cli-install.md)するか、いずれかのネイティブ インストーラーまたは **npm** で更新する必要があります。更新する場合、「`npm update -g azure-cli`」と入力し、インストールする場合、「`npm install -g azure-cli`」と入力します。

### Azure アカウントとサブスクリプションを設定する

Azure サブスクリプションをまだ持っていない場合でも、Visual Studio サブスクリプションがあれば、[Visual Studio サブスクライバー向けの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)をアクティブ化できます。または、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

Azure リソース管理テンプレートを使用するには、職場のアカウント、学校のアカウント、Microsoft アカウントのいずれかの ID を所有している必要があります。いずれかのアカウントを所有している場合、次のコマンドを入力します。

	azure login

次の出力が生成されます。

    info:    Executing command login
    |info:    To sign in, use a web browser to open the page https://aka.ms/devicelogin. 
    Enter the code E1A2B3C4D to authenticate.

> [AZURE.NOTE] Azure アカウントを持っていない場合、別の種類のアカウントが必要であることを示すエラー メッセージが表示されます。現在の Azure アカウントから作成する方法については、「[Azure Active Directory で職場または学校の ID を作成する](../virtual-machines/virtual-machines-windows-create-aad-work-id.md)」を参照してください。

ブラウザーで [https://aka.ms/devicelogin](https://aka.ms/devicelogin) を開き、コマンド出力で生成されたコードを入力します。

![Microsoft Azure CLI のデバイス ログイン画面のスクリーンショット](media/documentdb-automation-resource-manager-cli/azure-cli-login-code.png)

コードを入力したら、ブラウザーで使用する ID を選択し、必要に応じてユーザー名とパスワードを入力します。

![使用する Azure サブスクリプションに関連付けられている Microsoft ID アカウントを選択する場所のスクリーンショット](media/documentdb-automation-resource-manager-cli/identity-cli-login.png)

ログインすると、次の確認画面が表示されます。表示されたら、ブラウザーのウィンドウを閉じることができます。

![Microsoft Azure クロスプラットフォーム コマンド ライン インターフェイスへのログインを確認するスクリーンショット](media/documentdb-automation-resource-manager-cli/login-confirmation.png)

コマンド シェルでは、次の出力も提供します。

    /info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

ここで説明する対話式のログイン方法以外に、Azure CLi でログインする方法もあります。他の方法と複数のサブスクリプションを処理する方法に関する詳細については、「[Azure コマンド ライン インターフェイス (Azure CLI) から Azure サブスクリプションに接続する](../xplat-cli-connect.md)」を参照してください。

### Azure CLI リソース グループ モードに切り替える

既定では、Azure CLI はサービス管理モード (**asm** モード) で起動します。以下を入力してリソース グループ モードに切り替えます。

	azure config mode arm

次の出力が生成されます。

    info:    Executing command config mode
    info:    New mode is arm
    info:    config mode command OK

必要に応じて、「`azure config mode asm`」と入力することで、既定のコマンド セットに戻すことができます。

## <a id="quick-create-documentdb-account"></a>作業: Azure CLI を利用して DocumentDB アカウントを作成する

このセクションの指示に従い、Azure CLI で DocumentDB アカウントを作成します。

### 手順 1: リソース グループを作成または取得する

DocumentDB アカウントを作成するには、最初にリソース グループを用意する必要があります。使用するリソース グループの名前がわかっている場合、[手順 2](#create-documentdb-account-cli) に移動してください。

現在のすべてのリソース グループの一覧を確認するには、次のコマンドを実行し、使用するリソース グループの名前をメモします。

    azure group list

新しいリソース グループを作成するには、作成する新しいリソース グループの名前とリソース グループを作成するリージョンを指定します。

	azure group create <resourcegroupname> <resourcegrouplocation>

 - `<resourcegroupname>` に使用できる文字は、英数字、ピリオド、アンダースコア、「-」、かっこのみです。末尾にピリオドを指定することはできません。
 - `<resourcegrouplocation>` には、DocumentDB が一般公開されているリージョンのいずれかを指定します。最新のリージョン一覧については、[Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

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

エラーが発生した場合、「[トラブルシューティング](#troubleshooting)」を参照してください。

### <a id="create-documentdb-account-cli"></a> 手順 2: CLI を利用して DocumentDB アカウントを作成する

コマンド プロンプトで次のコマンドを入力し、新規または既存のリソース グループで DocumentDB アカウントを作成します。

> [AZURE.TIP] Azure PowerShell または Windows PowerShell でこのコマンドを実行する場合、予想外のコマンドに関するエラーを受け取ります。代わりに、Windows PowerShell コマンド プロンプトでこのコマンドを実行します。

    azure resource create -g <resourcegroupname> -n <databaseaccountname> -r "Microsoft.DocumentDB/databaseAccounts" -o "2015-04-08" -l <databaseaccountlocation> -p "{"databaseAccountOfferType":"Standard"}" 

 - `<resourcegroupname>` に使用できる文字は、英数字、ピリオド、アンダースコア、「-」、かっこのみです。末尾にピリオドを指定することはできません。
 - `<databaseaccountname>` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。
 - `<databaseaccountlocation>` には、DocumentDB が一般公開されているリージョンのいずれかを指定します。最新のリージョン一覧については、[Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

入力例:

    azure resource create -g new_res_group -n samplecliacct -r "Microsoft.DocumentDB/databaseAccounts" -o 2015-04-08  -l westus -p "{"databaseAccountOfferType":"Standard"}"

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

エラーが発生した場合、「[トラブルシューティング](#troubleshooting)」を参照してください。

コマンドが終了すると、アカウントは数分間「**作成中**」の状態になります。その後、「**オンライン**」の状態に変化し、使用する準備ができます。[Azure ポータル](https://portal.azure.com)の **[DocumentDB アカウント]** ブレードでアカウントの状態を確認できます。

## <a id="deploy-documentdb-from-a-template"></a>作業: ARM テンプレートを利用して DocumentDB アカウントを作成する

このセクションの指示に従い、Azure リソース マネージャー (ARM) のテンプレートとオプションのパラメーター ファイル (いずれも JSON ファイル) で DocumentDB アカウントを作成します。テンプレートを利用すると、誤りなく必要なものを正確に表現し、それを繰り返すことができます。

### ARM テンプレートとリソース グループについて

大部分のアプリケーションは、異なる種類のリソースの組み合わせ (1 つ以上の DocumentDB アカウント、ストレージ アカウント、仮想ネットワーク、コンテンツ配信ネットワークなど) から構築されます。既定の Azure サービス管理 API と Azure ポータルでは、サービス単位のアプローチを使用してこれらの項目を表していました。この方法では、個々のサービスを 1 つの論理的なデプロイ単位としてではなく、個別にデプロイ、管理 (またはこのことを実行するその他のツールを検索) する必要があります。

*Azure リソース マネージャー テンプレート*では、これらの異なるリソースを 1 つの論理的なデプロイ単位として、宣言型の方法でデプロイし、管理することが可能になります。何をデプロイするのかを Azure に 1 コマンドずつ命令するのではなく、JSON ファイル内にデプロイメント全体、つまりすべてのリソースと、関連する構成およびデプロイメント パラメーターを記述し、Azure にそれらのリソースを 1 つのグループとしてデプロイするよう指示します。

Azure リソース グループとその機能の詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」を参照してください。テンプレートの作成に興味がある場合は、「[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)」を参照してください。

### 手順 1: テンプレートとパラメーター ファイルを作成する

次の内容でローカル テンプレート ファイルを作成します。azuredeploy.json ファイルに名前を付けます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "type": "string"
            }
        },
        "variables": { },
        "resources": [
            {
                "apiVersion": "2015-04-08",
                "type": "Microsoft.DocumentDb/databaseAccounts",
                "name": "[parameters('databaseAccountName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "name": "[parameters('databaseAccountName')]",
                    "databaseAccountOfferType": "Standard"
                }
            }
        ]
    }

このテンプレートはパラメーターを 1 つだけ必要とします。作成するデータベース アカウントの名前です。新しいデータベース アカウントの場所はリソース グループと同じ場所に設定されます。

テンプレートに必要なパラメーターは 1 つだけであるため、コマンド ラインで値を入力するか、パラメーター ファイルを作成して値を指定できます。

パラメーター ファイルを作成するには、新しいファイルに次の内容をコピーし、azuredeploy.parameters.json ファイルに名前を付けます。コマンド プロンプトでデータベース アカウント名を指定する場合、このファイルを作成せずに続行できます。

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "databaseAccountName": {
                "value": "samplearmacct"
            }
        }
    }

azuredeploy.parameters.json ファイルで、「samplearmacct」という値を使用するデータベース名に変更し、ファイルを保存します。`"databaseAccountName"` に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。

### 手順 2: リソース グループを作成または取得する

DocumentDB アカウントを作成するには、最初にリソース グループを用意する必要があります。使用するリソース グループ名を既に知っている場合は、場所が [DocumentDB の一般公開されているリージョン](https://azure.microsoft.com/regions/#services)であることを確認し、[手順 3](#create-account-from-template) に進みます。テンプレートでは、アカウントの場所はリソース グループと同じリージョンに作成されるので、DocumentDB を使用できないリージョンにアカウントを作成しようとすると、デプロイメント エラーが発生します。

現在のすべてのリソース グループの一覧を確認するには、次のコマンドを実行し、使用するリソース グループの名前をメモします。

    azure group list

新しいリソース グループを作成するには、作成する新しいリソース グループの名前とリソース グループを作成するリージョンを指定します。

	azure group create <resourcegroupname> <databaseaccountlocation>

 - `<resourcegroupname>` に使用できる文字は、英数字、ピリオド、アンダースコア、「-」、かっこのみです。末尾にピリオドを指定することはできません。
 - `<databaseaccountlocation>` には、DocumentDB が一般公開されているリージョンのいずれかを指定します。最新のリージョン一覧については、[Azure のリージョン ページ](https://azure.microsoft.com/regions/#services)を参照してください。

入力例:

	azure group create new_res_group westus

次の出力が生成されます。

    info:    Executing command group create
    + Getting resource group new_res_group
    + Creating resource group new_res_group
    info:    Created resource group new_res_group
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/new_res_group
    data:    Name:                new_res_group
    data:    Location:            West US
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

エラーが発生した場合、「[トラブルシューティング](#troubleshooting)」を参照してください。

### <a id="create-account-from-template"></a>手順 3: ARM テンプレートを利用して DocumentDB アカウントを作成する

リソース グループで DocumentDB アカウントを作成するには、次のコマンドを実行し、テンプレート ファイルのパス、パラメーター ファイルまたはパラメーター値のパス、デプロイメントするリソース グループの名前、デプロイメント名を指定します (-n は任意です)。

パラメーター ファイルを使用するには:

    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g <resourcegroupname> -n <deploymentname>

 - `<PathToTemplate>` は、手順 1 で作成した azuredeploy.json ファイルのパスです。パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
 - `<PathToParameterFile>` は、手順 1 で作成した azuredeploy.parameters.json ファイルのパスです。パス名にスペースが含まれる場合は、二重引用符でこのパラメーターを囲みます。
 - `<resourcegroupname>` は、DocumentDB データベース アカウントを追加する既存のリソース グループ名です。
 - `<deploymentname>` は、デプロイメント名 (省略可能) です。

入力例:

    azure group deployment create -f azuredeploy.json -e azuredeploy.parameters.json -g new_res_group -n azuredeploy

あるいは、パラメーター ファイルなしでデータベース アカウント名パラメーターを指定し、代わりに値の入力を求めさせるには、次のコマンドを実行します。

    azure group deployment create -f <PathToTemplate> -g <resourcegroupname> -n <deploymentname>

入力例 (「new\_db\_acct」というデータベース アカウントのプロンプトとエントリが表示されます):

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
    info:    group deployment create command OK

エラーが発生した場合、「[トラブルシューティング](#troubleshooting)」を参照してください。

コマンドが終了すると、アカウントは数分間「**作成中**」の状態になります。その後、「**オンライン**」の状態に変化し、使用する準備ができます。[Azure ポータル](https://portal.azure.com)の **[DocumentDB アカウント]** ブレードでアカウントの状態を確認できます。

## トラブルシューティング

リソース グループまたはデータベース アカウントの作成中に `Deployment provisioning state was not successful` のようなエラーが発生する場合、いくつかのトラブルシューティング方法があります。

> [AZURE.NOTE] データベース アカウント名に正しくない文字を指定するか、DocumentDB を使用できない場所を指定すると、デプロイメント エラーが発生します。データベース アカウント名に使用できる文字は、英小文字、数字、「-」のみです。文字数は 3 ～ 50 文字にする必要があります。有効なデータベース アカウントの場所一覧については、[Azure リージョンのページ](https://azure.microsoft.com/regions/#services)を参照してください。

- 出力に次の `Error information has been recorded to C:\Users\wendy\.azure\azure.err` が含まれている場合は、azure.err ファイルでエラー情報を確認してください。

- このログ ファイルには、リソース グループの役立つ情報が記録されている場合があります。ログ ファイルを表示するには、次のコマンドを実行します。

    	azure group log show <resourcegroupname> --last-deployment

    入力例:

    	azure group log show new_res_group --last-deployment

    追加情報については、「[Azure のリソース グループ デプロイのトラブルシューティング](../resource-manager-troubleshoot-deployments-cli.md)」を参照してください。

- 次のスクリーンショットのように、エラー情報は Azure ポータルでも確認できます。エラー情報に移動するには: ジャンプバーで [リソース グループ] をクリックし、エラーが発生したリソース グループを選択します。次に、[リソース グループ] ブレードの [Essentials] 領域で [前回のデプロイ] の日付をクリックし、[デプロイ履歴] ブレードで失敗したデプロイを選択し、[デプロイ] ブレードで赤い感嘆符が付いた [操作の詳細] 詳細をクリックします。失敗したデプロイメントの状態メッセージが [操作の詳細] ブレードに表示されます。

    ![デプロイメント エラー メッセージに移動する方法を示す Azure ポータルのスクリーンショット](media/documentdb-automation-resource-manager-cli/portal-troubleshooting-deploy.png)

## 次のステップ

DocumentDB アカウントを作成できたら、次の手順として DocumentDB データベースを作成します。データベースを作成するには、次のいずれかを使用します。

- Azure ポータル。[Azure ポータルを使用した DocumentDB データベースの作成に関するページ](documentdb-create-database.md)の説明を参照してください。
- GitHub の [azure-documentdb-dotnet](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples) リポジトリの [DatabaseManagement](https://github.com/Azure/azure-documentdb-net/tree/master/samples/code-samples/DatabaseManagement) プロジェクトにある C# .NET のサンプル。
- [DocumentDB SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx)。DocumentDB には、.NET、Java、Python、Node.js、および JavaScript API の SDK があります。

データベースを作成した後に、データベースに [1 つまたは複数のコレクションを追加](documentdb-create-collection.md)し、それらのコレクションに[ドキュメントを追加する](documentdb-view-json-document-explorer.md)必要があります。

コレクションにドキュメントを用意した後で、ポータルの[クエリ エクスプローラー](documentdb-query-collections-query-explorer.md)、[REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx)、またはいずれかの [SDK](https://msdn.microsoft.com/library/azure/dn781482.aspx) を使用することで、[DocumentDB SQL](documentdb-sql-query.md) を使用してドキュメントに対して[クエリを実行](documentdb-sql-query.md#executing-queries)することができます。

DocumentDB の詳細については、以下の資料を参照してください。

-	[DocumentDB のラーニング パス](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-	[DocumentDB のリソース モデルと概念](documentdb-resources.md)

使用できる他のテンプレートについては、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)」を参照してください。

<!---HONumber=AcomDC_0720_2016-->