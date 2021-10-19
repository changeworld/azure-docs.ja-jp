---
title: FHIR サービスの診断設定を表示し、有効にする-Azure の医療 Api
description: この記事では、FHIR サービスで診断設定を有効にし、いくつかのサンプルクエリで監査ログを確認する方法について説明します。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: how-to
ms.date: 10/12/2021
ms.author: zxue
ms.openlocfilehash: 7574d4abf20e07019016796e937cf80c7d915128
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130187760"
---
# <a name="view-and-enable-diagnostic-settings-in-the-fhir-service"></a>FHIR サービスの診断設定を表示し、有効にする

診断ログへのアクセスは、すべての医療サービスにとって不可欠です。 健康保険の携行性とアカウンタビリティ法 (HIPAA) などの規制要件への準拠は、必須です。 この記事では、Azure の医療 Api 内の FHIR サービスで診断ログの設定を選択する方法について説明します。 また、これらのログのサンプルクエリをいくつか確認します。

> [!IMPORTANT]
> Azure の医療 Api サービスは、現在プレビューの段階です。 [Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー段階、または一般公開されていない Azure の機能に適用される法律条項が含まれます。

## <a name="steps-to-enable-diagnostic-logs"></a>診断ログを有効にする手順

1. Azure portal で FHIR サービスを選択します。

2. **[監視]** で **[診断設定]** を選択します。

3. [ **+ 診断設定の追加**] を選択します。

   [![Azure portal の [診断設定] ページのスクリーンショット。 ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png) ](media/diagnostic-logs/fhir-diagnostic-settings-screen.png#lightbox)

4. **診断設定の名前** を入力します。
 
   [![監査ログを有効または無効にするための、宛先の詳細とチェックボックスのスクリーンショット。 ](media/diagnostic-logs/fhir-diagnostic-settings-add.png) ](media/diagnostic-logs/fhir-diagnostic-settings-add.png#lightbox)

5. ログへのアクセスに使用する方法を選択します。

   - **Log Analytics ワークスペースへの送信** は Azure Monitor の Log Analytics ワークスペースにログとメトリックを送信するためのものです。 このオプションを選択する前に、Log Analytics ワークスペースを作成する必要があります。
   
   - **ストレージアカウントへのアーカイブ** は、監査または手動検査用です。 使用するストレージアカウントが既に作成されている必要があります。 Retention オプションは、ストレージアカウントにのみ適用されます。 保持ポリシーの範囲は 1 ~ 365 日です。 保持ポリシーを適用せず、データを永続的に保持しない場合は、リテンション期間 (日数) を0に設定します。

   - **Event hub へのストリーミング** は、サードパーティのサービスまたはカスタム分析ソリューションによる取り込み用です。 このオプションを構成する前に、イベントハブの名前空間とイベントハブのポリシーを作成する必要があります。
   
   - Azure でサポートされているパートナーソリューションを有効にしている場合は、**パートナーソリューションへの送信ソリューション** を選択する必要があります。 詳細については、「 [パートナーのソリューションを使用した Azure の拡張](../../partner-solutions/overview.md)」を参照してください。

6. [ **Auditlogs**] を選択します。

7. **[保存]** を選択します。

> [!NOTE]
> 最初のログが [Log Analytics] ワークスペースに表示されるまでに最大15分かかることがあります (この方法を選択した場合)。 FHIR サービスがあるリソースグループまたはサブスクリプションから別のリソースグループまたはサブスクリプションに移動された場合は、移動の完了後に設定を更新します。


## <a name="diagnostic-log-details"></a>診断ログの詳細

この時点で、FHIR サービスは診断ログに次のフィールドを返します。

|フィールド名|Type|Notes|
|----------|----|-----|
|`CallerIdentity` |動的|Id 情報を格納している汎用プロパティバッグ。|
|`CallerIdentityIssuer` | String| 発行者。|
|`CallerIdentityObjectId` | String| オブジェクト ID。|
|`CallerIPAddress` | String| 呼び出し元の IP アドレス。|
|`CorrelationId` | String| 関連付け ID。|
|`FhirResourceType` | String| 操作が実行されたリソースの種類。|
|`LogCategory` | String| ログのカテゴリ。 (この記事では、を返し `AuditLogs` ます)。|
|`Location` | String| 要求を処理したサーバーの場所。 たとえば、 `South Central US`と指定します。|
|`OperationDuration` | int| この要求の完了に要した時間 (秒単位)。|
|`OperationName` | String| 操作の種類。 たとえば、`update` や `search-type` などです。|
|`RequestUri` | String| 要求 URI。|
|`ResultType` | String| ログの状態。 使用できる値は `Started` 、、 `Succeeded` 、または `Failed` です。|
|`StatusCode` | int| HTTP 状態コード。 たとえば、 `200`と指定します。|
|`TimeGenerated` | DateTime| イベントの日付と時刻。|
|`Properties` | String| のプロパティ `FhirResourceType` 。|
|`SourceSystem` | String| ソースシステム。常に `Azure` この場合はです。|
|`TenantId` | String | テナント ID。|
|`Type` | String| ログの種類。常に `MicrosoftHealthcareApisAuditLog` この場合はです。|
|`_ResourceId` | String| リソースに関する詳細。|       
        
## <a name="sample-queries"></a>サンプル クエリ

これらの基本的な Application Insights クエリを使用して、ログデータを探索できます。

- 次のクエリを実行して、 *最新の 100* のログを表示します。

  `Insights
  MicrosoftHealthcareApisAuditLogs
  | limit 100`

- 次のクエリを実行して、操作を *Fhir リソースの種類* 別にグループ化します。

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | summarize count() by FhirResourceType`

- 次のクエリを実行して、 *失敗した* すべての結果を取得します。

  `Insights
  MicrosoftHealthcareApisAuditLogs 
  | where ResultType == "Failed"`   

## <a name="conclusion"></a>まとめ

診断ログにアクセスできることは、サービスを監視し、コンプライアンス レポートを提供するために不可欠です。 この記事では、FHIR サービスのこれらのログを有効にする方法について説明しました。 

> [!NOTE]
> Azure の医療 Api サービスが一般公開されると、メトリックが追加されます。

## <a name="next-steps"></a>次の手順

FHIR サービスの概要については、以下を参照してください。

>[!div class="nextstepaction"]
>[FHIR サービスとは何ですか?](overview.md)   

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
