<properties
	pageTitle="タグを使用した Azure リソースの整理 | Microsoft Azure"
	description="タグを適用して、課金や管理のためにリソースを整理する方法を示します。"
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="tomfitz"/>


# タグを使用した Azure リソースの整理

リソース マネージャーを使用すると、タグを適用してリソースを理論的に整理できます。タグは、定義したプロパティによりリソースを識別するキーと値のペアで構成されます。同じカテゴリに属すリソースとしてマークするには、これらのリソースに同じタグを適用します。

特定のタグを持つリソースを表示した場合、すべてのリソース グループからリソースが表示されます。同じリソース グループ内のリソースに限定されないため、デプロイ関係にとらわれずにリソースを整理できます。タグは、課金または管理の目的でリソースを整理する必要がある場合に特に役立ちます。

リソースまたはリソース グループに追加する各タグは、サブスクリプション全体の分類に自動的に追加されます。タグの名前でサブスクリプションの分類を事前設定し、リソースとして使用する値を後でタグ付けすることもできます。

各リソースまたはリソース グループには、最大で 15 個のタグを含めることができます。タグ名は 512 文字まで、タグ値は 256 文字までに制限されます。

> [AZURE.NOTE] タグを適用できるのは、リソース マネージャーの操作をサポートするリソースのみです。仮想マシン、仮想ネットワーク、またはストレージをクラシック デプロイ モデル (クラシック ポータルや Service Management API など) を使用して作成した場合、そのリソースにタグを適用することはできません。タグ付けが必要な場合は、リソース マネージャーを介してこれらのリソースを再デプロイする必要があります。その他のすべてのリソースでは、タグ付けがサポートされています。

## テンプレート内のタグ

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

現時点で、リソース マネージャーはタグの名前と値のオブジェクトの処理をサポートしません。代わりに、タグの値のオブジェクトを渡すことはできますが、次のように、タグ名を指定する必要があります。

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


## ポータルでのタグ

ポータルで既存のリソースやリソース グループにタグを付けることができます。[参照] ハブを使用してタグを付けるリソースまたはリソース グループに移動し、ブレードの上部にある [概要] セクションの [タグ] 部分をクリックします。

![リソース ブレードとリソース グループのブレードの [タグ] 部分](./media/resource-group-using-tags/tag-icon.png)

既に適用されているタグの一覧のブレードが開きます。これが最初のタグの場合、一覧は空になっています。タグを追加するには、名前と値を指定し、Enter キーを押すだけです。いくつかのタグを追加すると、既存のタグの名前と値に基づくオートコンプリート オプションが機能します。これにより、リソース間で一貫性のある分類を適用でき、スペル ミスのような一般的なミスを回避することができます。

![名前と値のペアを持つタグ リソース](./media/resource-group-using-tags/tag-resources.png)

ポータル内のタグの分類を表示するには、[参照] ハブを使用してすべての項目を表示し、[タグ] を選択します。

![参照ハブによるタグの検索](./media/resource-group-using-tags/browse-tags.png)

最も重要なタグをスタート画面にピン留めしておくとすぐに利用できるため便利です。どうぞお楽しみください。

![スタート画面にタグをピン留めする](./media/resource-group-using-tags/pin-tags.png)

## タグと PowerShell

タグはリソースおよびリソース グループに直接適用されます。そのため、既に適用されているタグを調べるには、**Get-AzureRmResource** または **Get-AzureRmResourceGroup**.を使用してリソースまたはリソース グループを取得します。それでは、リソース グループから始めましょう。

    PS C:\> Get-AzureRmResourceGroup -Name tag-demo-group

    ResourceGroupName : tag-demo-group
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production


このコマンドレットは、リソース グループに関して、適用されているタグを含むいくつかのメタデータを返します。

リソースのメタデータを取得する際、タグは直接的には表示されません。タグは、下記のように、Hashtable オブジェクトとしてのみ表示されます。

    PS C:\> Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

実際のタグを表示するには、**Tags** プロパティを取得します。

    PS C:\> (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
    Dept: Finance
    Environment: Production

通常は、特定のリソース グループまたはリソースのタグを表示する代わりに、特定のタグと値を持つリソースまたはリソース グループをすべて取得します。特定のタグが付けられたリソース グループを取得するには、**Find-AzureRmResourceGroup** コマンドレットに **-Tag** パラメーターを指定して使用します。

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    tag-demo-group
    web-demo-group

特定のタグと値を持つすべてのリソースを取得するには、**Find-AzureRmResource** コマンドレットを使用します。

    PS C:\> Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }
    tfsqlserver
    tfsqldatabase

既存のタグがないリソース グループにタグを追加するには、**Set-AzureRmResourceGroup** コマンドを使用し、タグ オブジェクトを指定します。

    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
既存のタグがないリソースにタグを追加するには、**SetAzureRmResource** コマンドを使用します。

    PS C:\> Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

