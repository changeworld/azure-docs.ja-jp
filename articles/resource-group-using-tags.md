<properties
	pageTitle="タグを使用した Azure リソースの整理"
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
	ms.date="10/14/2015"
	ms.author="tomfitz"/>


# タグを使用した Azure リソースの整理

リソース マネージャーを使用すると、タグを適用してリソースを理論的に整理できます。タグは、定義したプロパティによりリソースを識別するキーと値のペアで構成されます。同じカテゴリに属すリソースとしてマークするには、これらのリソースに同じタグを適用します。

特定のタグを持つリソースを表示した場合、すべてのリソース グループからリソースが表示されます。同じリソース グループ内のリソースに限定されないため、デプロイメント関係にとらわれずにリソースを整理できます。タグは、課金または管理の目的でリソースを整理する必要がある場合に特に役立ちます。

リソースまたはリソース グループに追加する各タグは、サブスクリプション全体の分類に自動的に追加されます。タグの名前でサブスクリプションの分類を事前設定し、リソースとして使用する値を後でタグ付けすることもできます。

> [AZURE.NOTE]タグを適用できるのは、リソース マネージャーの操作をサポートするリソースのみです。仮想マシン、Virtual Network、またはストレージをクラシック デプロイ モデル (Azure ポータル、[Service Management API](../services/api-management/) など) を通して作成した場合、リソースにタグを適用することはできません。タグ付けが必要な場合は、リソース マネージャーを介してこれらのリソースを再デプロイする必要があります。その他のすべてのリソースでは、タグ付けがサポートされています。


## プレビュー ポータルでのタグ

プレビュー ポータルでリソースやリソース グループをタグ付けするのは簡単です。[参照] ハブを使用してタグを付けるリソースまたはリソース グループに移動し、ブレードの上部にある [概要] セクションの [タグ] 部分をクリックします。

![リソース ブレードとリソース グループのブレードの [タグ] 部分](./media/resource-group-using-tags/tag-icon.png)

既に適用されているタグの一覧のブレードが開きます。これが最初のタグの場合、一覧は空になっています。タグを追加するには、名前と値を指定し、Enter キーを押すだけです。いくつかのタグを追加すると、既存のタグの名前と値に基づくオートコンプリート オプションが機能します。これにより、リソース間で一貫性のある分類を適用でき、スペル ミスのような一般的なミスを回避することができます。

![名前と値のペアを持つタグ リソース](./media/resource-group-using-tags/tag-resources.png)

ポータル内のタグの分類を表示するには、[参照] ハブを使用してすべての項目を表示し、[タグ] を選択します。

![参照ハブによるタグの検索](./media/resource-group-using-tags/browse-tags.png)

最も重要なタグをスタート画面にピン留めしておくとすぐに利用できるため便利です。どうぞお楽しみください。

![スタート画面にタグをピン留めする](./media/resource-group-using-tags/pin-tags.png)

## PowerShell を使用したタグ付け

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

タグはリソースおよびリソース グループに直接適用されます。そのため、既に適用されているタグを調べるには、**Get-AzureRmResource** または **Get-AzureRmResourceGroup**.を使用してリソースまたはリソース グループを取得します。それでは、リソース グループから始めましょう。

    PS C:\> Get-AzureRmResourceGroup tag-demo

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    Resources         :
                    Name                             Type                                  Location
                    ===============================  ====================================  ==============
                    CPUHigh ExamplePlan              microsoft.insights/alertrules         eastus
                    ForbiddenRequests tag-demo-site  microsoft.insights/alertrules         eastus
                    LongHttpQueue ExamplePlan        microsoft.insights/alertrules         eastus
                    ServerErrors tag-demo-site       microsoft.insights/alertrules         eastus
                    ExamplePlan-tag-demo             microsoft.insights/autoscalesettings  eastus
                    tag-demo-site                    microsoft.insights/components         centralus
                    ExamplePlan                      Microsoft.Web/serverFarms             southcentralus
                    tag-demo-site                    Microsoft.Web/sites                   southcentralus


このコマンドレットは、リソース グループに関して、適用されているタグを含むいくつかのメタデータを返します。リソース グループにタグを付けるには、**Set-AzureRmResourceGroup** コマンドを使用して、タグ名と値を指定するだけです。

    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag @( @{ Name="project"; Value="tags" }, @{ Name="env"; Value="demo"} )

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  =====
                    project  tags
                    env      demo

