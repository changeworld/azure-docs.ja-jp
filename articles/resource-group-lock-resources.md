<properties 
	pageTitle="Azure リソース マネージャーによるリソースのロック" 
	description="ユーザーが特定のリソースを更新または削除できないようリソースをロックします。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2015" 
	ms.author="tomfitz"/>

# Azure リソース マネージャーによるリソースのロック

管理者は、状況によっては、リソースまたはリソース グループにロックを適用し、組織の他のユーザーが書き込み操作をコミットしたり、誤って重要なリソースを削除したりすることを防ぐ必要があります。

Azure リソース マネージャーは、リソース管理のロックを通じて、リソースに対する操作を制限する機能を提供します。リソース ロックは、特定のスコープでのロックのレベルを適用するポリシーです。ロックのレベルでは、適用するポリシーの種類を識別します。現在、**CanNotDelete** と **ReadOnly** という 2 つの値があります。スコープは、URI として表され、リソースまたはリソース グループを指定できます。

ロックは、特定の操作を実行するためにユーザーのアクセス許可を割り当てることとは異なります。ユーザーおよびロールのアクセス許可の設定の詳細については、[プレビュー ポータルでのロールに基づいたアクセス制御](role-based-access-control-configure.md)に関するページと「[リソースへのアクセスの管理と監査](resource-group-rbac.md)」を参照してください。

## 一般的なシナリオ

一般的なシナリオの 1 つに、リソース グループ内に、オンとオフを繰り返して使用されるリソースが存在する場合が挙げられます。VM のリソースが定期的にオンになり、一定の時間データを処理し、その後オフになるような場合です。このシナリオでは、VM のシャットダウンを有効にする必要がありますが、一方でストレージ アカウントを削除しないことが必須条件となります。このシナリオでは、ストレージ アカウントに対して、ロック レベルが **CanNotDelete** のリソース ロックを使用ことができます。

また、別のシナリオとして、運用システムに更新を適用しない時間帯を設定する場合もあります。**ReadOnly** のロック レベルを使用すると、作成または更新を停止できます。小売企業であれば、祝日のショッピング期間中に更新を許可しないようにすることができます。金融サービス企業であれば、市場の取引時間帯によって、デプロイに関連する制約を設けることができます。リソース ロックでは、適切にリソースをロックするポリシーを提供できます。これを、特定のリソースにのみ、またはリソース グループ全体に適用することができます。

## テンプレートでのロックの作成

次の例では、ストレージ アカウントに対するロックを作成するテンプレートを示します。ロックを適用するストレージ アカウントは、パラメーターとして指定し、このパラメーターは concat() 関数と組み合わせて使用します。この結果、リソース名は 'Microsoft.Authorization' とロックの名前 (この例では **myLock**) が連結された名前となります。

指定した種類は、リソースの種類に固有です。ストレージの場合は、この種類は、"Microsoft.Storage/storageaccounts/providers/locks" です。

スコープ レベルは、リソースの **level** プロパティを使用して設定されます。この例では、誤って削除されないことを重視しているため、レベルを **CannotDelete** に設定します。

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

スコープには、サブスクリプション、リソース グループまたはリソースを使用できます。lock-name には、ロックの名前を指定できます。api-version には、**2015-01-01** を使用します。

要求に、ロックのプロパティを指定する JSON オブジェクトを含めます。

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

ロックレベルには、**CanNotDelete** または **ReadOnly** を指定します。

例については、[管理ロック用の REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx) に関するページを参照してください。

## Azure PowerShell を使用したロックの作成

Azure PowerShell を使用してデプロイ済みのリソースをロックするには、次のように **New-AzureResourceLock** を使用します。

    PS C:> New-AzureResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName ExampleGroup

PowerShell では、動作中のロックに対するその他のコマンドを使用できます。たとえば、ロックを更新する **Set-AzureResourceLock**、ロックを削除する **Remove-AzureResourceLock** があります。

## 次のステップ

- [タグを使用した Azure リソースの整理](resource-group-using-tags.md)
- [新しいリソース グループへのリソースの移動](resource-group-move-resources.md)

<!---HONumber=July15_HO3-->