<properties 
   pageTitle="Azure Automation のデータ保持"
   description="Azure Automation のデータ保持ポリシーについて説明します。"
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
   ms.date="04/13/2015"
   ms.author="bwren" />

# Azure Automation のデータ保持

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

## 関連記事:
- [Azure オートメーションのバックアップ](https://msdn.microsoft.com/library/dn643635.aspx) 

<!---HONumber=July15_HO3-->