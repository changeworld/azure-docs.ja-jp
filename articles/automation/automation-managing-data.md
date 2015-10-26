<properties 
   pageTitle="Azure Automation のデータの管理"
   description="この記事には、Azure Automation 環境の管理に関する複数のトピックが含まれています。現在は、データの保持、Azure Automation のバックアップ、Azure Automation での障害復旧が含まれています。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/08/2015"
   ms.author="bwren;sngun" />

# Azure Automation のデータの管理

この記事には、Azure Automation 環境の管理に関する複数のトピックが含まれています。

## データの保持

Azure Automation でリソースを削除する場合、完全に削除される前に、監査目的で 90 日間保持されます。You can’t see or use the resource during this time.This policy also applies to resources that belong to an automation account that is deleted.

Azure Automation automatically deletes and permanently removes jobs older than 90 days.

The following table summarizes the retention policy for different resources.

|Data|Policy|
|:---|:---|
|Accounts|Permanently removed 90 days after the account is deleted by a user.|
|Assets|Permanently removed 90 days after the asset is deleted by a user, or 90 days after the account that holds the asset is deleted by a user.|
|Modules|Permanently removed 90 days after the module is deleted by a user, or 90 days after the account that holds the module is deleted by a user.|
|Runbooks|Permanently removed 90 days after the resource is deleted by a user, or 90 days after the account that holds the resource is deleted by a user.|
|Jobs|Deleted and permanently removed 90 days after last being modified.This could be after the job completes, is stopped, or is suspended.|

保持ポリシーはすべてのユーザーに適用され、現在カスタマイズすることはできません。

## Azure Automation のバックアップ

Microsoft Azure でオートメーション アカウントを削除すると、Runbook、モジュール、設定、ジョブ、資産など、アカウント内のすべてのオブジェクトが削除されます。The objects cannot be recovered after the account is deleted.削除する前に、以下の情報を使用してオートメーション アカウントの内容をバックアップできます。

### Runbook

Azure 管理ポータルまたは Windows PowerShell の [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) コマンドレットを使用して、Runbook をスクリプト ファイルにエクスポートできます。「[Runbook の作成またはインポート](https://msdn.microsoft.com/library/dn643637.aspx)」で説明されているように、このスクリプト ファイルは別のオートメーション アカウントにインポートできます。


### 統合モジュール

Azure Automation から統合モジュールをエクスポートすることはできません。オートメーション アカウントの外部で統合モジュールを使用できることを確認する必要があります。

### 資産

Azure Automation から[資産](https://msdn.microsoft.com/library/dn939988.aspx)をエクスポートすることはできません。Azure 管理ポータルを使用して、変数、資格情報、証明書、接続、およびスケジュールの詳細を記録する必要があります。その後、別のオートメーションにインポートする Runbook で使用されているすべての資産を手動で作成する必要があります。

[Azure コマンドレット](https://msdn.microsoft.com/library/dn690262.aspx)を使用して暗号化されていない資産の詳細を取得し、後で参照するために保存したり、同等の資産を別のオートメーション アカウントに作成したりできます。

暗号化されている変数または資格情報のパスワード フィールドの値を、コマンドレットを使用して取得することはできません。これらの値がわからない場合は、[Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) および [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) アクティビティを使用して Runbook から値を取得できます。

Azure Automation から証明書をエクスポートすることはできません。すべての証明書が Azure の外部で使用できることを確認する必要があります。

##Azure Automation での geo レプリケーション

Azure Automation は、geo レプリケーションをサポートしています。geo レプリケーションにより、Azure Automation では、2 つのリージョンでデータの永続性が維持されます。Azure ポータルでの Automation アカウントの作成時に、作成する必要があるリージョンを選択すると、これがプライマリ リージョンになります。データが geo レプリケートされるリージョンは、セカンダリ リージョンと呼ばれます。プライマリ リージョンとセカンダリ リージョンが相互にやり取りして、Automation アカウントに対して行われた更新を geo レプリケートします。セカンダリ リージョンには情報のコピーが保存されているので、プライマリ リージョンからセカンダリ リージョンへの Automation アカウントのフェールオーバーが発生した場合、すべての Automation アカウント情報をセカンダリ リージョンで引き続き利用できます。

geo レプリケーションは、Automation アカウントに組み込まれており、追加料金なしで提供されます。セカンダリ リージョンの選択は制御できません。セカンダリ リージョンは、プライマリ リージョンとして選択した場所に基づいて自動的に決定されます。

 
###geo レプリカの場所

現在、Automation アカウントは、次の 5 つのリージョンで作成できます。今後、さらに多くのリージョンのサポートが追加される予定です。次の表に、プライマリ リージョンとセカンダリ リージョンのペアを示します。

|プライマリ |セカンダリ
| ---------------   |----------------
|米国中南部 |米国中北部
|米国東部 2 |米国中央部
|西ヨーロッパ |北ヨーロッパ
|東南アジア |東アジア
|東日本 |西日本


###Azure Automation での障害復旧

重大な障害がプライマリ リージョンに影響を及ぼした場合、Automation チームは、まずプライマリ リージョンの復元を試みます。プライマリ リージョンを復元できない場合は、geo フェールオーバーが実行されます。影響を受けた顧客には、サブスクリプションを通じてこのことが通知されます。

<!---HONumber=Oct15_HO3-->