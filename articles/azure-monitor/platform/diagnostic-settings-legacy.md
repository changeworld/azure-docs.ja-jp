---
title: 診断設定を使用して Azure アクティビティ ログを収集する - Azure Monitor | Microsoft Docs
description: 診断設定を使用して、Azure アクティビティ ログを Azure Monitor ログ、Azure Storage、または Azure Event Hubs に転送します。
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2020
ms.locfileid: "79096895"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Azure のアクティビティ ログの収集とエクスポートの更新
[Azure アクティビティ ログ](platform-logs-overview.md)は、Azure で発生したサブスクリプションレベルのイベントの分析情報を提供する[プラットフォーム ログ](platform-logs-overview.md)です。 アクティビティ ログ エントリを[イベント ハブまたはストレージ アカウント](activity-log-export.md)、または [Log Analytics ワークスペース](activity-log-collect.md)に送信するメソッドは、[診断設定](diagnostic-settings.md)を使用するように変更されました。 この記事では、これらの方法の相違点、および診断設定に変更するための準備として、従来の設定をクリアする方法について説明します。


## <a name="differences-between-methods"></a>方法の相違点

### <a name="advantages"></a>長所
診断設定を使用する方法は、現在の方法よりも次の点で優れています。

- すべてのプラットフォーム ログを収集するための一貫した方法。
- 複数のサブスクリプションとテナントにわたってアクティビティ ログを収集する。
- コレクションをフィルター処理して特定のカテゴリのログのみを収集する。
- すべてのアクティビティ ログ カテゴリを収集する。 一部のカテゴリは、従来の方法を使用して収集されません。
- ログ インジェストの待機時間が短縮される。 前の方法では約 15 分の待機時間がありましたが、診断設定では約 1 分しか追加されません。

### <a name="considerations"></a>考慮事項
診断設定を使用したアクティビティ ログの収集について、この機能を有効にする前に次の詳細を考慮してください。

- アクティビティ ログを Azure Storage に収集するための保存期間の設定が削除されているため、データは削除されるまで無期限に保存されます。
- 現時点では、Azure portal を使用してのみサブスクリプション レベルの診断設定を作成できます。 PowerShell や CLI などの他の方法を使用するには、Resource Manager テンプレートを作成します。


### <a name="differences-in-data"></a>データの相違点
診断設定では、アクティビティ ログの収集に使用された前の方法と同じデータが収集されますが、現時、データには次の相違点があります。

以下の列が削除されました。 これらの列の代わりは形式が異なるため、これらの列を使用するログ クエリを変更する必要になる場合があります。 スキーマには削除された列が表示される場合がありますが、データは取り込まれません。

| 削除列 | 置換列 |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

次の列が追加されました。

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> 場合によっては、これらの列の値がすべて大文字になることがあります。 これらの列を含むクエリがある場合は、[=~ 演算子](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators)を使用して、大文字と小文字を区別しない比較を実行する必要があります。

## <a name="work-with-legacy-settings"></a>従来の設定を使用する
診断設定に置き換えることを選択しない場合、アクティビティ ログを収集するための従来の設定は引き続き機能します。 サブスクリプションのログ プロファイルを管理するには、次の方法を使用します。

1. Azure portal の **[Azure Monitor]** メニューで、 **[アクティビティ ログ]** を選択します。
3. **[診断設定]** をクリックします。

   ![診断設定](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 従来のエクスペリエンスについては、紫色のバナーをクリックします。

    ![従来のエクスペリエンス](media/diagnostic-settings-subscription/legacy-experience.png)


従来の収集方法の使用に関する詳細については、次の記事を参照してください。

- [Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する](activity-log-collect.md)
- [Azure Active Directory テナントにまたがる Azure Monitor に Azure Activity ログを収集する](activity-log-collect-tenants.md)
- [Azure アクティビティ ログをストレージまたは Azure Event Hubs にエクスポートする](activity-log-export.md)

## <a name="disable-existing-settings"></a>既存の設定を無効にする
診断設定を使用してアクティビティの収集を有効にする前に、既存の収集を無効にする必要があります。 両方を有効にすると、重複するデータが生成される可能性があります。

### <a name="disable-collection-into-log-analytics-workspace"></a>Log Analytics ワークスペースへの収集を無効にする

1. Azure portal の **[Log Analytics ワークスペース]** メニューを開き、アクティビティ ログを収集するためのワークスペースを選択します。
2. ワークスペースのメニューの **[ワークスペースのデータ ソース]** セクションで、 **[Azure アクティビティ ログ]** を選択します。
3. 切断するサブスクリプションをクリックします。
4. **[切断]** をクリックし、選択を確認するメッセージが表示されたら、 **[はい]** をクリックします。

### <a name="disable-log-profile"></a>ログ プロファイルを無効にする

1. 「[従来の設定を使用する](#work-with-legacy-settings)」で説明されている手順に従って、従来の設定を開きます。
2. ストレージまたはイベント ハブへの現在の収集を無効にします。



## <a name="activity-log-monitoring-solution"></a>アクティビティ ログ監視ソリューション
Azure Log Analytics 監視ソリューションには、Log Analytics ワークスペースのアクティビティ ログ レコードを分析するための、複数のログ クエリとビューが含まれています。 このソリューションでは、Log Analytics ワークスペースで収集されたログ データが使用されます。診断設定を使用してアクティビティ ログを収集する場合も、このソリューションは変更を加えずに引き続き機能します。 このソリューションの詳細については、「[Activity Logs Analytics の監視ソリューション](activity-log-collect.md#activity-logs-analytics-monitoring-solution)」を参照してください。

## <a name="next-steps"></a>次のステップ

* [アクティビティ ログについて詳しく学習します](../../azure-resource-manager/management/view-activity-logs.md)
* [診断設定について詳しく学習します](diagnostic-settings.md)
