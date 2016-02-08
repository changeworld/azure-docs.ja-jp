<properties 
	pageTitle="リソース マネージャーを使用したリソースのロック | Microsoft Azure" 
	description="すべてのユーザーとロールに制限を適用することで、ユーザーが特定のリソースの更新または削除を実行できないようにします。" 
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
	ms.date="01/21/2016" 
	ms.author="tomfitz"/>

# Azure リソース マネージャーによるリソースのロック

管理者は、状況によっては、サブスクリプション、リソース グループ、またはリソースにロックを適用し、組織の他のユーザーが誤って重要なリソースを削除することを防ぐ必要があります。

Azure リソース マネージャーは、リソース管理のロックを通じて、リソースに対する操作を制限する機能を提供します。ロックは、特定のスコープでのロックのレベルを適用するポリシーです。スコープには、サブスクリプション、リソース グループまたはリソースを使用できます。ロックのレベルで適用するポリシーの種類を識別します。現在設定できるレベルは **CanNotDelete** です。**CanNotDelete** は、正規ユーザーはリソースの読み取りと変更を実行できますが、制限されているリソースは削除できないことを示します。

ロックは、ロールベースのアクセス制御を使用して特定の操作を実行するアクセス許可をユーザーに割り当てる処理とは異なります。ユーザーとロールのアクセス許可を設定する方法については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。ロールベースのアクセス制御とは異なり、管理ロックを使用すると、すべてのユーザーとロールに対して制限を適用することができます。一般的に、ロックは指定した期間にのみ適用します。

親スコープでロックを適用すると、すべての子リソースは同じロックを継承します。

## 一般的なシナリオ

一般的なシナリオの 1 つに、オンとオフを繰り返して使用されるリソースがリソース グループ内に存在する場合が挙げられます。VM のリソースが定期的にオンになり、一定の時間データを処理し、その後オフになるような場合です。このシナリオでは、VM のシャットダウンを有効にする必要がありますが、一方でストレージ アカウントを削除しないことが必須条件となります。このシナリオでは、ストレージ アカウントに対して、ロック レベルが **CanNotDelete** のリソース ロックを使用ことができます。

## 組織でロックを作成または削除できるユーザー

管理ロックを作成または削除するには、**Microsoft.Authorization/*** または **Microsoft.Authorization/locks/*** アクションにアクセスできる必要があります。組み込みのロールのうち、**所有者**と**ユーザー アクセスの管理者**にのみこれらのアクションが許可されています。アクセス制御の割り当ての詳細については、「[Azure のロールベースのアクセス制御](./active-directory/role-based-access-control-configure.md)」を参照してください。

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

ロックレベルには **CanNotDelete** を指定します。

例については、[管理ロック用の REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx) に関するページを参照してください。

## Azure PowerShell を使用したロックの作成

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Azure PowerShell を使用してデプロイ済みのリソースをロックするには、次のように **New-AzureRmResourceLock** を使用します。PowerShell で、**LockLevel** を**CanNotDelete** に設定するだけです。

    PS C:\> New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

Azure PowerShell では、動作中のロックに対するその他のコマンドを使用できます。たとえば、ロックを更新する **Set-AzureRmResourceLock**、ロックを削除する **Remove-AzureRmResourceLock** があります。

## 次のステップ

- リソース ロックの操作の詳細については、「[Azure リソースのロック ダウン](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)」を参照してください。
- リソースを理論的に整理する方法については、「[タグを使用したリソースの整理](resource-group-using-tags.md)」を参照してください。
- リソースが存在するリソース グループを変更するには、「[新しいリソース グループへのリソースの移動](resource-group-move-resources.md)」を参照してください。
- カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。詳細については、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。

<!---HONumber=AcomDC_0128_2016-->