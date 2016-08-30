<properties 
	pageTitle="リソース マネージャーを使用したリソースのロック | Microsoft Azure" 
	description="すべてのユーザーとロールに制限を適用することで、ユーザーが特定のリソースの更新または削除を実行できないようにします。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="tomfitz"/>

# Azure リソース マネージャーによるリソースのロック

管理者は、サブスクリプション、リソース グループ、またはリソースをロックし、組織の他のユーザーが誤って重要なリソースを削除したり変更したりするのを防止しなければならないことがあります。ロック レベルは **CanNotDelete** または **ReadOnly** に設定できます。

- **CanNotDelete** は、正規ユーザーはリソースの読み取りと変更を実行できますが、削除ができないことを示します。
- **ReadOnly** は、正規ユーザーはリソースの読み取りを実行できますが、リソースの削除やリソースへのアクションの実行はできないことを示します。リソースへのアクセス許可は、 **リーダー** ロールに限定されています。

**ReadOnly** を適用すると、読み取り操作のように見えるいくつかの操作には実際に追加のアクションが必要となるため、予期しない結果を招く可能性があります。たとえば、**ReadOnly** ロックをストレージ アカウントに設定すると、すべてのユーザーがキーを一覧表示できなくなります。返されるキーは書き込み操作に使用できるため、キーの一覧表示操作は POST 要求を介して処理されます。別の例として、**ReadOnly** ロックを App Service リソースに配置すると、Visual Studio のサーバー エクスプローラーの操作には書き込みアクセスが必要となるため、Visual Studio のサーバー エクスプローラーはリソース用のファイルを表示できなくなります。

ロールベースのアクセス制御とは異なり、管理ロックを使用すると、すべてのユーザーとロールに対して制限を適用することができます。ユーザーとロールのアクセス許可を設定する方法については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

親スコープでロックを適用すると、すべての子リソースは同じロックを継承します。後で追加するリソースも、親からロックを継承します。継承されるロックの中で最も制限の厳しいロックが優先されます。

## 組織でロックを作成または削除できるユーザー

管理ロックを作成または削除するには、**Microsoft.Authorization/*** または **Microsoft.Authorization/locks/*** アクションにアクセスできる必要があります。組み込みのロールのうち、**所有者**と**ユーザー アクセスの管理者**にのみこれらのアクションが許可されています。

## ポータルを使用したロックの作成

[AZURE.INCLUDE [resource-manager-lock-resources](../includes/resource-manager-lock-resources.md)]

## テンプレートでのロックの作成

次の例では、ストレージ アカウントに対するロックを作成するテンプレートを示します。ロックを適用するストレージ アカウントはパラメーターとして提供されます。ロックの名前を作成するには、リソース名に **/Microsoft.Authorization/** とロックの名前 (この例では、**myLock**) を連結します。

指定した種類は、リソースの種類に固有です。ストレージの場合は、種類を "Microsoft.Storage/storageaccounts/providers/locks" に設定します。

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "lockedResource": {
          "type": "string"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
          "type": "Microsoft.Storage/storageAccounts/providers/locks",
          "apiVersion": "2015-01-01",
          "properties": {
            "level": "CannotDelete"
          }
        }
      ]
    }

## REST API によるロックの作成

[管理ロック用の REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx) を使用して、デプロイ済みのリソースをロックできます。REST API を使用すると、ロックを作成し、削除できます。また、既存のロックに関する情報を取得することもできます。

ロックを作成するには、次のように実行します。

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。lock-name には、ロックの名前を指定できます。api-version には、**2015-01-01** を使用します。

要求に、ロックのプロパティを指定する JSON オブジェクトを含めます。

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

例については、[管理ロック用の REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx) に関するページを参照してください。

## Azure PowerShell を使用したロックの作成

Azure PowerShell を使用してデプロイ済みのリソースをロックするには、次の例のように **New-AzureRmResourceLock** を使用します。

    New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup

Azure PowerShell では、動作中のロックに対するその他のコマンドを使用できます。たとえば、ロックを更新する **Set-AzureRmResourceLock**、ロックを削除する **Remove-AzureRmResourceLock** があります。

## 次のステップ

- リソース ロックの操作の詳細については、「[Azure リソースのロック ダウン](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)」を参照してください。
- リソースを理論的に整理する方法については、「[タグを使用したリソースの整理](resource-group-using-tags.md)」を参照してください。
- リソースが存在するリソース グループを変更するには、「[新しいリソース グループへのリソースの移動](resource-group-move-resources.md)」を参照してください。
- カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。詳細については、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。

<!---HONumber=AcomDC_0817_2016-->