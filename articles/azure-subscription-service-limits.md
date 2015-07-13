<properties
	pageTitle="Microsoft Azure サブスクリプションとサービスの制限、クォータ、制約"
	description="一般的な Azure サブスクリプションとサービスの制限、クォータ、制約の一覧を示します。制限を引き上げる方法と、最大値に関する情報も記載されています。"
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="mollybos"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/07/2015"
	ms.author="jroth"/>

# Azure サブスクリプションとサービスの制限、クォータ、制約

## 概要

本書では、Microsoft Azure での最も一般的な制限について具体的に説明しています。現時点では、すべての Azure サービスを網羅していませんのでご了承ください。今後、Azure Platform 全体を広くカバーするように制限を展開し、更新していく予定です。

> [AZURE.NOTE]「**既定の制限**」を超える制限の引き上げを希望される場合は、[オンライン カスタマー サポートに申請 (無料)](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) してください。以下の表の「**上限**」の値を超える制限の引き上げはできません。「**上限**」列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。

## 制限と Azure リソース マネージャー

複数の Azure リソースを 1 つの Azure リソース グループに結合できるようになりました。リソース グループを使用するとき、グローバルになった制限が Azure リソース マネージャーによりリージョン レベルでの管理対象になります。Azure リソース グループの詳細については、[リソース グループを使用した Azure のリソースの管理](resource-group-portal.md)に関するページを参照してください。

次の制限では、Azure リソース マネージャーを使用する際の制限の相違点を反映するために新しい表が追加されました。たとえば、**サブスクリプションの制限**の表と、**サブスクリプションの制限 - Azure リソース マネージャー**の表などがあります。制限が 2 つのシナリオに適用される場合、制限は最初の表にのみ表示されます。特に記載のない限り、制限はすべてのリージョンに適用されます。

> [AZURE.NOTE]Azure リソース グループ内のリソースのクォータは、サブスクリプションごとではなく、サブスクリプションのリージョンごとにアクセスできることに注意してください。また、サービス管理クォータも同様です。コア クォータを例に説明します。コア対応のクォータの増量をリクエストする場合、どのリージョンでいくつのコアを使用するかを決めてから、Azure リソース グループのコア クォータの数量と目的のリージョンをリクエストする必要があります。したがって、西ヨーロッパで 30 のコアを使用してアプリケーションを実行する必要がある場合、具体的に、西ヨーロッパで 30 のコアをリクエストしてください。この場合、他のリージョンのコア クォータは増量されません -- 西ヨーロッパでのみ 30 コア クォータが増量されます。<!-- --> 結果として、1 つのリージョンのワークロードに対してどのくらいの Azure リソース グループのクォータが必要かを決め、デプロイメントを検討している各リージョンにその分量をリクエストするだけなので便利です。特定のリージョンの現在のクォータを判断する方法について詳しくは、[デプロイメントの問題のトラブルシューティング](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues)に関するページを参照してください。

## サブスクリプションの制限

[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]


## サブスクリプションの制限 - Azure リソース マネージャー 

次の制限は、Azure リソース マネージャーと Azure リソース グループの使用時に適用されます。Azure リソース マネージャーで変更されていない制限はこの一覧に含まれません。これらの制限については、前の表を参照してください。

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## リソース グループの制限

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


## 仮想マシンの制限

[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


## 仮想マシンの制限 - Azure リソース マネージャー

次の制限は、Azure リソース マネージャーと Azure リソース グループの使用時に適用されます。Azure リソース マネージャーで変更されていない制限はこの一覧に含まれません。これらの制限については、前の表を参照してください。

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## ネットワークの制限

[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]


## ネットワークの制限 - Azure リソース マネージャー

次の制限は、Azure リソース マネージャーと Azure リソース グループの使用時に適用されます。Azure リソース マネージャーで変更されていない制限はこの一覧に含まれません。これらの制限については、前の表を参照してください。

[AZURE.INCLUDE [azure-virtual-network-limits-azure-resource-manager](../includes/azure-virtual-network-limits-azure-resource-manager.md)]


## 標準ストレージの制限

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

## Premium Storage の制限

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">リソース</th>
   <th align="left" valign="middle">既定の制限</th>
</tr>
<tr>
   <td valign="middle"><p>アカウントごとのディスク容量合計</p></td>
   <td valign="middle"><p>35 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>アカウントごとのスナップショット容量合計</p></td>
   <td valign="middle"><p>10 TB</p></td>
</tr>
<tr>
   <td valign="middle"><p>アカウントごとの最大帯域幅 (受信 + 送信)</p></td>
   <td valign="middle"><p>50 Gbps</p></td>
</tr>
</table>


## ストレージの制限 - Azure リソース マネージャー

次の制限は、Azure リソース マネージャーと Azure リソース グループの使用時に適用されます。Azure リソース マネージャーで変更されていない制限はこの一覧に含まれません。これらの制限については、前の表を参照してください。

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## Cloud Services の制限

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


## Web Apps (Webs サイト) の制限

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]


## バッチのプレビューの制限

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]


## DocumentDB の制限

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


## モバイル エンゲージメントの制限

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


## SQL Database の制限

[AZURE.INCLUDE [azure-sql-database-limits](../includes/azure-sql-database-limits.md)]


## Media Services の制限

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]


## Service Bus の制限

[AZURE.INCLUDE [azure-servicebus-limits](../includes/azure-servicebus-limits.md)]


## Active Directory の制限

[AZURE.INCLUDE [azure-active-directory-limits](../includes/azure-active-directory-limits.md)]


## RemoteApp の制限

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

## StorSimple システムの制限

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]
 
## 関連項目

[Understanding Azure Limits and Increases (Azure の制限と増設について)](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)

<!---HONumber=62-->