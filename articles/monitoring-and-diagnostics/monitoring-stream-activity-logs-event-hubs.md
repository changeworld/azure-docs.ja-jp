---
title: Event Hubs への Azure アクティビティ ログのストリーミング | Microsoft Docs
description: Azure アクティビティ ログを Event Hubs にストリーミングする方法について説明します。
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ec4c2d2c-8907-484f-a910-712403a06829
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2018
ms.author: johnkem
ms.openlocfilehash: 4b2d9866839f943f65beb271d44bc691441b0fb3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Event Hubs への Azure アクティビティ ログのストリーミング
[Azure アクティビティ ログ](monitoring-overview-activity-logs.md)は、以下のいずれかを実行することで、あらゆるアプリケーションでほぼリアルタイムにストリームできます。

* ポータルの組み込み **[エクスポート]** オプションを使用する
* Azure PowerShell コマンドレットまたは Azure CLI を使用してログのプロファイルで Azure Service Bus ルール ID を有効にする

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>アクティビティ ログと Event Hubs で実行できること
アクティビティ ログのストリーミング機能は、次の 2 つの方法で使用できます。

* **サード パーティのログおよびテレメトリ システムにストリーミングする** - 将来的に、Azure Event Hubs のストリーミングは、アクティビティ ログをサード パーティの SIEM やログ分析ソリューションにパイプ処理するためのメカニズムになります。
* **カスタムのテレメトリおよびログ プラットフォームを構築する** - カスタム構築されたテレメトリ プラットフォームが既にある場合や構築を検討している場合は、Event Hubs の非常にスケーラブルな発行/サブスクライブの特性により、アクティビティ ログを柔軟に取り込むことができます。 詳細については、[グローバル規模のテレメトリ プラットフォームで Event Hubs を使用する方法に関する Dan Rosanova によるガイド](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)をご覧ください。

## <a name="enable-streaming-of-the-activity-log"></a>アクティビティ ログのストリーミングの有効化
アクティビティ ログのストリーミングは、プログラムによって有効にすることも、ポータルで有効にすることもできます。 いずれの方法でも、イベント ハブ名前空間と、その名前空間の共有アクセス ポリシーを選択します。 名前が insights-logs-operationallogs のイベント ハブが その名前空間に作成されるのは、新しいアクティビティ ログ イベントが最初に発生したときです。 

Event Hubs 名前空間が存在しない場合は、最初に作成する必要があります。 この Event Hubs 名前空間にアクティビティ ログ イベントをストリーミングしたことがある場合は、以前に作成された Event Hub が再利用されます。 

共有アクセス ポリシーでは、ストリーミング メカニズムのアクセス許可が定義されます。 現在、Event Hubs にストリーミングするには、**管理**、**送信**、**リッスン**の各アクセス許可が必要です。 Event Hubs 名前空間の共有アクセス ポリシーは、Azure Portal の Event Hubs 名前空間の **[構成]** タブで作成または変更できます。 

アクティビティ ログのログ プロファイルにストリーミングを含めるよう更新するには、変更操作を実行するユーザーに、その Event Hubs の承認規則に対する ListKey アクセス許可が必要です。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、Event Hubs 名前空間は、ログを出力するサブスクリプションと同じサブスクリプションに属している必要はありません。

### <a name="via-the-azure-portal"></a>Azure Portal の使用
1. ポータル左側の **[すべてのサービス]** 検索を使用して、**[アクティビティ ログ]** セクションに移動します。
   
   ![ポータルのサービスの一覧からアクティビティ ログを選択します。](./media/monitoring-stream-activity-logs-event-hubs/activity.png)
2. ログの上部にある **[エクスポート]** ボタンを選択します。
   
   ![ポータルの [エクスポート] ボタン](./media/monitoring-stream-activity-logs-event-hubs/export.png)

   前のビューでアクティビティ ログを表示したときに適用したフィルターの設定は、エクスポート設定には影響ありません。 これは、ポータルで、アクティビティ ログを参照中に表示される内容をフィルター処理するためのみに使用されます。
3. 表示されるセクションで、**[All regions]\(すべての領域\)** を選択します。 特定の領域を選択しないでください。
   
   ![セクションのエクスポート](./media/monitoring-stream-activity-logs-event-hubs/export-audit.png)

   > [!WARNING]  
   > **[All regions]\(すべての領域\)** 以外を選択した場合、受信することを期待している重要なイベントを受信できなくなります。 アクティビティ ログはグローバル (領域に限定されない) ログであるため、ほとんどのイベントに領域は関連付けられていません。 
   > 

4. **[保存]** をクリックしてこれらの設定を保存します。 設定はサブスクリプションにすぐに適用されます。
5. 複数のサブスクリプションがある場合は、この操作を繰り返して、すべてのデータを同じイベント ハブに送信します。

### <a name="via-powershell-cmdlets"></a>PowerShell コマンドレットの使用
ログ プロファイルが既に存在する場合は、まず、そのプロファイルを削除する必要があります。

1. `Get-AzureRmLogProfile` を使用して、ログ プロファイルが存在するかどうかを確認します。
2. 存在する場合は、 `Remove-AzureRmLogProfile` を使用して削除します。
3. `Set-AzureRmLogProfile` を使用して、プロファイルを作成します。

   ```powershell

   Add-AzureRmLogProfile -Name my_log_profile -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action

   ```

Service Bus ルール ID は、この形式 `{service bus resource ID}/authorizationrules/{key name}` の文字列です。 

### <a name="via-azure-cli"></a>Azure CLI の使用
ログ プロファイルが既に存在する場合は、まず、そのプロファイルを削除する必要があります。

1. `azure insights logprofile list` を使用して、ログ プロファイルが存在するかどうかを確認します。
2. 存在する場合は、 `azure insights logprofile delete` を使用して削除します。
3. `azure insights logprofile add` を使用して、プロファイルを作成します。

   ```azurecli-interactive
   azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
   ```

Service Bus ルール ID は、この形式 `{service bus resource ID}/authorizationrules/{key name}` の文字列です。

## <a name="consume-the-log-data-from-event-hubs"></a>Event Hubs からのログ データを使用する
アクティビティ ログのスキーマについては、[Azure アクティビティ ログで、サブスクリプションのアクティビティを監視する](monitoring-overview-activity-logs.md)に関するドキュメントを参照してください。 各イベントは、*レコード*と呼ばれる JSON BLOB の配列です。

## <a name="next-steps"></a>次の手順
* [ストレージ アカウントにアクティビティ ログをアーカイブする](monitoring-archive-activity-log.md)
* [Azure アクティビティ ログの概要を確認する](monitoring-overview-activity-logs.md)
* [アクティビティ ログ イベントに基づいてアラートを設定する](insights-auditlog-to-webhook-email.md)

