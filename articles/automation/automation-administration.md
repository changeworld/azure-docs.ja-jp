<properties 
   pageTitle="Azure Automation の管理"
   description="この記事には、Azure Automation 環境の管理に関する複数のトピックが含まれます。現在は、データの保持と Azure Automation のバックアップが含まれます。"
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
   ms.date="07/06/2015"
   ms.author="bwren" />

# Azure Automation の管理

この記事には、Azure Automation 環境の管理に関する複数のトピックが含まれます。

## データの保持

Azure Automation でリソースを削除する場合、完全に削除される前に、監査目的で 90 日間保持されます。この期間にリソースを表示したり使用したりすることはできません。このポリシーは、削除されるオートメーション アカウントに属するリソースにも適用されます。

Azure Automation は、90 日を超えるジョブを自動的に削除して完全に除去します。

次の表は、さまざまなリソースの保持ポリシーをまとめたものです。

|データ|ポリシー|
|:---|:---|
|アカウント|アカウントは、ユーザーによって削除された日から 90 日後に完全に除去されます。|
|資産|資産は、ユーザーによって削除された日から 90 日後に、または資産を保持するアカウントがユーザーによって削除された日から 90 日後に、完全に除去されます。|
|モジュール|モジュールは、ユーザーによって削除された日から 90 日後に、またはモジュールを保持するアカウントがユーザーによって削除された日から 90 日後に、完全に除去されます。|
|Runbook|リソースは、ユーザーによって削除された日から 90 日後に、またはリソースを保持するアカウントがユーザーによって削除された日から 90 日後に、完全に除去されます。|
|ジョブ|最終更新日から 90 日後に削除された後に、完全に除去されます。これは、ジョブが完了、停止、または中断された後に実行されます。|

保持ポリシーはすべてのユーザーに適用され、現在カスタマイズすることはできません。

## Azure Automation のバックアップ

Microsoft Azure でオートメーション アカウントを削除すると、Runbook、モジュール、設定、ジョブ、資産など、アカウント内のすべてのオブジェクトが削除されます。アカウントを削除した後で、オブジェクトを復元することはできません。削除する前に、以下の情報を使用してオートメーション アカウントの内容をバックアップできます。

### Runbook

Azure 管理ポータルまたは Windows PowerShell の [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) コマンドレットを使用して、Runbook をスクリプト ファイルにエクスポートできます。「[Runbook の作成またはインポート](https://msdn.microsoft.com/library/dn643637.aspx)」で説明されているように、このスクリプト ファイルは別のオートメーション アカウントにインポートできます。


### 統合モジュール

Azure Automation から統合モジュールをエクスポートすることはできません。オートメーション アカウントの外部で統合モジュールを使用できることを確認する必要があります。

### 資産

Azure Automation から[資産](https://msdn.microsoft.com/library/dn939988.aspx)をエクスポートすることはできません。Azure 管理ポータルを使用して、変数、資格情報、証明書、接続、およびスケジュールの詳細を記録する必要があります。その後、別のオートメーションにインポートする Runbook で使用されているすべての資産を手動で作成する必要があります。

[Azure コマンドレット](https://msdn.microsoft.com/library/dn690262.aspx)を使用して暗号化されていない資産の詳細を取得し、後で参照するために保存したり、同等の資産を別のオートメーション アカウントに作成したりできます。

暗号化されている変数または資格情報のパスワード フィールドの値を、コマンドレットを使用して取得することはできません。これらの値がわからない場合は、[Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) および [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx) アクティビティを使用して Runbook から値を取得できます。

Azure Automation から証明書をエクスポートすることはできません。すべての証明書が Azure の外部で使用できることを確認する必要があります。

<!---HONumber=July15_HO4-->