<properties
   pageTitle="Azure リソース マネージャーのテンプレートを使用したアプリケーションの展開"
   services="azure-portal"
   description="Azure リソース マネージャーを使用してアプリケーションを Azure に展開します。テンプレートは JSON ファイルであり、ポータル、PowerShell、Azure コマンドライン インターフェイス (Mac、Linux、Windows 用)、または REST で使用できます。"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-portal"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/22/2015"
   ms.author="tomfitz"/>

# Azure リソース マネージャーのテンプレートを使用したアプリケーションの展開

このトピックでは、Azure リソース マネージャーのテンプレートを使用して Azure にアプリケーションを展開する方法について説明します。また、Azure PowerShell、Azure CLI、REST API、または Microsoft Azure プレビュー ポータルを使用して、アプリケーションを展開する方法も示します。

Azure リソース マネージャーのテンプレートを使用すると、宣言型の JSON によって Azure でアプリケーションを迅速かつ簡単にプロビジョニングすることができます。1 つの JSON テンプレートで、Virtual Machines、Virtual Networks、Storage、App Services、およびデータベースなど、複数のサービスを展開できます。アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用して繰り返し一貫した方法でアプリケーションを展開します。

アプリケーションの管理を簡素化するために、共通のライフサイクルを共有するすべてのリソースを 1 つのリソース グループにまとめることができます。リソース グループを作成すれば、関連するリソースをまとめて簡単に展開、更新、および削除することができます。ほとんどの場合、リソース グループは 1 つのアプリケーションまたはアプリケーション層 (大規模なアプリケーションの場合) にマップされます。テンプレートを使用して展開されるリソースは、単一リソース グループに配置されます。ただし、これには他のリソース グループでの依存関係を含めることができます。

リソース グループ内では、展開の実行を追跡して、展開の状態とテンプレートの実行出力を確認することができます。

テンプレートを使用してアプリケーションを展開する場合は、パラメーター値を指定することで、リソースの作成方法をカスタマイズすることができます。これらのパラメーターの値は、インラインか、パラメーター ファイルのどちらかで指定します。

## 概念

- リソース グループ - 一般的なライフ サイクルを共有するエンティティのコレクション
- リソース マネージャーのテンプレート - 展開の目標とする状態を定義した宣言型の JSON ファイル
- 展開 - リソース マネージャーのテンプレートの実行を追跡する操作
- パラメーター - 展開を実行するユーザーが展開リソースをカスタマイズするために指定する値
- パラメーター ファイル - パラメーターの名前と値を格納する JSON ファイル 

## シナリオ

リソース マネージャーのテンプレートを使用すると、次のことができます。

- Microsoft SharePoint など、複雑な多階層アプリケーションを展開します。
- 一貫した方法で繰り返しアプリケーションを展開します。
- 開発、テスト、および実稼働の環境をサポートします。
- 展開の状態を表示します。
- 展開監査ログを使用して、展開エラーのトラブルシューティングを行います。

## プレビュー ポータルで展開する

ご存知でしょうか。 ギャラリー内のアプリケーションはどれも Azure リソース マネージャーのテンプレートによってサポートされています。 ポータルを使用して Virtual Machine、Virtual Network、Storage Account、App Service、またはデータベースを作成するだけで、追加の操作を行わなくても、Azure リソース マネージャーの恩恵を得られます。

プレビュー ポータルで展開のトラブルシューティングを行うには、**[参照]** -> **[リソース グループ]** -> *[YourResourceGroupName]* をクリックします。ここで、**[監視]** レンズの下にある **[イベント]** タイルをクリックします。最後に、個別の **[操作]** と **[イベント]** を選択して詳細を表示できます。

## PowerShell で展開する