タグは全体として更新されるため、既にタグが付けられているリソースにタグをもう 1 つ追加する場合は、保持しておきたいすべてのタグを含む配列を使用する必要があります。これを行うために、まず既存のタグを選択して、新しいタグを 1 つ追加することができます。

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag $tags

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  ========
                    project  tags
                    env      demo
                    status   approved


特定のタグを 1 つ以上削除するには、削除するタグが含まれない配列を保存します。

プロセスはリソースの場合も同じですが、**Get-AzureRmResource** および **Set-AzureRmResource** コマンドレットを使用する点のみ異なります。

特定のタグが付けられたリソース グループを取得するには、**Find-AzureRmResourceGroup** コマンドレットに **-Tag** パラメーターを指定して使用します。

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo

Azure PowerShell 1.0 プレビューよりも前のバージョンで特定のタグが付けられたリソースを取得するには、次のコマンドを使用します。

    PS C:\> Get-AzureResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo
    PS C:\> Get-AzureResource -Tag @{ Name="env"; Value="demo" } | %{ $_.Name }
    rbacdemo-web
    rbacdemo-docdb
    ...    

PowerShell を使用してサブスクリプション内のすべてのタグの一覧を取得するには、**Get-AzureRmTag** コマンドレットを使用します。

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

"hidden-" や "link:" で始まるタグが表示される場合があります。これらは内部タグです。これらのタグは単に無視して、変更しないでください。

分類に新しいタグを追加するには、**New-AzureRmTag** コマンドレットを使用します。これらのタグは、リソースまたはリソース グループにまだ適用されていない場合でもオートコンプリートに含められます。タグ名/値を削除するには、タグが使用されている任意のリソースからタグを削除した後、**Remove-AzureRmTag** コマンドレットを使用して分類から削除します。

## REST API を使用したタグ付け

ポータルと PowerShell のどちらも、バックグラウンドで[リソース マネージャーの REST AP](http://msdn.microsoft.com/library/azure/dn790568.aspx) を使用します。別の環境にタグ付けを統合する必要がある場合、リソース ID に対する GET 操作でタグを取得し、PATCH 呼び出しでタグのセットを更新できます。


## タグ付けと課金

サービスがサポートされていれば、タグを使用して課金データをグループ化できます。たとえば、 [Azure リソース マネージャーに統合された Virtual Machines](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) を使用すると、仮想マシンの課金データを整理するためのタグを定義および適用できます。異なる組織向けに複数の VM を実行している場合は、タグを使用すると、コスト センターごとに課金データをグループ化できます。また、タグを使用すると、運用環境で実行されている VM の課金データなどの、ランタイム環境ごとにコストを分類することもできます。

タグに関する情報は、[Azure Resource Usage API と RateCard API](billing-usage-rate-card-overview.md) から、あるいは [Azure アカウント ポータル](https://account.windowsazure.com/)または [EA ポータル](https://ea.azure.com)からダウンロードできる使用状況のコンマ区切り値 (CSV) ファイルから取得できます。課金情報へのプログラムによるアクセスの詳細については、「[Microsoft Azure リソースの消費を把握する](billing-usage-rate-card-overview.md)」を参照してください。REST API の操作については、「[Azure Billing REST API Reference (Azure Billing REST API リファレンス)](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)」を参照してください。

課金のタグがサポートされているサービスの使用状況 CSV ファイルをダウンロードした場合、タグは「**Tags**」列に表示されます。詳細については、[Understand your bill for Microsoft Azure (Microsoft Azure の課金について)](billing-understand-your-bill.md) を参照してください。

![課金タグを参照してください](./media/resource-group-using-tags/billing_csv.png)

## 次のステップ

- リソースのデプロイ時に Azure PowerShell を使用する方法の概要については、「[Azure リソース マネージャーでの Windows PowerShell の使用](./powershell-azure-resource-manager.md)」をご覧ください。
- リソースのデプロイ時に Azure CLI を使用する方法の概要については、「[Azure リソース管理での Mac、Linux、および Windows 用 Azure CLI の使用](./xplat-cli-azure-resource-manager.md)」をご覧ください。
- プレビュー ポータルの使用方法の概要については、「[Azure プレビュー ポータルを使用した Azure リソースの管理](./resource-group-portal.md)」をご覧ください。  

<!---HONumber=Oct15_HO3-->