タグは全体として更新されるため、既にタグが付けられているリソースにタグをもう 1 つ追加する場合は、保持しておきたいすべてのタグを含む配列を使用する必要があります。これを行うには、まず既存のタグを選択し、そのセットに新しいタグを追加して、すべてのタグを再適用します。

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag $tags

特定のタグを 1 つ以上削除するには、削除するタグが含まれない配列を保存します。

プロセスはリソースの場合も同じですが、**Get-AzureRmResource** および **Set-AzureRmResource** コマンドレットを使用する点のみ異なります。

PowerShell を使用してサブスクリプション内のすべてのタグの一覧を取得するには、**Get-AzureRmTag** コマンドレットを使用します。

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

"hidden-" や "link:" で始まるタグが表示される場合があります。これらは内部タグです。これらのタグは単に無視して、変更しないでください。

分類に新しいタグを追加するには、**New-AzureRmTag** コマンドレットを使用します。これらのタグは、リソースまたはリソース グループにまだ適用されていない場合でもオートコンプリートに含められます。タグ名/値を削除するには、タグが使用されている任意のリソースからタグを削除した後、**Remove-AzureRmTag** コマンドレットを使用して分類から削除します。

## タグと Azure CLI

タグはリソースおよびリソース グループに直接適用されます。そのため、既に適用されているタグを調べるには **azure group show** を使用してリソース グループとそのリソースを取得します。

    azure group show -n tag-demo-group
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
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

特定のリソースのタグを表示するには、**azure resource show** を使用します。

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
特定のタグと値を持つすべてのリソースを取得するには、次のコマンドレットを使用します。

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

タグは全体として更新されるため、既にタグが付けられているリソースにタグをもう 1 つ追加する場合は、保持しておきたい既存のタグをすべて取得する必要があります。リソース グループのタグの値を設定するには、**azure group set** を使用して、リソース グループのすべてのタグを指定します。

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
サブスクリプションの既存のタグを一覧表示するには **azure tag list** を使用し、新しいタグを追加するには **azure tag create** を使用します。サブスクリプションの分類からタグを削除する場合は、対象のタグが使用されているリソースがあれば、そのリソースからタグを削除してから、**azure tag delete** を使用してタグを削除します。

## タグと REST API

ポータルと PowerShell のどちらも、バックグラウンドで[リソース マネージャーの REST AP](https://msdn.microsoft.com/library/azure/dn848368.aspx) を使用します。別の環境にタグ付けを統合する必要がある場合、リソース ID に対する GET 操作でタグを取得し、PATCH 呼び出しでタグのセットを更新できます。


## タグと課金

サービスがサポートされていれば、タグを使用して課金データをグループ化できます。たとえば、 [Azure リソース マネージャーに統合された Virtual Machines](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md) を使用すると、仮想マシンの課金データを整理するためのタグを定義および適用できます。異なる組織向けに複数の VM を実行している場合は、タグを使用すると、コスト センターごとに課金データをグループ化できます。また、タグを使用すると、運用環境で実行されている VM の課金データなどの、ランタイム環境ごとにコストを分類することもできます。

タグに関する情報は、[Azure Resource Usage API と RateCard API](billing-usage-rate-card-overview.md) から、あるいは [Azure アカウント ポータル](https://account.windowsazure.com/)または [EA ポータル](https://ea.azure.com)からダウンロードできる使用状況のコンマ区切り値 (CSV) ファイルから取得できます。課金情報へのプログラムによるアクセスの詳細については、「[Microsoft Azure リソースの消費を把握する](billing-usage-rate-card-overview.md)」を参照してください。REST API の操作については、「[Azure Billing REST API Reference (Azure Billing REST API リファレンス)](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)」を参照してください。

課金のタグがサポートされているサービスの使用状況 CSV ファイルをダウンロードした場合、タグは「**Tags**」列に表示されます。詳細については、[Understand your bill for Microsoft Azure (Microsoft Azure の課金について)](billing-understand-your-bill.md) を参照してください。

![課金タグを参照してください](./media/resource-group-using-tags/billing_csv.png)

## 次のステップ

- カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。定義するポリシーには、場合によっては、すべてのリソースに特定のタグを設定することが必要になります。詳細については、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。
- リソースのデプロイ時に Azure PowerShell を使用する方法の概要については、「[Azure リソース マネージャーでの Windows PowerShell の使用](./powershell-azure-resource-manager.md)」をご覧ください。
- リソースのデプロイ時に Azure CLI を使用する方法の概要については、「[Azure リソース管理での Mac、Linux、および Windows 用 Azure CLI の使用](./xplat-cli-azure-resource-manager.md)」をご覧ください。
- ポータルの使用方法の概要については、「[Azure ポータルを使用した Azure リソースの管理](./azure-portal/resource-group-portal.md)」をご覧ください。  

<!---HONumber=AcomDC_0309_2016-->