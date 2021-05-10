---
title: Azure VMware Solution でアラートを構成し、メトリックを使用する
description: アラートを使用して通知を受信する方法について説明します。 また、メトリックを使用して、Azure VMware Solution のプライベート クラウドに関する深い洞察を得る方法についても説明します。
ms.topic: how-to
ms.date: 04/02/2021
ms.openlocfilehash: 486f25eba017b2d4e37c0796909a0d26adee6ba8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309630"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Azure VMware Solution で Azure アラートを構成する 

この記事では、[Microsoft Azure アラート](/azure/azure-monitor/alerts/alerts-overview)で [Azure アクション グループ](/azure/azure-monitor/alerts/action-groups)を構成して、定義したトリガー イベントの通知を受け取る方法について説明します。 また、[Azure Monitor メトリック](/azure/azure-monitor/essentials/data-platform-metrics) を使用して、Azure VMware Solution のプライベート クラウドに関する深い洞察を得る方法についても説明します。


## <a name="supported-metrics-and-activities"></a>サポートされているメトリックとアクティビティ

次のメトリックは、Azure Monitor メトリックを使用して表示できます。

| **シグナル名**                                                         | **シグナルの種類** | **サービスの監視** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| データストア ディスクの合計容量                                           | 指標          | プラットフォーム            |
| 使用済みのデータストア ディスクの割合                                          | 指標          | プラットフォーム            |
| CPU 使用率                                                          | 指標          | プラットフォーム            |
| 平均有効メモリ                                                | 指標          | プラットフォーム            |
| 平均メモリ オーバーヘッド                                                 | 指標          | プラットフォーム            |
| 平均メモリ合計                                                    | 指標          | プラットフォーム            |
| メモリの平均使用量                                                    | 指標          | プラットフォーム            |
| 使用済みのデータストア ディスク                                                     | 指標          | プラットフォーム            |
| すべての管理操作                                           | アクティビティ ログ    | 管理      |
| Microsoft.AVS リソース プロバイダーを登録する。 (Microsoft.AVS/privateClouds) | アクティビティ ログ    | 管理      |
| PrivateCloud を作成または更新する。 (Microsoft.AVS/privateClouds)          | アクティビティ ログ    | 管理      |
| PrivateCloud を削除する。 (Microsoft.AVS/privateClouds)                    | アクティビティ ログ    | 管理      |

## <a name="configure-an-alert-rule"></a>アラート ルールの構成
1. Azure VMware Solution プライベート クラウドから、 **[監視]**  >  **[アラート]** 、 **[新しいアラート ルール]** の順に選択します。
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Azure VMware Solution プライベート クラウドでアラート ルールを構成する場所を示すスクリーンショット。" lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   新しい構成画面が開いたら、そこで次の操作を行います。
   - スコープの定義
   - 条件の構成
   - アクション グループの設定
   - アラート ルールの詳細の定義
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png" alt-text="[アラート ルールの作成] ウィンドウを示すスクリーンショット。" lightbox="media/configure-alerts-for-azure-vmware-solution/create-alert-rule-details.png":::

1. **[スコープ]** の下で、監視するターゲット リソースを選択します。 既定では、[アラート] メニューを開いた Azure VMware Solution プライベート クラウドが定義されています。

