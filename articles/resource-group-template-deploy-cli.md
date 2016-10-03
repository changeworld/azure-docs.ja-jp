<properties
   pageTitle="Azure CLI とテンプレートを使用してリソースをデプロイする |Microsoft Azure"
   description="Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。リソースは Resource Manager テンプレートで定義されます。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Azure CLI](resource-group-template-deploy-cli.md)
- [ポータル](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

このトピックでは、Azure CLI と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。

> [AZURE.TIP] デプロイ時のエラーのデバッグについては、以下を参照してください。
>
> - エラーのトラブルシューティングに役立つ情報を入手するためには、「[Azure CLI でのデプロイ操作の表示](resource-manager-troubleshoot-deployments-cli.md)」
> - 一般的なデプロイ エラーを解決するためには、「[Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーをトラブルシューティングする](resource-manager-common-deployment-errors.md)」

テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

## 簡単なデプロイ手順

この記事では、デプロイ時に使用できるさまざまなオプションをすべて説明します。ただし、多くの場合、必要なのは 2 つの簡単なコマンドのみです。デプロイを簡単に開始するには、次のコマンドを使用します。

    azure group create -n ExampleResourceGroup -l "West US"
    azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

ご使用のシナリオにさらに適したデプロイのオプションについて詳しく知るには、この記事を読み進めてください。

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## Azure CLI でのデプロイ

リソース マネージャーで Azure CLI を使用したことがない場合は、「[Azure リソース管理での、Mac、Linux、および Windows 用 Azure CLI の使用](xplat-cli-azure-resource-manager.md)」に関するページを参照してください。

1. Azure アカウントにログインします。資格情報を提供すると、コマンドがログインの結果を返します。

        azure login
  
        ...
        info:    login command OK

2. 複数のサブスクリプションがある場合、デプロイメントに使用するサブスクリプション ID を提供します。

        azure account set <YourSubscriptionNameOrId>

3. Azure リソース マネージャー モジュールに切り替えます。新しいモードの確認が表示されます。

        azure config mode arm
   
        info:     New mode is arm

4. 既存のリソース グループがない場合は、リソース グループを作成します。ソリューションに必要なリソース グループと場所の名前を指定します。リソース グループにはリソースに関するメタデータが格納されるため、リソース グループの場所を入力する必要があります。場合によっては、コンプライアンス上の理由から、そのメタデータが格納される場所を指定する必要があります。一般に、ほとんどのリソースが存在する場所を指定することをお勧めします。同じ場所を使用することで、テンプレートを簡素化できます。

        azure group create -n ExampleResourceGroup -l "West US"

     新しいリソース グループの概要が返されます。
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. **azure group template validate** コマンドを実行して、デプロイを実行する前に検証します。デプロイをテストする場合、(次の手順で示すように) デプロイの実行時に必要なパラメーターを正確に指定します。

        azure group template validate -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup

5. リソース グループにリソースをデプロイするには、次のコマンドを実行して必要なパラメーターを指定します。パラメーターには、デプロイの名前、リソース グループの名前、テンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。
   
     次の 3 つのオプションを使用してパラメーターの値を提供できます。

     1. インライン パラメーターとローカルのテンプレートを使用します。各パラメーターは、`"ParameterName": { "value": "ParameterValue" }` の形式になります。次の例では、エスケープ文字を含むパラメーターを示します。

            azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     2. インライン パラメーターとローカルのテンプレートへのリンクを使用します。

            azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     3. パラメーター ファイルを使用します。テンプレート ファイルについては、「[パラメーター ファイル](./#parameter-file)」を参照してください。
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     上記の 3 つの方法のいずれかを使用してリソースがデプロイされると、デプロイの概要が表示されます。
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     完全デプロイメントを実行するには、**[モード]** を **[完全]** に設定します。テンプレートにないリソースを誤って削除する可能性があるため、このモードを使用する際は注意してください。

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. デプロイ エラーをトラブルシューティングするために役立つ可能性がある追加情報を記録する場合は、**debug-setting** パラメーターを使用します。デプロイ操作と共に要求の内容、応答の内容、またはその両方を記録するように指定できます。

        azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

## SAS トークンを使用してストレージからテンプレートをデプロイする

SAS トークンを使用したデプロイの際に、テンプレートをストレージ アカウントに追加し、リンクできます。

> [AZURE.IMPORTANT] 次の手順に従うことにより、テンプレートを含む BLOB はアカウントの所有者だけがアクセスできるようになります。ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。SAS トークンの使用はテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。

### ストレージ アカウントにプライベートのテンプレートを追加する

次の手順で、テンプレートのストレージ アカウントをセットアップします。

1. リソース グループを作成します。

        azure group create -n "ManageGroup" -l "westus"

2. ストレージ アカウントを作成します。ストレージ アカウント名は、 Azure 内で一意である必要があるため、独自の名前を入力してください。

        azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates

3. ストレージ アカウントとキーの変数を設定します。

        export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
        export AZURE_STORAGE_ACCESS_KEY={storage_account_key}

4. コンテナーを作成します。アクセス許可は **Off** に設定します。これは所有者だけがコンテナーにアクセスできることを表します。

        azure storage container create --container templates -p Off 
        
4. コンテナーに、テンプレートを追加します。

        azure storage blob upload --container templates -f c:\Azure\Templates\azuredeploy.json
        
### デプロイ時に SAS トークンを指定する

ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを取得しテンプレートの URI に含めます。

1. アクセスを制限するため、読み取りアクセス許可と有効期限を持つ SAS トークンを作成します。デプロイの完了に必要な時間を確保できるように有効期限を設定します。SAS トークンを含むテンプレートの完全な URI を取得します。

        expiretime=$(date -I'minutes' --date "+30 minutes")
        fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")

2. SAS トークンを含む URI を指定することにより、テンプレートをデプロイします。

        azure group deployment create --template-uri $fullurl -g ExampleResourceGroup

リンクされたテンプレートでの SAS トークン使用例については、「[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## 次のステップ
- .NET クライアント ライブラリを使用したリソースのデプロイの例については、[.NET ライブラリとテンプレートを使用したリソースのデプロイ](virtual-machines/virtual-machines-windows-csharp-template.md)に関する記事を参照してください。
- テンプレートのパラメーターの定義については、[テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
- ソリューションを別の環境にデプロイする方法については、「[Microsoft Azure の開発環境とテスト環境](solution-dev-test-environments.md)」を参照してください。
- セキュリティで保護された値を渡す KeyVault 参照を使用する方法については、「[デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->