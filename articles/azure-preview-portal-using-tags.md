<properties urlDisplayName="" pageTitle="タグを使用した Azure リソースの整理" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Using tags to organize your Azure resources" authors="Michael Flanakin" solutions="" writer="" manager="carolz" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="10/08/2014" ms.author="micflan" />


# タグを使用した Azure リソースの整理

Azure プレビュー ポータルとその基になるリソース マネージャーを使うと、リソースを整理したり、ユーザーに合わせて操作方法をカスタマイズしたりできます。 

完全な Azure ポータルでは、サブスクリプションがリソースを分類およびグループ化する唯一の方法になります。プレビュー ポータルでは、関連するエンティティをグループ化する方法として、[リソース グループが採用されていました](http://azure.microsoft.com/ja-jp/documentation/articles/azure-preview-portal-using-resource-groups)。この機能は、[ロールベースのアクセスを導入](http://azure.microsoft.com/ja-jp/documentation/articles/role-based-access-control-configure)した際に、より価値が高まりました。現在は、同じ目的で、キーと値のペアを使用してリソースをタグ付けすることにより、リソースをさらに分類し、異なるリソース グループ間、およびポータル内の異なるサブスクリプション間にわたってリソースを表示することができます。

チーム、プロジェクト、または環境に基づいてリソースをグループ化すれば、必要なときに表示したい情報だけにフォーカスすることができます。 


## Azure プレビュー ポータルにおけるタグ

プレビュー ポータルでリソースやリソース グループをタグ付けするのは簡単です。[参照] ハブを使用してタグを付けるリソースまたはリソース グループに移動し、ブレードの上部にある [概要] セクションの [タグ] 部分をクリックします。 

![Tags part on resource and resource group blades](./media/azure-preview-portal-using-tags/rgblade.png)

既に適用されているタグの一覧のブレードが開きます。これが最初のタグの場合、一覧は空になっています。タグを追加するには、名前と値を指定し、Enter キーを押すだけです。いくつかのタグを追加すると、既存のタグの名前と値に基づくオートコンプリート オプションが機能します。これにより、リソース間で一貫性のある分類を適用でき、スペル ミスのような一般的なミスを回避することができます。

![Tag resources with name/value pairs](./media/azure-preview-portal-using-tags/tag-resources.png)

ここで、それぞれのタグをクリックして、同じタグを持つすべてのリソースの一覧を表示できます。これが最初のタグの場合、当然ですがその一覧はあまり興味深いものにはなりません。次に、PowerShell を使ってすべてのリソースにすばやくタグを付ける方法について説明します。


## PowerShell を使用したタグ付け

最初に、最新の [Azure PowerShell モジュール](http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell/)を入手します。Azure PowerShell モジュールを初めて使用する場合は、参考として[このドキュメントをお読みください](http://azure.microsoft.com/ja-jp/documentation/articles/install-configure-powershell)。この記事では、既にアカウントを追加し、タグを付けるリソースを含むサブスクリプションを選択しているものと想定しています。

タグ付けは、[リソース マネージャー](http://msdn.microsoft.com/ja-jp/library/azure/dn790568.aspx)で使用可能なリソースおよびリソース グループに対してのみ行うことができます。そこで、次の作業として、リソース マネージャーを使用するための操作を行います。詳細については、「[Using Windows PowerShell with Resource Manager (リソース マネージャーでの Windows PowerShell の使用)](http://azure.microsoft.com/ja-jp/documentation/articles/powershell-azure-resource-manager/)」を参照してください。

  Switch-AzureMode AzureResourceManager

タグはリソースおよびリソース グループに直接適用されます。そのため、既に適用されているタグを調べるには、それぞれ `Get-AzureResource` または `Get-AzureResourceGroup` を使用してリソースまたはリソース グループを取得します。それでは、リソース グループから始めましょう。

![Getting tags with Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-in-PowerShell.png)

このコマンドレットは、リソース グループに関して、適用されているタグを含むいくつかのメタデータを返します。リソース グループにタグを付けるには、`Set-AzureResourceGroup` を使用して、タグ名と値を指定するだけです。

![Setting tags with Set-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Set-AzureResourceGroup-in-PowerShell.png)

タグは全体として更新されるため、既にタグが付けられているリソースにタグをもう 1 つ追加する場合は、保持しておきたいすべてのタグを含む配列を保存して使用する必要があります。特定のタグを削除するには、削除するタグが含まれない配列を保存します。 

リソースの場合も手順は同じですが、`Get-AzureResource` コマンドレットと `Set-AzureResource` コマンドレットを使う点が異なります。特定のタグが付けられたリソースまたはリソース グループを取得するには、`Get-AzureResource` コマンドレットまたは `Get-AzureResourceGroup` コマンドレットに `-Tag` パラメーターを使用します。

![Getting tagged resources and resource groups with Get-AzureResource and Get-AzureResourceGroup in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureResourceGroup-with-tags-in-PowerShell.png)


## リソース マネージャーを使用したタグ付け

プレビュー ポータルと PowerShell のどちらも、バックグラウンドで[リソース マネージャーの REST API](http://msdn.microsoft.com/ja-jp/library/azure/dn790568.aspx) を使用します。別の環境にタグ付けを統合する必要がある場合、リソース ID に対する GET 操作でタグを取得し、PATCH 呼び出しでタグのセットを更新できます。


## 分類の管理

前半の部分で、一貫性を確保しミスを回避するうえでオートコンプリート機能が有用であることに触れました。オートコンプリート機能では、サブスクリプションに設定された使用可能なタグの分類に基づいて値が設定されます。リソースまたはリソース グループにタグを追加すると、タグはサブスクリプション全体の分類に自動的に追加されます。ただし、目的の名前と値を含む分類を事前に設定して、後でリソースをタグ付けするときにそれらが使用されるようにすることもできます。

PowerShell を使用してサブスクリプション内のすべてのタグの一覧を取得するには、`Get-AzureTag` コマンドレットを使用します。

![Get-AzureTag in PowerShell](./media/azure-preview-portal-using-tags/Get-AzureTag-in-PowerShell.png)


"hidden-" や "link:" で始まるタグが表示される場合があります。これらは内部タグです。これらのタグは単に無視して、変更しないでください。 

分類に新しいタグを追加するには、`New-AzureTag` コマンドレットを使用します。これらのタグは、リソースまたはリソース グループにまだ適用されていない場合でもオートコンプリートに含められます。タグ名/値を削除するには、タグが使用されている任意のリソースからタグを削除した後、`Remove-AzureTag` コマンドレットを使用して分類から削除します。

ポータル内のタグの分類を表示するには、[参照] ハブを使用してすべての項目を表示し、[タグ] を選択します。

![Find tags via the Browse hub](./media/azure-preview-portal-using-tags/browse-tags.png)

最も重要なタグをスタートボードにピン留めしておくとすぐに利用できるため便利です。どうぞお楽しみください。

![Pin tags to the Startboard](./media/azure-preview-portal-using-tags/pin-tags.png)

