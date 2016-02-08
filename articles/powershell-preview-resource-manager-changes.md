<properties
	pageTitle="Azure PowerShell 1.0 Preview リソース マネージャーに対する変更 | Microsoft Azure"
	description="Azure PowerShell 1.0 Preview で追加されたリソース マネージャーのコマンドレットにおける変更点を説明します。"
	services="azure-resource-manager"
	documentationCenter="na"
	authors="ravbhatnagar"
	manager="ryjones"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="powershell"
	ms.workload="na"
	ms.date="01/26/2016"
	ms.author="gauravbh;tomfitz"/>

#Azure リソース マネージャーの管理 PowerShell コマンドレットに対する変更

Azure PowerShell 1.0 Preview リリースの一環として、管理コマンドレットにいくつかの機能が強化されました。これらの機能強化は、1.0 Preview に含まれるコマンドレット名の変更に追加されるものです。これらの機能強化には大きな変更を伴うものもあるため、既存のスクリプトを停止させる場合があります。これらの変更は、お客様の操作性を向上させることを目的としています。これらの変更についてご意見をお聞かせください。Azure PowerShell 1.0 にお客様からのフィードバックを反映します。新しいコマンドレットをぜひお試しのうえ、フィードバックをお寄せください。

##リソース グループから分離されたテンプレートのデプロイメント

0\.9.8 リリースでは、テンプレートのデプロイメント パラメーターはすべて、リソース グループのコマンドレットにありました。New-AzureResourceGroup では、新しいリソース グループを作成できるだけではなく、デプロイするテンプレートの詳細も提供できます。また、同じテンプレートのデプロイ機能も New-AzureResourceGroupDeployment にありました。1.0 プレビューでは、この機能を切り離しました。New-AzureRMResourceGroup では新しいリソース グループを作成する機能が用意され、New-AzureRmResourceGroupDeployment により、テンプレート デプロイの機能が提供されます。パイプを使用すると、2 つを一緒に使用できます。これにより、コマンドレットを理解および使用しやすくなります。

##統合された監査ログ コマンドレット

監査ログでは、さまざまな範囲でログを取得するための Get-AzureResourceProviderLog、Get-AzureResourceGroupLog、Get-AzureSubscriptionIdLog、Get-AzureResourceLog といった多数のコマンドレットがありました。ログを取得するために、通常、ログ コマンドレットの組み合わせを実行する必要がありました。この操作は最適なものではありませんでした。この機能を 1 つのコマンドレットに統合しました。これは、パラメーターの使用中にさまざまな範囲で呼び出すことができます。適切なパラメーターで Get-AzureRmLog を呼び出して、範囲を指定できるようになりました。

バージョン 0.9.8 では、特定のリソース グループのログを取得するために、次のような操作を実行します。

    Get-AzureResourceGroupLog -ResourceGroup <resource-group-name>

特定のリソースのログを取得するには、次のような操作を実行します。

     Get-AzureResourceLog -ResourceId
     /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>/providers/<provider-namespace>/
     <resource-name>

1\.0 プレビューでは、次のコマンドレットを実行すれば、同じ情報が得られます。リソース グループのログを取得するには、次の操作を実行します。

     Get-AzureRmLog -ResourceGroup <resource-group-name>
     
特定のリソースのログを取得するには、次を実行します。

     Get-AzureRmLog -ResourceId /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>
     /providers/<provider-namespace>/<resource-name>

##リソースおよびリソース グループの Get コマンドレットに対する変更

これらのコマンドレットでリソース プロバイダーにのみ直接クエリが実行されるように、Get-AzureRmResource および Get-AzureRmResourceGroup コマンドレットに変更を加えました。Find-AzureRmResource* という新しいコマンドレットへのキャッシュに対してクエリを実行する機能を分離しました。特定のタグを持つリソース グループを検索する場合、新しい Find-AzureRmResourceGroup コマンドレットを使用できます。この変更により、タグに対してクエリを実行するパラメーターが Get AzureRmResource および Get AzureRmResourceGroup コマンドレットから削除されました。

0\.9.8 には、特定のタグを含むすべてのリソース グループを検索するには、次を実行します。

    Get-AzureResourceGroup -Tag <tag-object>

1\.0 プレビューでは、下のコマンドレットを実行し、上のシナリオを達成します。

    Find-AzureRmResourceGroup -Tag <tag-object>
    
##削除された Test-AzureResource および Test-AzureResourceGroup

これらのコマンドレットは、すべてのシナリオとリソースの種類に対して確実に機能しない場合がありました。マイクロソフトは、この機能について何か優れたソリューションがないかと模索していました。一方で、あてにならないコマンドレットの継続使用を回避したいという希望もありました。このリリースでは、これらのコマンドレットが削除されています。今後のリリースで信頼性の高いソリューションが追加される予定です。

##Get-AzureRmResourceProvider の改良

Get-AzureRmResourceProvider コマンドレットを使用して、リソース プロバイダーの場所情報を取得できるようになりました。これにより、特定のリージョンで利用できるプロバイダーと種類を把握できます。また、このコマンドレットを使用すると、特定のプロバイダーの利用可能な場所の一覧も取得できます。Get-AzureLocation コマンドレットを削除しました。これは、Get-AzureRmResourceProvider コマンドレットですべての場所情報を取得できるようになったためです。

0\.9.8 では、サポートされているすべての場所の一覧を取得するには、次を実行します。

     Get-AzureLocation

プロバイダーの登録状態を取得するには、次を実行します。

     Get-AzureProvider -ListAvailable

1\.0 プレビューでは、下の図のように、コマンドレットを 1 つだけ使用し、上の操作を実行できます。

     Get-AzureRmResourceProvider -Location <location>

上の操作は結果を絞り込み、指定の場所で利用できるプロバイダーと種類のみを表示します。

あるいは、下の図のように、特定のプロバイダーに結果を絞り込むことができます。

     Get-AzureRmResourceProvider -ProviderNamespace <provider-namespace>

上の操作は結果を絞り込み、指定したプロバイダーでのみサポートされている場所を表示します。

##ポリシー コマンドレット

Azure リソース マネージャーにポリシーのサポートが追加されました。本リリースでは、ポリシーを管理するための PowerShell コマンドレットが追加されています。ポリシーに関する詳細については、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。

<!---HONumber=AcomDC_0128_2016-->