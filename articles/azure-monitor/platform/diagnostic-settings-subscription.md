---
title: 診断設定を使用して Azure アクティビティ ログを収集する (プレビュー) - Azure Monitor | Microsoft Docs
description: 診断設定を使用して、Azure アクティビティ ログを Azure Monitor ログ、Azure Storage、または Azure Event Hubs に転送します。
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 6104a8b01cc9fca5ff8de973e7fc2af77cda8515
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048206"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>診断設定を使用して Azure アクティビティ ログを収集する (プレビュー)
[Azure アクティビティ ログ](activity-logs-overview.md)は、Azure で発生したサブスクリプションレベルのイベントの分析情報を提供する[プラットフォーム ログ](platform-logs-overview.md)です。 ここまで、アクティビティ ログ エントリを[イベント ハブまたはストレージ アカウント](activity-log-export.md)に送信するためのログ プロファイルを作成し、コネクタを使用してこれらを [Log Analytics ワークスペース](activity-log-collect.md)に収集しました。

これで、[リソース ログ](resource-logs-overview.md)の収集に使用されるのと同じ[診断設定](diagnostic-settings.md)を使用して、Azure アクティビティ ログの収集を構成できるようになりました。 診断設定を使用する方法は、現在の方法よりも次の点で優れています。

- すべてのプラットフォーム ログを収集するための一貫した方法。
- 複数のサブスクリプションとテナントにわたってアクティビティ ログを収集する。
- コレクションをフィルター処理して特定のカテゴリのログのみを収集する。
- すべてのアクティビティ ログ カテゴリを収集する。 一部のカテゴリは、前の方法を使用して収集されません。
- ログ インジェストの待機時間が短縮される。 前の方法では約 15 分の待機時間がありましたが、診断設定では約 1 分しか追加されません。

## <a name="considerations"></a>考慮事項
診断設定を使用したアクティビティ ログの収集について、この機能を有効にする前に次の詳細を考慮してください。

- 診断設定を使用してアクティビティの収集を有効にする前に、既存の収集を無効にする必要があります。 両方を有効にすると、重複するデータが生成される可能性があります。
- アクティビティ ログを Azure Storage に収集するための保存期間の設定が削除されているため、データは削除されるまで無期限に保存されます。
- アクティビティ ログは、複数のサブスクリプションから 1 つの Log Analytics ワークスペースに送信できます。 アクティビティ ログは、任意の 1 つのサブスクリプションから複数の Log Analytics ワークスペースに送信できます。

## <a name="configure-diagnostic-settings"></a>診断設定を構成する
Azure アクティビティ ログを収集するために Azure portal で診断設定を作成するには、次の手順に従います。 現在のところ、他の方法を使用してサブスクリプション レベルの診断設定を作成することはできません。

1. アクティビティ ログに対する既存の Log Analytics ワークスペース コレクションを無効にします。
   1. Azure portal の **[Log Analytics ワークスペース]** メニューを開き、アクティビティ ログを収集するためのワークスペースを選択します。
   2. ワークスペースのメニューの **[ワークスペースのデータ ソース]** セクションで、 **[Azure アクティビティ ログ]** を選択します。
   3. 切断するサブスクリプションをクリックします。
   4. **[切断]** をクリックし、選択を確認するメッセージが表示されたら、 **[はい]** をクリックします。
2. Azure portal の **[Azure Monitor]** メニューで、 **[アクティビティ ログ]** を選択します。
3. **[診断設定]** をクリックします。
   
   ![診断設定](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. 従来のエクスペリエンスの紫色のバナーをクリックし、ストレージまたはイベント ハブへの現在の収集を無効にします。 

    ![従来のエクスペリエンス](media/diagnostic-settings-subscription/legacy-experience.png)

5. 「[Azure portal で診断設定を作成する](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)」の手順に従って、診断設定を作成します。 アクティビティ ログのイベントをフィルター処理するために使用できるカテゴリの詳細については、「[アクティビティ ログのカテゴリ](activity-logs-overview.md#categories-in-the-activity-log)」を参照してください。 


## <a name="differences-in-data"></a>データの相違点
診断設定では、アクティビティ ログの収集に使用された前の方法と同じデータが収集されますが、現時、データには次の相違点があります。

次のプロパティが削除されています。

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

次のプロパティが追加されています。

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-monitoring-solution"></a>アクティビティ ログ監視ソリューション
Azure Log Analytics 監視ソリューションには、Log Analytics ワークスペースのアクティビティ ログ レコードを分析するための、複数のログ クエリとビューが含まれています。 このソリューションでは、Log Analytics ワークスペースで収集されたログ データが使用されます。診断設定を使用してアクティビティ ログを収集する場合も、このソリューションは変更を加えずに引き続き機能します。 このソリューションの詳細については、「[Activity Logs Analytics の監視ソリューション](activity-log-collect.md#activity-logs-analytics-monitoring-solution)」を参照してください。

## <a name="next-steps"></a>次の手順

* [アクティビティ ログについて詳しく学習します](../../azure-resource-manager/resource-group-audit.md)
* [診断設定について詳しく学習します](diagnostic-settings.md)
