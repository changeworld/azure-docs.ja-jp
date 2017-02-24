---
title: "Azure CLI とテンプレートを使用してリソースをデプロイする | Microsoft Docs"
description: "Azure Resource Manager と Azure CLI を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e4851e872349fa6483e1f1a340d0968e845a3518
ms.openlocfilehash: ccbb918a3377094395a27a9b7a63f213c5085027


---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ
> [!div class="op_single_selector"]
> * [PowerShell](resource-group-template-deploy.md)
> * [Azure CLI](resource-group-template-deploy-cli.md)
> * [ポータル](resource-group-template-deploy-portal.md)
> * [REST API](resource-group-template-deploy-rest.md)
> 
> 

このトピックでは、Azure CLI と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。  テンプレートは、ローカル ファイルまたは URI を通じて利用できる外部ファイルのいずれも使用できます。 テンプレートがストレージ アカウントに存在する場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

> [!TIP]
> デプロイ時のエラーのデバッグについては、以下を参照してください。
> 
> * 「[デプロイ操作の表示](resource-manager-deployment-operations.md)」。エラーのトラブルシューティングに役立つ情報を掲載しています。
> * [Azure Resource Manager を使用してリソースを Azure にデプロイするときに発生する一般的なエラーをトラブルシューティングする](resource-manager-common-deployment-errors.md) 」
> 
> 

## <a name="quick-steps-to-deployment"></a>簡単なデプロイ手順
デプロイを簡単に開始するには、次のコマンドを使用します。

```
azure group create -n examplegroup -l "West US"
azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g examplegroup -n exampledeployment
```

これらのコマンドは、リソース グループを作成し、そのリソース グループにテンプレートをデプロイします。 テンプレート ファイルとパラメーター ファイルは、両方ともローカル ファイルです。 成功した場合は、リソースのデプロイに必要なすべてを入手できたことになります。 ただし、デプロイするリソースを指定する方法は、より多くのオプションから選択できます。 この記事の残りの部分では、デプロイ時に使用できるオプションをすべて説明します。 

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

## <a name="deploy"></a>デプロイ
リソース マネージャーで Azure CLI を使用したことがない場合は、「 [Azure リソース管理での、Mac、Linux、および Windows 用 Azure CLI の使用](xplat-cli-azure-resource-manager.md)」に関するページを参照してください。

1. Azure アカウントにログインします。 資格情報を提供すると、コマンドがログインの結果を返します。
   
   ```
   azure login
   ```
2. 複数のサブスクリプションがある場合、デプロイに使用するサブスクリプション ID を提供します。
   
   ```
   azure account set <YourSubscriptionNameOrId>
   ```
4. テンプレートをデプロイするときは、デプロイ済みのリソースを格納するリソース グループを指定する必要があります。 デプロイする既存のリソース グループがある場合は、この手順をスキップしてそのリソース グループを使用することができます。 
   
     リソース グループを作成するには、リソース グループの名前と場所を指定します。 リソース グループにはリソースに関するメタデータが格納されるため、リソース グループの場所を入力します。 場合によっては、コンプライアンス上の理由から、そのメタデータが格納される場所を指定する必要があります。 一般に、ほとんどのリソースが存在する場所を指定することをお勧めします。 同じ場所を使用することで、テンプレートを簡素化できます。
   
   ```
   azure group create -n ExampleResourceGroup -l "West US"
   ```

     新しいリソース グループの概要が返されます。
   
5. **azure group template validate** コマンドを実行して、デプロイを実行する前に検証します。 デプロイをテストする場合、(次の手順で示すように) デプロイの実行時に必要なパラメーターを正確に指定します。
   
   ```
   azure group template validate -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup
   ```
