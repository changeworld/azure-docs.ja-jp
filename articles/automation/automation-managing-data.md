<properties 
   pageTitle="Azure Automation のデータの管理 | Microsoft Azure"
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

Azure Automation でリソースを削除する場合、完全に削除される前に、監査目的で 90 日間保持されます。この期間にリソースを表示または使用することはできません。このポリシーは、削除済みのオートメーション アカウントに属するリソースにも適用されます。

Azure Automation は、90 日を超えるジョブを自動的に削除した後、完全に消去します。

次の表は、さまざまなリソースの保持ポリシーをまとめたものです。

|データ|ポリシー|
|:---|:---|
|アカウント|ユーザーによってアカウントが削除された日から 90 日後に完全に消去されます。|
|資産|ユーザーによって資産が削除された日から 90 日後に、または資産を保持するアカウントがユーザーによって削除された日から 90 日後に完全に消去されます。|
|モジュール|ユーザーによってモジュールが削除された日から 90 日後に、またはモジュールを保持するアカウントがユーザーによって削除された日から 90 日後に完全に消去されます。|
|Runbooks|ユーザーによってリソースが削除された日から 90 日後に、またはリソースを保持するアカウントがユーザーによって削除された日から 90 日後に完全に消去されます。|
|ジョブ|最終更新日から 90 日後に削除された後、完全に消去されます。これは、ジョブが完了、停止、または中断された後に実行されます。|

保持ポリシーはすべてのユーザーに適用され、現在カスタマイズすることはできません。

## Azure Automation のバックアップ

Microsoft Azure でオートメーション アカウントを削除すると、Runbook、モジュール、設定、ジョブ、資産など、アカウント内のすべてのオブジェクトが削除されます。アカウントを削除した後にオブジェクトを修復することはできません。.削除する前に、以下の情報を使用してオートメーション アカウントの内容をバックアップできます。

### Runbook

Microsoft Azure 管理ポータルまたは Windows PowerShell の [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) コマンドレットを使用して、Runbook をスクリプト ファイルにエクスポートできます。「[Runbook の作成またはインポート](https://msdn.microsoft.com/library/dn643637.aspx)」で説明されているように、このスクリプト ファイルは別のオートメーション アカウントにインポートできます。


### 統合モジュール

Azure Automation から統合モジュールをエクスポートすることはできません。オートメーション アカウントの外部で統合モジュールを使用できることを確認する必要があります。

### 資産

Azure Automation から[資産](https://msdn.microsoft.com/library/dn939988.aspx)をエクスポートすることはできません。Microsoft Azure 管理ポータルを使用して、変数、資格情報、証明書、接続、およびスケジュールの詳細を記録する必要があります。その後、別のオートメーションにインポートする Runbook で使用されているすべての資産を手動で作成する必要があります。

[Azure コマンドレット](https://msdn.microsoft.com/library/dn690262.aspx)を使用して暗号化されていない資産の詳細を取得し、後で参照するために保存したり、同等の資産を別のオートメーション アカウントに作成したりできます。

暗号化されている変数または資格情報のパスワード フィールドの値を、コマンドレットを使用して取得することはできません。これらの値がわからない場合は、[Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) および [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) アクティビティを使用して Runbook から値を取得できます。

Azure Automation から証明書をエクスポートすることはできません。すべての証明書が Azure の外部で使用できることを確認する必要があります。

##Azure Automation での geo レプリケーション

Azure Automation アカウントでの標準である geo レプリケーションでは、アカウント データを別の地理的リージョンにバックアップして冗長性を確保します。アカウントのセットアップ時にはプライマリ リージョンを選択できます。アカウントにはセカンダリ リージョンが自動的に割り当てられます。プライマリ リージョンからコピーされた、セカンダリ データは、データの損失が発生した場合にも継続的に更新されます。

次の表に、使用可能なプライマリ リージョンとセカンダリ リージョンのペアを示します。

|プライマリ |セカンダリ
| ---------------   |----------------
|米国中南部 |米国中北部
|米国東部 2 |米国中央部
|西ヨーロッパ |北ヨーロッパ
|東南アジア |東アジア
|東日本 |西日本

万一、プライマリ リージョンのデータが失われた場合、Microsoft はその復旧を試みます。プライマリ リージョンを復元できない場合は、geo フェールオーバーが実行されます。影響を受けた顧客には、サブスクリプションを通じてこのことが通知されます。

<!----HONumber=Oct15_HO4-->