1. **[条件]** で **[条件の追加]** を選択し、開いたウィンドウで、アラート ルールに対して作成するシグナルを選択します。 

   この例では、[Azure VMware Solution SLA](https://aka.ms/avs/sla) の観点から見て妥当な、 **[Percentage Datastore Disk Used] (データストア ディスク使用率)** を選択しています。 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="定義済みのシグナル名を持つ [シグナル ロジックの構成] ウィンドウを示すスクリーンショット。"::: 

1. アラートをトリガーするロジックを定義し、 **[完了]** を選択します。 

   この例では、**しきい値** および **評価の頻度** のみが調整されています。 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="選択したシグナル ロジックの情報を示すスクリーンショット。"::: 

1. **[アクション]** で、**[アクション グループの追加]** を選択します。 アクション グループでは、通知の受信 *方法* と受信 *者* が定義されます。   通知は、メール、SMS、[Azure モバイル アプリのプッシュ通知](https://azure.microsoft.com/features/azure-portal/mobile-app/)、音声メッセージなどで受信できます。
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group.png" alt-text="既存のアクション グループと、新しいアクション グループを作成する場所を示すスクリーンショット。":::

1. 開いたウィンドウで、 **[アクショングループの作成]** を選択します。

   >[!TIP]
   > 既存のアクション グループを使用することもできます。

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/select-action-group-for-alert-rule.png" alt-text="アラートに対して選択するアクション グループを示すスクリーンショット。"::: 

 

 
1. 開いたウィンドウの **[基本]** タブで、アクション グループに名前と表示名を付けます。

1. **[通知]** タブで、 **[通知の種類]** と **[名前]** を選択します。 次に、 **[OK]** を選択します。

   この例は、メール通知に基づいています。

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png" alt-text="アラートのメール、SMS メッセージ、プッシュ、音声の設定を示すスクリーンショット。" lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-notification-settings.png":::     

1. (省略可能) プロアクティブなアクションを選択し、イベント発生時に通知を受け取る場合は、 **[アクション]** を構成します。 使用可能な **[アクションの種類]** を選択し、 **[確認と作成]** を選択します。 
   - Automation Runbooks
   - Azure Functions – イベントドリブンのカスタム サーバーレス コードを実行するため
   - ITSM – ServiceNow などのサービス プロバイダーと統合してチケットを作成するため
   - Logic App – より複雑なワークフロー オーケストレーション用
   - Webhooks – 別のサービスでプロセスをトリガーするため

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png" alt-text="[アクション グループの作成] ウィンドウを示すスクリーンショット。[アクションの種類] ドロップダウンにフォーカスが当てられています。" lightbox="media/configure-alerts-for-azure-vmware-solution/create-action-group-action-type.png":::     

1. **[アラート ルールの詳細]** で、名前、説明、アラート ルールを保存するリソースグループ、重大度を指定します。 次に、 **[アラート ルールの作成]** を選択します。
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-rule-details.png" alt-text="アラート ルールの詳細を示すスクリーンショット。"::: 
 
   アラート ルールが表示され、Azure portal から管理できます。

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="[ルール] ウィンドウに表示されている、新しいアラート ルールを示すスクリーンショット。" lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::     

   アラート ルールで定義されているしきい値にメトリックが達するとすぐに、 **[アラート]** メニューが更新され、表示されるようになります。

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="定義されたしきい値に達した後のアラートを示すスクリーンショット。" lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   構成されているアクション グループに応じて、構成されたメディアを通じて通知を受け取ります。 この例では、メールを構成しました。
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="エラー文字列、イベントがトリガーされた日付/時刻を含む Azure Monitor アラートのスクリーンショット。"::: 

## <a name="work-with-metrics"></a>メトリックの使用

1. Azure VMware Solution のプライベート クラウドから、 **[監視]**  >  **[メトリック]** を選択します。 次に、ドロップダウンから目的のメトリックを選択します。
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="[メトリック] ウィンドウを示すスナップショット。[メトリック] ドロップダウンにフォーカスが当てられています。" lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. **時間の範囲** や **時間の粒度** など、ダイアグラムのパラメーターを変更できます。 

   他のオプションは次のとおりです。
   - 関連する Log Analytics ワークスペースで、**ログの詳細を確認** し、データを照会する
   - 使いやすくするために、Azure ダッシュボードに **このダイアグラムをピン留め** する。

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="メトリックの時間範囲および時間粒度オプションを示すスクリーンショット。" lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>次のステップ

これで、Azure VMware Solution プライベート クラウドのアラート ルールが構成されました。次について、さらに詳細な情報を得ることができます。
- [Azure Monitor のメトリック](/azure/azure-monitor/essentials/data-platform-metrics)
- [Azure Monitor アラート](/azure/azure-monitor/alerts/alerts-overview)
- [Azure アクション グループ](/azure/azure-monitor/alerts/action-groups)

また、[Azure VMware Solution](index.yml) のその他のハウツー ガイドのいずれかを使用して、続行することもできます。





