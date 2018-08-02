---
title: Event Hubs への Azure アクティビティ ログのストリーミング
description: Azure アクティビティ ログを Event Hubs にストリーミングする方法について説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 7a5372174fcc7cd9552c00c9d283772c9863b815
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258000"
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

アクティビティ ログのログ プロファイルにストリーミングを含めるよう更新するには、変更操作を実行するユーザーに、その Event Hubs の承認規則に対する ListKey アクセス許可が必要です。 設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っており、さらに両方のサブスクリプションが同じ AAD テナントに入っている限り、Event Hubs 名前空間は、ログを出力するサブスクリプションと同じサブスクリプションに属している必要はありません。

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

4. **[Azure Event Hubs]** オプションをクリックし、ログを送信するイベント ハブ名前空間を選択し、**[OK]** をクリックします。
5. **[保存]** をクリックしてこれらの設定を保存します。 設定はサブスクリプションにすぐに適用されます。
6. 複数のサブスクリプションがある場合は、この操作を繰り返して、すべてのデータを同じイベント ハブに送信します。

### <a name="via-powershell-cmdlets"></a>PowerShell コマンドレットの使用
ログ プロファイルが既に存在する場合は、最初に既存のログ プロファイルを削除してから、新しいログ プロファイルを作成する必要があります。

1. `Get-AzureRmLogProfile` を使用して、ログ プロファイルが存在するかどうかを確認します。  ログ プロファイルが存在する場合は、*name* プロパティを探します。
2. `Remove-AzureRmLogProfile` を使用し、*name* プロパティの値を使用してログ プロファイルを削除します。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzureRmLogProfile -Name "default"
    ```
3. `Add-AzureRmLogProfile` を使用して、新しいログ プロファイルを作成します。

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzureRmLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   Add-AzureRmLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Azure CLI の使用
ログ プロファイルが既に存在する場合は、最初に既存のログ プロファイルを削除してから、新しいログ プロファイルを作成する必要があります。

1. `az monitor log-profiles list` を使用して、ログ プロファイルが存在するかどうかを確認します。
2. `az monitor log-profiles delete --name "<log profile name>` を使用し、*name* プロパティの値を使用してログ プロファイルを削除します。
3. `az monitor log-profiles create` を使用して、新しいログ プロファイルを作成します。

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Event Hubs からのログ データを使用する
アクティビティ ログのスキーマについては、[Azure アクティビティ ログで、サブスクリプションのアクティビティを監視する](monitoring-overview-activity-logs.md)に関するドキュメントを参照してください。 各イベントは、*レコード*と呼ばれる JSON BLOB の配列です。

## <a name="next-steps"></a>次の手順
* [ストレージ アカウントにアクティビティ ログをアーカイブする](monitoring-archive-activity-log.md)
* [Azure アクティビティ ログの概要を確認する](monitoring-overview-activity-logs.md)
* [アクティビティ ログ イベントに基づいてアラートを設定する](insights-auditlog-to-webhook-email.md)

