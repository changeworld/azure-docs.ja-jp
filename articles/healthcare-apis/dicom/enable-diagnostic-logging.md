---
title: DICOM サービスで診断ログを有効にする - Azure Healthcare API
description: この記事では、DICOM サービスで診断ログを有効にする方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 51a162ccbad5813a559cf27c26adcd964adeaf07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779419"
---
# <a name="enable-diagnostic-logging-in-the-dicom-service"></a>DICOM サービスで診断ログを有効にする

> [!IMPORTANT]
> Azure Healthcare API は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、DICOM サービスで診断ログを有効にし、それらのログのサンプル クエリを確認できるようにする方法について説明します。 診断ログへのアクセスは、規制要件への準拠が必須であるすべての医療サービスにとって不可欠です。 診断ログを有効にする DICOM サービスの機能は、Azure portal の[診断設定](../../azure-monitor/essentials/diagnostic-settings.md)です。 

## <a name="enable-audit-logs"></a>監査ログを有効にする

1. 診断ログ DICOM サービスを有効にするには、Azure portal で DICOM サービスを選択します。
2. **[アクティビティ ログ]** ブレードを選択し、 **[診断設定]** を選択します。

   [![Azure アクティビティ ログ。](media/dicom-activity-log.png)](media/dicom-activity-log.png#lightbox)

3. **[+ 診断設定の追加]** を選択します。

   [![診断設定を追加します。](media/add-diagnostic-settings.png)](media/add-diagnostic-settings.png#lightbox)

4. **診断設定の名前** を入力します。

   [![診断設定を構成します。](media/configure-diagnostic-settings.png)](media/configure-diagnostic-settings.png#lightbox)

5. 診断ログにアクセスする **[カテゴリ]** と **[宛先]** の詳細を選択します。

   * Azure Monitor の **[Log Analytics ワークスペースに送信する]** 。 このオプションを選択する前に、Log Analytics ワークスペースを作成する必要があります。 プラットフォーム ログの詳細については、「[Azure プラットフォーム ログの概要](../../azure-monitor/essentials/platform-logs-overview.md)」を参照してください。
   * 監査や手動での検査のために、**ストレージ アカウントにアーカイブ** します。 使用するストレージ アカウントは既に作成済みである必要があります。
   * サード パーティのサービスやカスタム分析ソリューションで取り込むために、**イベント ハブにストリーム配信** します。 この手順を構成する前に、イベント ハブの名前空間とイベント ハブのポリシーを作成する必要があります。
   * Azure のパートナー組織として使用している **パートナー ソリューションに送信します**。 潜在的なパートナー統合の詳細については、[Azure パートナー ソリューションのドキュメント](../../partner-solutions/overview.md)を参照してください。

     サポートされているメトリックの詳細については、「[Azure Monitor のサポートされるメトリック](.././../azure-monitor/essentials/metrics-supported.md)」を参照してください。

6. **[保存]** を選択します。


   > [!Note] 
   > 最初のログが Log Analytics に表示されるまでには、最大で 15 分かかることがあります。 また、DICOM サービスを、あるリソース グループまたはサブスクリプションから別のリソース グループまたはサブスクリプションに移動した場合は、移動が完了した後で設定を更新します。 
 
   診断ログの使用方法の詳細については、[Azure リソース ログのドキュメント](../../azure-monitor/essentials/platform-logs-overview.md)を参照してください

## <a name="audit-log-details"></a>監査ログの詳細

DICOM サービスは、監査ログに次のフィールドを返します。 

|フィールド名  |Type  |メモ  |
|---------|---------|---------|
|correlationId|String|関連付け ID
|category|String|ログ カテゴリ (現在 'AuditLogs' があります) 
|operationName|String|操作の種類についての説明 (例: 取得、ストア、クエリなど) 
|time|DateTime|イベントの日付と時刻。 
|resourceId|String| リソースへの Azure パス。
|identity|動的|ID 情報を含む汎用プロパティ バッグ (現在、DICOM には適用されません)。
|callerIpAddress|String|呼び出し元の IP アドレス。
|場所|String|要求を処理したサーバーの場所。
|uri|String|要求 URI。
|resultType|String| 現在使用可能な値は、Started、Succeeded、または Failed。
|resultSignature|int|HTTP 状態コード (200 など)。
|properties|String|リソースの種類、リソース名、サブスクリプション ID、監査アクションなどのプロパティについて説明します。
|type|String|ログの種類 (この場合、常に MicrosoftHealthcareApisAuditLog)。
|レベル|String|ログ レベル (情報、エラー)。
|operationVersion|String| 現在空です。 API のバージョンを表示するために使用されます。


## <a name="sample-queries"></a>サンプル クエリ

ログ データを調査するために使用できるいくつかの基本的な Application Insights クエリを次に示します。

**最新の 100 件** のログを表示するには、次のクエリを実行します。

```Application Insights
MicrosoftHealthcareApisAuditLogs
| limit 100
```

**DICOM リソースの種類** で操作をグループ化するには、次のクエリを実行します。

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| summarize count() by DICOMResourceType
```

すべての **失敗した結果** を取得するには、次のクエリを実行します。

```Application Insights
MicrosoftHealthcareApisAuditLogs 
| where ResultType == "Failed" 
```

## <a name="conclusion"></a>まとめ

診断ログにアクセスできることは、サービスを監視し、コンプライアンス レポートを提供するために不可欠です。 DICOM サービスを使用すると、診断ログを使用してこれらのアクションを実行できます。 

## <a name="next-steps"></a>次のステップ
この記事では、DICOM サービスの監査ログを有効にする方法について説明しました。 Azure アクティビティ ログの詳細については、次を参照してください。
 
>[!div class="nextstepaction"]
>[Azure アクティビティ ログのイベント スキーマ](.././../azure-monitor/essentials/activity-log-schema.md)