リソース マネージャーで Azure PowerShell を使用したことがない場合は、「[Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

1. Azure アカウントにログインします。資格情報を提供すると、コマンドがアカウントの情報を返します。

        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. 複数のサブスクリプションがある場合、デプロイメントに使用するサブスクリプション ID を提供します。

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Azure リソース マネージャー モジュールに切り替える

        PS C:\> Switch-AzureMode AzureResourceManager

4. 既存のリソース グループがない場合は、新しいリソース グループを作成します。ソリューションに必要なリソース グループと場所の名前を指定します。新しいリソース グループの概要が返されます。

        PS C:\> New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. リソース グループに新しいデプロイメントを作成するには、**New-AzureResourceGroupDeployment** コマンドを実行して必要なパラメーターを指定します。パラメーターにはデプロイメントの名前、リソース グループの名前、作成したテンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。
   
     次のオプションを使用してパラメーターの値を提供できます。
   
     - インライン パラメーターを使用する

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - パラメーター オブジェクトを使用する

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - パラメーター ファイルの使用テンプレート ファイルについては、「[パラメーター ファイル](./#parameter-file)」を参照してください。

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     リソース グループをデプロイすると、デプロイメントの概要が表示されます。

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. デプロイメント エラーに関する情報を取得するには

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. デプロイメント エラーに関する詳細情報を取得するには

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

## Mac、Linux、および Windows 用の Azure CLI で展開する

リソース マネージャーで Azure CLI を使用したことがない場合は、「[Azure リソース管理での、Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)」に関するページを参照してください。

1. Azure アカウントにログインします。資格情報を提供すると、コマンドがログインの結果を返します。

        azure login
  
        ...
        info:    login command OK

2. 複数のサブスクリプションがある場合、デプロイメントに使用するサブスクリプション ID を提供します。

        azure account set <YourSubscriptionNameOrId>

3. Azure リソース マネージャー モジュールに切り替えます。新しいモードの確認が表示されます。

        azure config mode arm
   
        info:     New mode is arm

4. 既存のリソース グループがない場合は、新しいリソース グループを作成します。ソリューションに必要なリソース グループと場所の名前を指定します。新しいリソース グループの概要が返されます。

        azure group create -n ExampleResourceGroup -l "West US"
   
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

5. リソース グループに新しいデプロイメントを作成するには、次のコマンドを実行して必要なパラメーターを指定します。パラメーターにはデプロイメントの名前、リソース グループの名前、作成したテンプレートへのパスや URL、シナリオに必要なその他のパラメーターが含まれます。
   
     次のオプションを使用してパラメーターの値を提供できます。

     - インライン パラメーターとローカルのテンプレートを使用します。

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - インライン パラメーターとローカルのテンプレートへのリンクを使用します。

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - パラメーター ファイルを使用します。テンプレート ファイルについては、「[パラメーター ファイル](./#parameter-file)」を参照してください。
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     リソース グループをデプロイすると、デプロイメントの概要が表示されます。
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. 最新のデプロイメントに関する情報を取得するには

         azure group log show -l ExampleResourceGroup

7. デプロイメント エラーに関する詳細情報を取得するには
      
         azure group log show -l -v ExampleResourceGroup

## REST API で展開する
1. [一般的なパラメーターおよびヘッダー](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common) (認証トークンを含む) を設定します。
2. 既存のリソース グループがない場合は、新しいリソース グループを作成します。ソリューションに必要なサブスクリプション ID、新しいリソース グループの名前、および場所を指定します。詳細については、「[リソース グループの作成](https://msdn.microsoft.com/library/azure/dn790525.aspx)」を参照してください。

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "West US",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }
   
3. リソース グループの新しい展開を作成します。サブスクリプション ID、展開するリソース グループの名前、展開の名前、およびテンプレートの場所を指定します。テンプレート ファイルについては、「[パラメーター ファイル](./#parameter-file)」を参照してください。リソース グループを作成する REST API の詳細については、「[テンプレートの展開を作成する](https://msdn.microsoft.com/library/azure/dn790564.aspx)」を参照してください。
    
         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
            <common headers>
            {
              "properties": {
                "templateLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                  "contentVersion": "1.0.0.0",
                },
                "mode": "Incremental",
                "parametersLink": {
                  "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                  "contentVersion": "1.0.0.0",
                }
              }
            }
   
4. テンプレートの展開の状態を取得します。詳細については、「[テンプレートの展開に関する情報を取得](https://msdn.microsoft.com/library/azure/dn790565.aspx)」を参照してください。

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## パラメーター ファイル

展開中にパラメーター ファイルを使用してパラメーター値をテンプレートに渡す場合は、次の例に示すような形式の JSON ファイルを作成する必要があります。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            }
       }
    }

## 次のステップ
- [Azure リソース マネージャーの概要](../resource-group-overview.md)
- [.NET ライブラリとテンプレートを使用したリソースの展開](../arm-template-deployment.md)
- [Azure で複雑なアプリケーションを予測どおりに展開する](../app-service-web/app-service-deploy-complex-application-predictably.md)
- [テンプレートの作成](../resource-group-authoring-templates.md)
- [テンプレート関数](../resource-group-template-functions.md)
- [高度なテンプレートの操作](../resource-group-advanced-template.md)  

 

<!---HONumber=62-->