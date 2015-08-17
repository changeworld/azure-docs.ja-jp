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
	ms.date="08/05/2015" 
	ms.author="tomfitz"/>


# タグを使用した Azure リソースの整理

リソース マネージャーを使用すると、タグを適用してリソースを理論的に整理できます。タグは、定義したプロパティによりリソースを識別するキーと値のペアで構成されます。同じカテゴリに属すリソースとしてマークするには、これらのリソースに同じタグを適用します。

特定のタグを持つリソースを表示した場合、すべてのリソース グループからリソースが表示されます。同じリソース グループ内のリソースに限定されないため、デプロイメント関係にとらわれずにリソースを整理できます。タグは、課金または管理の目的でリソースを整理する必要がある場合に特に役立ちます。

> [AZURE.NOTE]タグを適用できるのは、リソース マネージャーの操作をサポートするリソースのみです。仮想マシン、Virtual Network、または記憶域を従来のデプロイ モデル (Azure ポータル、[Service Management API](https://msdn.microsoft.com/library/azure/dn948465.aspx) など) を通じて作成した場合、リソースにタグを適用することはできません。タグ付けが必要な場合は、リソース マネージャーを介してこれらのリソースを再デプロイする必要があります。その他のすべてのリソースでは、タグ付けがサポートされています。

## プレビュー ポータルでのタグ

プレビュー ポータルでリソースやリソース グループをタグ付けするのは簡単です。[参照] ハブを使用してタグを付けるリソースまたはリソース グループに移動し、ブレードの上部にある [概要] セクションの [タグ] 部分をクリックします。

![リソース ブレードとリソース グループのブレードの [タグ] 部分](./media/resource-group-using-tags/rgblade.png)

既に適用されているタグの一覧のブレードが開きます。これが最初のタグの場合、一覧は空になっています。タグを追加するには、名前と値を指定し、Enter キーを押すだけです。いくつかのタグを追加すると、既存のタグの名前と値に基づくオートコンプリート オプションが機能します。これにより、リソース間で一貫性のある分類を適用でき、スペル ミスのような一般的なミスを回避することができます。

![名前と値のペアを持つタグ リソース](./media/resource-group-using-tags/tag-resources.png)

ここで、それぞれのタグをクリックして、同じタグを持つすべてのリソースの一覧を表示できます。これが最初のタグの場合、当然ですがその一覧はあまり興味深いものにはなりません。次に、PowerShell を使ってすべてのリソースにすばやくタグを付ける方法について説明します。


## PowerShell を使用したタグ付け

最初に、[最新の Azure PowerShell モジュール](./install-configure-powershell.md)を入手します。Azure PowerShell モジュールを初めて使用する場合は、参考として[このドキュメント](./install-configure-powershell.md)をお読みください。この記事では、既にアカウントを追加し、タグを付けるリソースを含むサブスクリプションを選択しているものと想定しています。

タグ付けは、[リソース マネージャー](http://msdn.microsoft.com/library/azure/dn790568.aspx)で使用可能なリソースおよびリソース グループに対してのみ行うことができます。そこで、次の作業として、リソース マネージャーを使用するための操作を行います。詳細については、[リソース マネージャーでの Windows PowerShell の使用](powershell-azure-resource-manager.md)をご覧ください。

    Switch-AzureMode AzureResourceManager

タグはリソースおよびリソース グループに直接適用されます。そのため、既に適用されているタグを調べるには、それぞれ `Get-AzureResource` または `Get-AzureResourceGroup` を使用してリソースまたはリソース グループを取得します。それでは、リソース グループから始めましょう。

![PowerShell で Get-AzureResourceGroup を使用したタグの取得](./media/resource-group-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

このコマンドレットは、リソース グループに関して、適用されているタグを含むいくつかのメタデータを返します。リソース グループにタグを付けるには、`Set-AzureResourceGroup` を使用して、タグ名と値を指定するだけです。

![PowerShell で Get-AzureResourceGroup を使用したタグの設定](./media/resource-group-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

タグは全体として更新されるため、既にタグが付けられているリソースにタグをもう 1 つ追加する場合は、保持しておきたいすべてのタグを含む配列を使用する必要があります。特定のタグを削除するには、削除するタグが含まれない配列を保存します。

リソースの場合も手順は同じですが、`Get-AzureResource` コマンドレットと `Set-AzureResource` コマンドレットを使う点が異なります。特定のタグが付けられたリソースまたはリソース グループを取得するには、`Get-AzureResource` コマンドレットまたは `Get-AzureResourceGroup` コマンドレットに `-Tag` パラメーターを使用します。

![PowerShell で Get-AzureResource と Get-AzureResourceGroup を使用したタグ付きリソースとリソース グループの取得](./media/resource-group-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## REST API を使用したタグ付け

ポータルと PowerShell のどちらも、バックグラウンドで[リソース マネージャーの REST AP](http://msdn.microsoft.com/library/azure/dn790568.aspx) を使用します。別の環境にタグ付けを統合する必要がある場合、リソース ID に対する GET 操作でタグを取得し、PATCH 呼び出しでタグのセットを更新できます。


## 分類の管理

前半の部分で、一貫性を確保しミスを回避するうえでオートコンプリート機能が有用であることに触れました。オートコンプリート機能では、サブスクリプションに設定された使用可能なタグの分類に基づいて値が設定されます。リソースまたはリソース グループにタグを追加すると、タグはサブスクリプション全体の分類に自動的に追加されます。ただし、目的の名前と値を含む分類を事前に設定して、後でリソースをタグ付けするときにそれらが使用されるようにすることもできます。

PowerShell を使用してサブスクリプション内のすべてのタグの一覧を取得するには、`Get-AzureTag` コマンドレットを使用します。

![PowerShell での Get-AzureTag](./media/resource-group-using-tags/Get-AzureTag-in-PowerShell.png)


"hidden-" や "link:" で始まるタグが表示される場合があります。これらは内部タグです。これらのタグは単に無視して、変更しないでください。

分類に新しいタグを追加するには、`New-AzureTag` コマンドレットを使用します。これらのタグは、リソースまたはリソース グループにまだ適用されていない場合でもオートコンプリートに含められます。タグ名/値を削除するには、タグが使用されている任意のリソースからタグを削除した後、`Remove-AzureTag` コマンドレットを使用して分類から削除します。

ポータル内のタグの分類を表示するには、[参照] ハブを使用してすべての項目を表示し、[タグ] を選択します。

![参照ハブによるタグの検索](./media/resource-group-using-tags/browse-tags.png)

最も重要なタグをスタート画面にピン留めしておくとすぐに利用できるため便利です。どうぞお楽しみください。

![スタート画面にタグをピン留めする](./media/resource-group-using-tags/pin-tags.png)

## タグ付けと課金

サービスがサポートされていれば、タグを使用して課金データをグループ化できます。たとえば、 [Azure リソース マネージャーに統合された Virtual Machines](/virtual-machines/virtual-machines-azurerm-versus-azuresm.md) を使用すると、仮想マシンの課金データを整理するためのタグを定義および適用できます。異なる組織向けに複数の VM を実行している場合は、タグを使用すると、コスト センターごとに課金データをグループ化できます。また、タグを使用すると、実稼働環境で実行されている VM の課金データなどの、ランタイム環境ごとにコストを分類することもできます。

タグに関する情報は、[使用状況 API](billing-usage-rate-card-overview.md) から、 あるいは [Microsoft Azure へようこそ](https://account.windowsazure.com/)または [Microsoft サインイン](https://ea.azure.com)からダウンロードできる使用状況のコンマ区切り値 (CSV) ファイルから取得できます。

課金のタグがサポートされているサービスの使用状況 CSV ファイルをダウンロードした場合、タグは「**Tags**」列に表示されます。詳細については、[Understand your bill for Microsoft Azure (Microsoft Azure の課金について)](billing-understand-your-bill.md) を参照してください。

![課金タグを参照してください](./media/resource-group-using-tags/billing_csv.png)

## 次のステップ

- リソースのデプロイ時に Azure PowerShell を使用する方法の概要については、「[Azure リソース マネージャーでの Windows PowerShell の使用](./powershell-azure-resource-manager.md)」を参照してください。
- リソースのデプロイ時に Azure CLI を使用する方法の概要については、「[Azure リソース管理での Mac、Linux、および Windows 用 Azure CLI の使用](./xplat-cli-azure-resource-manager.md)」を参照してください。
- プレビュー ポータルの使用方法の概要については、「[Azure プレビュー ポータルを使用した Azure リソースの管理](./resource-group-portal.md)」を参照してください。  
  

  

<!---HONumber=August15_HO6-->