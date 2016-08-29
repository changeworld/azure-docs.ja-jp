<properties
	pageTitle="タグを使用した Azure リソースの整理 | Microsoft Azure"
	description="タグを適用して、課金や管理のためにリソースを整理する方法を示します。"
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="tomfitz"/>


# タグを使用した Azure リソースの整理

リソース マネージャーを使用すると、タグを適用してリソースを理論的に整理できます。タグは、定義したプロパティによりリソースを識別するキーと値のペアで構成されます。同じカテゴリに属すリソースとしてマークするには、これらのリソースに同じタグを適用します。

特定のタグを持つリソースを表示した場合、すべてのリソース グループからのリソースが表示されます。同じリソース グループ内のリソースに限定されないため、デプロイ関係にとらわれずにリソースを整理できます。タグは、課金または管理の目的でリソースを整理する必要がある場合に役立ちます。

リソースまたはリソース グループに追加する各タグは、サブスクリプション全体の分類に自動的に追加されます。タグの名前でサブスクリプションの分類を事前設定し、リソースとして使用する値を後でタグ付けすることもできます。

各リソースまたはリソース グループには、最大で 15 個のタグを含めることができます。タグ名は 512 文字まで、タグ値は 256 文字までに制限されます。

> [AZURE.NOTE] タグを適用できるのは、リソース マネージャーの操作をサポートするリソースのみです。仮想マシン、仮想ネットワーク、またはストレージをクラシック デプロイ モデル (クラシック ポータルなど) を使用して作成した場合、そのリソースにタグを適用することはできません。タグ付けをサポートするには、Resource Manager を介してこれらのリソースを再デプロイします。その他のすべてのリソースでは、タグ付けがサポートされています。

## テンプレート

デプロイ中にリソースにタグを付けるには、デプロイしているリソースに**タグ**要素を追加し、タグの名前と値を指定するだけで済みます。タグの名前と値は、サブスクリプションに事前に存在する必要はありません。各リソースに対して、最大で 15 個のタグを指定できます。

次の例では、タグを使用したストレージ アカウントを示します。

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

現時点で、リソース マネージャーはタグの名前と値のオブジェクトの処理をサポートしません。代わりに、タグの値のオブジェクトを渡しますが、次の例のようにタグ名を指定する必要があります。

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## ポータル

[AZURE.INCLUDE [resource-manager-tag-resource](../includes/resource-manager-tag-resources.md)]

## PowerShell

[AZURE.INCLUDE [resource-manager-tag-resources](../includes/resource-manager-tag-resources-powershell.md)]

## Azure CLI

タグはリソースやリソース グループ上に直接存在します。既存のタグを表示するには、**azure group show** を使用してリソース グループとそのリソースを取得します。

    azure group show -n tag-demo-group
    
これにより、リソース グループに関するメタデータが、適用されているすべてのタグと共に返されます。
    
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

リソース グループのみのタグを取得するには、[jq](http://stedolan.github.io/jq/download/) などの JSON ユーティリティを使用します。

    azure group show -n tag-demo-group --json | jq ".tags"
    
これにより、そのリソース グループのタグが返されます。
    
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

特定のリソースのタグを表示するには、**azure resource show** を使用します。

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    
これにより、そのリソースのタグが返されます。
    
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
次の例は、タグ名と値があるすべてのリソースを取得する方法を示します。

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    
これにより、そのタグが指定されたリソースの名前が返されます。
    
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

タグは全体として更新されます。既存のタグがあるリソースにタグを 1 つ追加するには、残しておきたい既存のタグをすべて取得します。リソース グループのタグの値を設定するには、**azure group set** を使用して、リソース グループのすべてのタグを指定します。

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    
リソース グループの概要が、新しいタグと共に返されます。
    
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
サブスクリプションの既存のタグを一覧表示するには **azure tag list** を使用します。また、タグを追加するには **azure tag create** を使用します。サブスクリプションの分類からタグを削除するには、まず任意のリソースからタグを削除します。次に、**azure tag delete** を使用してタグを削除します。

## REST API

ポータルと PowerShell のどちらも、バックグラウンドで[リソース マネージャーの REST AP](https://msdn.microsoft.com/library/azure/dn848368.aspx) を使用します。別の環境にタグ付けを統合する必要がある場合、リソース ID に対する GET 操作でタグを取得し、PATCH 呼び出しでタグのセットを更新できます。


## タグと課金

サービスがサポートされていれば、タグを使用して課金データをグループ化できます。たとえば、 [Azure リソース マネージャーに統合された Virtual Machines](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) を使用すると、仮想マシンの課金データを整理するためのタグを定義および適用できます。異なる組織向けに複数の VM を実行している場合は、タグを使用すると、コスト センターごとに課金データをグループ化できます。また、タグを使用すると、運用環境で実行されている VM の課金データなどの、ランタイム環境ごとにコストを分類することもできます。

タグに関する情報は、[Azure Resource Usage API と RateCard API](billing-usage-rate-card-overview.md) から、あるいはコンマ区切り値 (CSV) ファイルから取得できます。使用状況ファイルは [Azure アカウント ポータル](https://account.windowsazure.com/)または [EA ポータル](https://ea.azure.com)からダウンロードします。課金情報へのプログラムによるアクセスの詳細については、「[Microsoft Azure リソースの消費を把握する](billing-usage-rate-card-overview.md)」を参照してください。REST API の操作については、「[Azure Billing REST API Reference (Azure Billing REST API リファレンス)](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)」を参照してください。

課金のタグがサポートされているサービスの使用状況 CSV ファイルをダウンロードした場合、タグは「**Tags**」列に表示されます。詳細については、[Understand your bill for Microsoft Azure (Microsoft Azure の課金について)](billing-understand-your-bill.md) を参照してください。

![課金タグを参照してください](./media/resource-group-using-tags/billing_csv.png)

## 次のステップ

- カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。定義するポリシーには、すべてのリソースが特定のタグに値が指定されていることが必要になる場合があります。詳細については、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。
- リソースのデプロイ時に Azure PowerShell を使用する方法の概要については、「[Azure リソース マネージャーでの Windows PowerShell の使用](./powershell-azure-resource-manager.md)」をご覧ください。
- リソースのデプロイ時に Azure CLI を使用する方法の概要については、「[Azure リソース管理での Mac、Linux、および Windows 用 Azure CLI の使用](./xplat-cli-azure-resource-manager.md)」をご覧ください。
- ポータルの使用方法の概要については、「[Azure ポータルを使用した Azure リソースの管理](./azure-portal/resource-group-portal.md)」をご覧ください。

<!---HONumber=AcomDC_0817_2016-->