6. リソース グループにリソースをデプロイするには、**azure group deployment create** コマンドを実行して必要なパラメーターを指定します。 パラメーターには、デプロイの名前、リソース グループの名前、テンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。 **mode** パラメーターを指定しない場合、**Incremental** の既定値が使用されます。 完全デプロイを実行するには、**[モード]** を **[完全]** に設定します。 テンプレートにないリソースを誤って削除する可能性があるため、完全モードを使用する際は注意してください。
   
     ローカル テンプレートをデプロイするには、**template-file** パラメーターを使用します。
   
   ```
   azure group deployment create --resource-group examplegroup --template-file "c:\MyTemplates\example.json"
   ```
   
     外部テンプレートをデプロイするには、**template-uri** パラメーターを使用します。
   
   ```
   azure group deployment create --resource-group examplegroup --template-uri "https://raw.githubusercontent.com/exampleuser/MyTemplates/master/example.json"
   ```
   
     前の&2; つの例には、パラメーター値は含まれません。 パラメーター値を渡す方法については、「[パラメーター](#parameters)」のセクションを参照してください。 ここでは、次の構文でパラメーター値を入力するように求められます。

   ```
   info:    Executing command group deployment create
   info:    Supply values for the following parameters
   firstParameters:  <type here>
   ```

      リソースがデプロイされると、デプロイの概要が表示されます。 概要には、**ProvisioningState** が含まれます。これは、デプロイが成功したかどうかを示します。

   ```
   + Initializing template configurations and parameters
   + Creating a deployment
   info:    Created template deployment "example"
   + Waiting for deployment to complete
   +
   +
   data:    DeploymentName     : example
   data:    ResourceGroupName  : examplegroup
   data:    ProvisioningState  : Succeeded
   ```
7. デプロイ エラーをトラブルシューティングするために役立つ可能性がある追加情報を記録する場合は、 **debug-setting** パラメーターを使用します。 デプロイ操作と共に要求の内容、応答の内容、またはその両方を記録するように指定できます。
   
   ```
   azure group deployment create --debug-setting All -f <PathToTemplate> -e <PathToParameterFile> -g examplegroup -n exampleDeployment
   ```

## <a name="deploy-template-from-storage-with-sas-token"></a>SAS トークンを使用してストレージからテンプレートをデプロイする
SAS トークンを使用したデプロイの際に、テンプレートをストレージ アカウントに追加し、リンクできます。

> [!IMPORTANT]
> 次の手順に従うことにより、テンプレートを含む BLOB はアカウントの所有者だけがアクセスできるようになります。 ただし、BLOB の SAS トークンを作成すると、その URI を持つ誰もが BLOB にアクセスできるようになります。 もし別のユーザーが URI を傍受した場合、そのユーザーは、テンプレートにアクセスできます。 SAS トークンの使用はテンプレートへのアクセスを制限する有効な方法ですが、パスワードのような機密データをテンプレートに直接含めないでください。
> 
> 

### <a name="add-private-template-to-storage-account"></a>ストレージ アカウントにプライベートのテンプレートを追加する
次の手順で、テンプレートのストレージ アカウントをセットアップします。

1. リソース グループを作成します。
   
   ```
   azure group create -n "ManageGroup" -l "westus"
   ```
2. ストレージ アカウントを作成します。 ストレージ アカウント名は、 Azure 内で一意である必要があるため、独自の名前を入力してください。
   
   ```
   azure storage account create -g ManageGroup -l "westus" --sku-name LRS --kind Storage storagecontosotemplates
   ```
3. ストレージ アカウントとキーの変数を設定します。
   
   ```
   export AZURE_STORAGE_ACCOUNT=storagecontosotemplates
   export AZURE_STORAGE_ACCESS_KEY={storage_account_key}
   ```
4. コンテナーを作成します。 アクセス許可は **Off** に設定します。これは所有者だけがコンテナーにアクセスできることを表します。
   
   ```
   azure storage container create --container templates -p Off 
   ```
5. コンテナーに、テンプレートを追加します。
   
   ```
   azure storage blob upload --container templates -f c:\MyTemplates\azuredeploy.json
   ```

### <a name="provide-sas-token-during-deployment"></a>デプロイ時に SAS トークンを指定する
ストレージ アカウントにプライベートのテンプレートをデプロイするため、SAS トークンを取得しテンプレートの URI に含めます。

1. アクセスを制限するため、読み取りアクセス許可と有効期限を持つ SAS トークンを作成します。 デプロイの完了に必要な時間を確保できるように有効期限を設定します。 SAS トークンを含むテンプレートの完全な URI を取得します。
   
   ```
   expiretime=$(date -I'minutes' --date "+30 minutes")
   fullurl=$(azure storage blob sas create --container templates --blob azuredeploy.json --permissions r --expiry $expiretimetime --json  | jq ".url")
   ```
2. SAS トークンを含む URI を指定することにより、テンプレートをデプロイします。
   
   ```
   azure group deployment create --template-uri $fullurl -g ExampleResourceGroup
   ```

リンクされたテンプレートでの SAS トークン使用例については、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

## <a name="parameters"></a>parameters

次のオプションを使用してパラメーターの値を提供できます。 
   
- インライン パラメーターを使用する 各パラメーターは、 `"ParameterName": { "value": "ParameterValue" }`の形式になります。 次の例では、エスケープ文字を含むパラメーターを示します。

   ```   
   azure group deployment create -f <PathToTemplate> -p "{\"ParameterName\":{\"value\":\"ParameterValue\"}}" -g ExampleResourceGroup -n ExampleDeployment
   ```
- パラメーター ファイルを使用します。

  ```    
  azure group deployment create -f "c:\MyTemplates\example.json" -e "c:\MyTemplates\example.params.json" -g ExampleResourceGroup -n ExampleDeployment
  ```
      

[!INCLUDE [resource-manager-parameter-file](../../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>次のステップ
* .NET クライアント ライブラリを使用したリソースのデプロイの例については、「[Deploy resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」 (.NET ライブラリとテンプレートを使用した Azure リソースのデプロイ) を参照してください。
* テンプレートのパラメーターの定義については、 [テンプレートの作成](resource-group-authoring-templates.md#parameters)に関する記事を参照してください。
* ソリューションを別の環境にデプロイする方法については、「 [Microsoft Azure の開発環境とテスト環境](solution-dev-test-environments.md)」を参照してください。
* セキュリティで保護された値を渡す KeyVault 参照を使用する方法については、「 [デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
* デプロイの自動化についての&4; 回シリーズの解説については、「[Automating application deployments to Azure Virtual Machines (Azure Virtual Machines へのアプリケーションのデプロイの自動化)](../virtual-machines/virtual-machines-windows-dotnet-core-1-landing.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 このシリーズでは、アプリケーションのアーキテクチャ、アクセスとセキュリティ、可用性と拡張性、およびアプリケーションのデプロイについて説明しています。




<!--HONumber=Jan17_HO2-->


