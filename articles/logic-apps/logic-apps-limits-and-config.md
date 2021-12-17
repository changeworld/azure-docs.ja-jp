---
title: 制約と構成の参考ガイド
description: Azure Logic Apps の制限と構成情報のリファレンス ガイド。
services: logic-apps
ms.suite: integration
ms.reviewer: rohithah, rarayudu, azla
ms.topic: reference
ms.date: 11/16/2021
ms.openlocfilehash: 0d8acc47ebe871a0888d0f7f32e63fa5784fcb14
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723458"
---
# <a name="limits-and-configuration-reference-for-azure-logic-apps"></a>Azure Logic Apps の制約と構成の参考文献

> Power Automate については、[Power Automate の制限と構成](/power-automate/limits-and-config)に関するページを確認してください。

この記事では、Azure Logic Apps と関連リソースの制限と構成情報について説明します。 ロジック アプリ ワークフローを作成するには、使用する状況、ソリューションの要件、必要な機能、ワークフローを実行する環境に適した種類の **Locig App** リソースを選択します。

> [!NOTE]
> 多くの制限は、Azure Logic Apps の実行に使用できる複数の環境にわたって同一ですが、違いがある場合は指摘されます。 

次の表で、標準の **Logic App (Consumption)** のリソースの種類と、**Logic App (Standard)** のリソースの種類の違いを要約します。 ロジック アプリ ワークフローを展開、ホスト、実行する際の、*シングルテナント* 環境と、*マルチテナント* および *統合サービス環境 (ISE)* との違いも分かります。

[!INCLUDE [Logic app resource type and environment differences](../../includes/logic-apps-resource-environment-differences-table.md)]

<a name="definition-limits"></a>

## <a name="workflow-definition-limits"></a>ワークフロー定義の制限

次の表に、1 つのワークフロー定義の値を示します。

| 名前 | 制限 | Notes |
| ---- | ----- | ----- |
| サブスクリプションごとの 1 リージョンあたりのワークフロー数 | 1,000 個のワークフロー ||
| ワークフローごとのトリガー数 | 10 個のトリガー | この制限は、デザイナーではなく、JSON ワークフロー定義 (コード ビューでも Azure Resource Manager (ARM) テンプレートでも) を操作する場合にのみ適用されます。 |
| ワークフローごとのアクション数 | 500 個のアクション | この制限を拡大するには、必要に応じて入れ子になったワークフローを使用します。 |
| アクションの入れ子の深さ | 8 個のアクション | この制限を拡大するには、必要に応じて入れ子になったワークフローを使用します。 |
| トリガーまたはアクション - 名前の最大長 | 80 文字 ||
| トリガーまたはアクション - 入力または出力の最大サイズ | 104,857,600 バイト <br>(105 MB) | シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| アクション - 入力と出力の最大結合サイズ | 209,715,200 バイト <br>(210 MB) | シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| 式の文字制限 | 8,192 文字 ||
| `description` - 最大長 | 256 文字 ||
| `parameters` - 項目の最大数 | 50 個のパラメーター ||
| `outputs` - 項目の最大数 | 10 個の出力 ||
| `trackedProperties` - 最大サイズ | 8,000 文字 ||
||||

<a name="run-duration-retention-limits"></a>

## <a name="run-duration-and-retention-history-limits"></a>実行継続時間と保持履歴の制限

次の表に、1 つのワークフロー実行の値を示します。

| 名前 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|--------------|---------------|---------------------------------|-------|
| ストレージでの実行履歴の保持期間 | 90 日間 | 90 日間 <br>(既定値)。 | 366 日間 | 実行の開始後、ワークフローの実行履歴をストレージに保持する時間。 <p><p>**注**: ワークフローの実行の継続時間が保持期間の制限を超えると、その実行はストレージ内の実行履歴から削除されます。 保持期間の制限に達した直後に実行が削除されない場合、実行は 7 日以内に削除されます。 <p><p>実行が完了したかタイムアウトしたかに関係なく、実行履歴の保持期間は、常に、実行の開始時刻と、ワークフロー設定に指定された現在の制限である [ **[実行履歴の保持期間 (日数)]**](#change-retention) を使用して計算されます。 前の制限に関係なく、現在の制限は、保持期間の計算に常に使用されます。 <p><p>詳細については、「[実行継続時間とストレージでの履歴の保持期間を変更する](#change-retention)」を参照してください。 |
| 実行継続時間 | 90 日間 | - ステートフル ワークフロー: 90 日間 <br>(既定値)。 <p><p>- ステートレス ワークフロー: 5 分間 <br>(既定値)。 | 366 日間 | タイムアウトを強制する前にワークフローの実行を継続できる時間。 <p><p>実行継続時間は、実行の開始時刻と、ワークフロー設定 [ **[実行履歴の保持期間 (日数)]**](#change-duration) に指定された制限を使用してその開始時に計算されます。 <p>**重要**: [実行期間] の値は常に、[ストレージでの実行履歴の保持期間] の値以下になるようにしてください。 そうしないと、関連付けられているジョブが完了する前に実行履歴が削除される可能性があります。 <p><p>詳細については、「[実行継続時間とストレージでの履歴の保持期間を変更する](#change-duration)」を参照してください。 |
| 繰り返し間隔 | - 最小: 1 秒 <p><p>- 最大: 500 日間 | - 最小: 1 秒 <p><p>- 最大: 500 日間 | - 最小: 1 秒 <p><p>- 最大: 500 日間 ||
||||||

<a name="change-duration"></a>
<a name="change-retention"></a>

### <a name="change-run-duration-and-history-retention-in-storage"></a>実行継続時間とストレージでの履歴の保持期間を変更する

デザイナーで、同じ設定を使用して、ワークフローを実行し、実行履歴をストレージに保持できる最大日数を制御します。

* マルチテナント サービスの場合、90 日間の既定制限は最大制限と同じです。 この値は減らすことしかできません。

* シングルテナント サービスでは、90 日に設定されている既定の上限を延長、短縮できます。 詳細については、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。

* 統合サービス環境の場合、90 日間の既定制限を減らすことも増やすこともできます。

たとえば、保持期間の制限を 90 日間から 30 日間に減らしたとします。 実行履歴から 60 日前の実行が削除されます。 保有期間を 30 日間から 60 日間に増やした場合、20 日前の実行はさらに 40 日間実行履歴に残ります。

> [!IMPORTANT]
> 実行の継続時間が現在の実行履歴の保持期間の制限を超えると、その実行はストレージ内の実行履歴から削除されます。 実行履歴が失われないようにするには、保持期間の制限を、実行の可能な最長期間よりも "*常に*" 多くします。

これらのプロパティの既定値または現在の制限を変更するには、次の手順を実行します。

#### <a name="portal-multi-tenant-service"></a>[ポータル (マルチテナント サービス)](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) の検索ボックスで、 **[ロジック アプリ]** を検索して選択します。

1. ロジック アプリ デザイナーでロジック アプリを見つけて開きます。

1. ロジック アプリのメニューで、 **[ワークフロー設定]** を選択します。

1. **[ランタイム オプション]** で、 **[実行履歴の保持期間 (日数)]** リストから **[カスタム]** を選択します。

1. スライダーをドラッグして、目的の日数に変更します。

1. 操作が完了したら、 **[ワークフロー設定]** のツールバーで、 **[保存]** を選択します。

#### <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

Azure Resource Manager テンプレートを使用する場合、この設定はワークフローのリソース定義内にプロパティとして表示されます。これについては、「[Microsoft.Logic ワークフロー テンプレートのリファレンス](/azure/templates/microsoft.logic/workflows)」を参照してください。

```json
{
   "name": "{logic-app-name}",
   "type": "Microsoft.Logic/workflows",
   "location": "{Azure-region}",
   "apiVersion": "2019-05-01",
   "properties": {
      "definition": {},
      "parameters": {},
      "runtimeConfiguration": {
         "lifetime": {
            "unit": "day",
            "count": {number-of-days}
         }
      }
   }
}
```
---

<a name="concurrency-looping-and-debatching-limits"></a>
<a name="looping-debatching-limits"></a>

## <a name="looping-concurrency-and-debatching-limits"></a>ループ、コンカレンシー、および分割処理の制限

次の表に、1 つのワークフロー実行の値を示します。

### <a name="loop-actions"></a>ループ アクション

<a name="for-each-loop"></a>

#### <a name="for-each-loop"></a>For each ループ

**For each** ループの値の一覧を次の表に示します。

| 名前 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|--------------|---------------|---------------------------------|-------|
| 配列項目 | 100,000 項目 | - ステートフル ワークフロー: 100,000 項目 <br>(既定値)。 <p><p>- ステートレス ワークフロー: 100 項目 <br>(既定値)。 | 100,000 項目 | **For each** ループで処理できる配列項目の数。 <p><p>さらに大きな配列にフィルターを適用するには、[クエリ アクション](logic-apps-perform-data-operations.md#filter-array-action)を使用できます。 <p><p>シングルテナント サービスで既定の制限を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| コンカレント反復 | コンカレンシーがオフ: 20 <p><p>コンカレンシーがオン: <p>- 既定:20 <br>- 最小:1 <br>- 最大:50 | コンカレンシーがオフ: 20 <br>(既定値)。 <p><p>コンカレンシーがオン: <p><p>- 既定:20 <br>- 最小:1 <br>- 最大:50 | コンカレンシーがオフ: 20 <p><p>コンカレンシーがオン: <p>- 既定:20 <br>- 最小:1 <br>- 最大:50 | 同時に (つまり、並列で) 実行できる **For each** ループ イテレーションの数。 <p><p>マルチテナント サービスでこの値を変更するには、「[**For each** のコンカレンシーを変更する](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)」または「[**For each** ループを順次実行する](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)」を参照してください。 <p><p>シングルテナント サービスで既定の制限を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
||||||

<a name="until-loop"></a>

#### <a name="until-loop"></a>Until ループ

**Until** ループの値の一覧を次の表に示します。

| 名前 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|--------------|---------------|---------------------------------|-------|
| イテレーション | - 既定:60 <br>- 最小:1 <br>- 最大:5,000 | ステートフル ワークフロー: <p><p>- 既定:60 <br>- 最小:1 <br>- 最大:5,000 <p><p>ステートレス ワークフロー: <p><p>- 既定:60 <br>- 最小:1 <br>- 最大:100 | - 既定:60 <br>- 最小:1 <br>- 最大:5,000 | ワークフロー実行中に **Until** ループで実行できるサイクル数。 <p><p>マルチテナント サービスでこの値を変更するには、**Until** ループ図形で **[制限の変更]** を選択し、 **[カウント]** プロパティの値を指定します。 <p><p>シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| タイムアウト | 既定: PT1H (1 時間) | ステートフル ワークフロー: PT1H (1 時間) <p><p>ステートレス ワークフロー: PT5M (5 分間) | 既定: PT1H (1 時間) | **Until** ループを終了するまでに実行できる時間であり、[ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)で指定されます。 タイムアウト値は、ループのサイクルごとに評価されます。 ループ内のアクションがタイムアウト制限より長くなる場合、現在のサイクルは停止しません。 ただし、制限の条件が満たされていないため、次のサイクルは開始しません。 <p><p>マルチテナント サービスでこの値を変更するには、**Until** ループ図形で **[制限の変更]** を選択し、 **[タイムアウト]** プロパティの値を指定します。 <p><p>シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
||||||

<a name="concurrency-debatching"></a>

### <a name="concurrency-and-debatching"></a>コンカレンシーと分割処理

| 名前 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|--------------|---------------|---------------------------------|-------|
| トリガー - 同時実行 | コンカレンシーがオフ: 無制限 <p><p>コンカレンシーがオン (元に戻せません): <p><p>- 既定:25 <br>- 最小:1 <br>- 最大:100 | コンカレンシーがオフ: 無制限 <p><p>コンカレンシーがオン (元に戻せません): <p><p>- 既定値: 100 <br>- 最小:1 <br>- 最大:100 | コンカレンシーがオフ: 無制限 <p><p>コンカレンシーがオン (元に戻せません): <p><p>- 既定:25 <br>- 最小:1 <br>- 最大:100 | 1 つのトリガーで同時に (つまり並列で) 開始できる同時実行の数。 <p><p>**注**: コンカレンシーが有効になっていると、[配列のバッチ解除](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)のために **SplitOn** 上限が 100 項目に下がります。 <p><p>マルチテナント サービスでこの値を変更するには、「[トリガーのコンカレンシーを変更する](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)」または「[インスタンスを順次トリガーする](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-trigger)」を参照してください。 <p><p>シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| 待機中の実行の最大数 | コンカレンシーがオフ: <p><p>- 最小: 1 回の実行 <p>- 最大: 50 回の実行 <p><p>コンカレンシーがオン: <p><p>- 最小: 10 回の実行 + 同時実行の数 <p>- 最大: 100 回の実行 | コンカレンシーがオフ: <p><p>- 最小: 1 回の実行 <br>(既定値)。 <p>- 最大: 50 回の実行 <br>(既定値)。 <p><p>コンカレンシーがオン: <p><p>- 最小: 10 回の実行 + 同時実行の数 <p>- 最大: 200 回の実行 <br>(既定値)。 | コンカレンシーがオフ: <p><p>- 最小: 1 回の実行 <p>- 最大: 50 回の実行 <p><p>コンカレンシーがオン: <p><p>- 最小: 10 回の実行 + 同時実行の数 <p>- 最大: 100 回の実行 | 現在のワークフロー インスタンスで既に最大数の同時インスタンスが実行されているときに実行を待機できるワークフロー インスタンスの数。 <p><p>マルチテナント サービスでこの値を変更するには、「[実行待機の制限を変更する](../logic-apps/logic-apps-workflow-actions-triggers.md#change-waiting-runs)」を参照してください。 <p><p>シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| **SplitOn** 項目数 | コンカレンシーがオフ: 100,000 項目 <p><p>コンカレンシーがオン: 100 項目 | コンカレンシーがオフ: 100,000 項目 <p><p>コンカレンシーがオン: 100 項目 | コンカレンシーがオフ: 100,000 項目 <br>(既定値)。 <p><p>コンカレンシーがオン: 100 項目 <br>(既定値)。 | 配列を返すトリガーの場合、**For each** ループを使用するのではなく、処理のために [配列項目を複数のワークフロー インスタンスに分割、つまり、バッチ解除する](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) **SplitOn** プロパティを使用する式を指定できます。 この式では、各配列項目のワークフロー インスタンスを作成および実行するために使用する配列を参照します。 <p><p>**注**: コンカレンシーが有効になっていると、**SplitOn** 上限が 100 項目に下がります。 |
||||||

<a name="throughput-limits"></a>

## <a name="throughput-limits"></a>スループットの制限

次の表に、1 つのワークフロー定義の値を示します。

| 名前 | マルチテナント | シングルテナント | Notes |
|------|--------------|---------------|-------|
| アクション - 5 分間のローリング間隔ごとの実行 | 既定: 100,000 回の実行 <br>- 高スループット モード: 300,000 回の実行 | なし | マルチテナント サービスでは、既定値をワークフローの最大値に引き上げることができます。 詳細については、現在プレビューの「[高スループット モードで実行する](#run-high-throughput-mode)」を参照してください。 または、必要に応じて[複数のワークフローにワークロードを分散](handle-throttling-problems-429-errors.md#logic-app-throttling)できます。 |
| アクション - 同時送信呼び出し | 最大 2,500 回の呼び出し | なし | 必要に応じて、同時要求数を削減するか期間を短縮できます。 |
| マネージド コネクタの調整 | 調整の制限はコネクタによって異なります | 調整の制限はコネクタによって異なります | マルチテナントの場合は、[各マネージド コネクタのテクニカル リファレンス ページ](/connectors/connector-reference/connector-reference-logicapps-connectors)を確認してください。 <p><p>コネクタの調整の処理について詳しくは、[調整の問題 ("429 - 要求が多すぎます" エラー) の処理](handle-throttling-problems-429-errors.md#connector-throttling)に関するページを参照してください。 |
| ランタイム エンドポイント - 同時受信呼び出し | 最大 1,000 回の呼び出し | なし | 必要に応じて、同時要求数を削減するか期間を短縮できます。 |
| ランタイム エンドポイント - 5 分あたりの読み取り呼び出し数  | 60,000 回の読み取り呼び出し | なし | この制限は、ワークフローの実行履歴から未加工の入力と出力を取得する呼び出しに適用されます。 必要に応じて複数のワークフローにワークロードを分散できます。 |
| ランタイム エンドポイント - 5 分あたりの起動呼び出し数 | 45,000 回の起動呼び出し | なし | 必要に応じて複数のワークフローにワークロードを分散できます。 |
| 5 分あたりのコンテンツのスループット | 600 MB | なし | 必要に応じて複数のワークフローにワークロードを分散できます。 |
|||||

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>高スループット モードで実行する

1 つのワークフロー定義の場合、5 分ごとに実行されるアクションの数には、[既定の制限](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)があります。 ワークフローの既定値を[最大値](../logic-apps/logic-apps-limits-and-config.md#throughput-limits) (既定値の 3 倍) に引き上げるには、プレビュー段階の高スループット モードを有効にできます。 または、必要に応じて[複数のワークフローにワークロードを分散](../logic-apps/handle-throttling-problems-429-errors.md#logic-app-throttling)できます。

#### <a name="portal-multi-tenant-service"></a>[ポータル (マルチテナント サービス)](#tab/azure-portal)

1. Azure portal のロジック アプリのメニューで **[設定]** の下から **[ワークフロー設定]** を選択します。

1. **[ランタイム オプション]** > **\[高スループット]** で設定を **[オン]** に変更します。

   ![[ワークフロー設定] と [高スループット] が [オン] に設定されている Azure portal のロジック アプリ メニューを示すスクリーンショット。](./media/logic-apps-limits-and-config/run-high-throughput-mode.png)

#### <a name="resource-manager-template"></a>[Resource Manager テンプレート](#tab/azure-resource-manager)

ロジック アプリをデプロイするために、ARM テンプレートのこの設定を有効にするには、ロジック アプリのリソース定義の `properties` オブジェクトで、`operationOptions` プロパティを `OptimizedForHighThroughput`に設定した `runtimeConfiguration` オブジェクトを追加します。

```json
{
   <template-properties>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            <logic-app-resource-definition-properties>,
            <logic-app-workflow-definition>,
            <more-logic-app-resource-definition-properties>,
            "runtimeConfiguration": {
               "operationOptions": "OptimizedForHighThroughput"
            }
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {},
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

ロジック アプリのリソース定義の詳細については、「[概要: Azure Resource Manager テンプレートを使用して Azure Logic Apps のデプロイを自動化する](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)」を参照してください。

---

### <a name="integration-service-environment-ise"></a>統合サービス環境 (ISE)

* [Developer ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):1 分あたり最大 500 回の実行が提供されますが、これらの点に注意してください。

  * この SKU は、必ず探索、実験、開発、またはテストにのみ使用し、運用環境やパフォーマンス テストには使用しないでください。 この SKU には、サービス レベル アグリーメント (SLA)、スケールアップ機能、リサイクル中の冗長性がありません。これは、遅延やダウンタイムが発生する可能性があることを意味します。

  * バックエンドの更新により、サービスが断続的に中断する場合があります。

* [Premium ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level):次の表では、この SKU のスループット制限について説明していますが、通常の処理でこれらの制限を超えるか、これらの制限を超える可能性のあるロード テストを実行するには、[Logic Apps チームに問い合わせ](mailto://logicappsemail@microsoft.com)の上、要件についてご相談ください。

  | 名前 | 制限 | Notes |
  |------|-------|-------|
  | 基本単位の実行制限 | インフラストラクチャの容量が 80% に達するとシステムが調整される | 1 分あたり最大 4,000 回のアクション実行 (1 か月あたり最大 1 億 6,000 万回のアクション実行) が提供されます |
  | スケール ユニットの実行制限 | インフラストラクチャの容量が 80% に達するとシステムが調整される | 各スケール ユニットでは、1 分あたり最大 2,000 回の追加アクション実行 (1 か月あたり最大 8,000 万回の追加アクション実行) を提供できます |
  | 追加できる最大スケール ユニット | 10 スケール ユニット | |
  ||||

<a name="gateway-limits"></a>

## <a name="data-gateway-limits"></a>データ ゲートウェイの制限

Azure Logic Apps では、オンプレミス データ ゲートウェイ経由での挿入や更新などの書き込み操作がサポートされています。 ただし、これらの操作には、[ペイロードのサイズに制限](/data-integration/gateway/service-gateway-onprem#considerations)があります。

<a name="variables-action-limits"></a>

## <a name="variables-action-limits"></a>変数アクションの制限

次の表に、1 つのワークフロー定義の値を示します。

| 名前 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|--------------|---------------|---------------------------------|-------|
| ワークフローごとの変数 | 250 個の変数 | 250 個の変数 <br>(既定値)。 | 250 個の変数 ||
| 変数 - コンテンツの最大サイズ | 104,857,600 文字 | ステートフル ワークフロー: 104,857,600 文字 <br>(既定値)。 <p><p>ステートレス ワークフロー: 1,024 文字 <br>(既定値)。 | 104,857,600 文字 | シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| 変数 (配列型) - 配列項目の最大数 | 100,000 項目 | 100,000 項目 <br>(既定値)。 | Premium SKU: 100,000 項目 <p><p>Developer SKU: 5,000 項目 | シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
||||||

<a name="http-limits"></a>

## <a name="http-request-limits"></a>HTTP 要求の制限

次の表に、1 回の受信または送信呼び出しの値を示します。

<a name="http-timeout-limits"></a>

### <a name="timeout-duration"></a>タイムアウト期間

既定では、HTTP アクションと APIConnection アクションは、[標準的な同期操作パターン](/azure/architecture/patterns/async-request-reply)に従いますが、Response アクションは "*同期操作パターン*" に従います。 マネージド コネクタ操作の中には、非同期呼び出しを行うものや webhook 要求をリッスンするものがあるため、これらの操作のタイムアウトは次の制限よりも長くなる場合があります。 詳細については、[各コネクタのテクニカル リファレンス ページ](/connectors/connector-reference/connector-reference-logicapps-connectors)のほか、[ワークフローのトリガーとアクション](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)に関するドキュメントも参照してください。

> [!NOTE]
> シングルテナント サービスでリソースの種類が **Logic App (Standard)** である場合、ステートレス ワークフローは "*同期的*" にのみ実行できます。

| 名前 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|--------------|---------------|---------------------------------|-------|
| 送信要求 | 120 秒 <br>(2 分) | 235 秒 <br>(3.9 分) <br>(既定値)。 | 240 秒 <br>(4 分) | 送信要求の例には、HTTP トリガーまたはアクションによる呼び出しが含まれます。 <p><p>**ヒント**: これよりも実行時間が長い操作には、[非同期ポーリング パターン](../logic-apps/logic-apps-create-api-app.md#async-pattern)または ["Until" ループ](../logic-apps/logic-apps-workflow-actions-triggers.md#until-action)を使用します。 [呼び出し可能なエンドポイント](logic-apps-http-endpoint.md)を持つ別のワークフローを呼び出すときにタイムアウト制限を回避するには、組み込みの Azure Logic Apps アクションを代わりに使用できます。これは、デザイナーの操作ピッカー ( **[ビルトイン]** の下) にあります。 <p><p>シングルテナント サービスで既定の制限を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| 受信要求 | 120 秒 <br>(2 分) | 235 秒 <br>(3.9 分) <br>(既定値)。 | 240 秒 <br>(4 分) | 受信要求の例には、要求トリガー、HTTP Webhook トリガー、および HTTP Webhook アクションによって受信された呼び出しが含まれます。 <p><p>**注**: 元の呼び出し元で応答を受け取るには、入れ子になった別のワークフローを呼び出す場合を除き、応答のすべてのステップが制限内に完了する必要があります。 詳細については、「[ロジック アプリを呼び出し、トリガーし、入れ子にする](../logic-apps/logic-apps-http-endpoint.md)」をご覧ください。 <p><p>シングルテナント サービスで既定の制限を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
||||||

<a name="message-size-limits"></a>

### <a name="messages"></a>メッセージ

| 名前 | チャンクが有効 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|------------------|--------------|-------------------------|---------------------------------|-------|
| コンテンツのダウンロード - 要求の最大数 | はい | 1,000 の要求 | 1,000 の要求 <br>(既定値)。 | 1,000 の要求 ||
| メッセージ サイズ | いいえ | 100 MB | 100 MB | 200 MB | この制限を回避するには、「[Handle large messages with chunking](../logic-apps/logic-apps-handle-large-messages.md)」をご覧ください。 ただし、一部のコネクタおよび API では、チャンク、または既定の制限さえもサポートされない場合があります。 <p><p>- AS2、X12、EDIFACT などのコネクタには、独自の [B2B メッセージ制限](#b2b-protocol-limits)があります。 <p>- ISE コネクタでは、ISE 以外のコネクタ制限ではなく、ISE の制限が使用されます。 <p><p>シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| メッセージ サイズ | はい | 1 GB | 1,073,741,824 バイト <br>(1 GB) <br>(既定値)。 | 5 GB | この制限は、チャンクをネイティブでサポートするアクションに適用されます。または、ランタイム構成でのチャンクを有効にできます。 <p><p>ISE を使用している場合、Azure Logic Apps エンジンはこの制限をサポートしますが、コネクタには、エンジンの制限などに応じて独自のチャンク制限があります。例については、[Azure Blob Storage コネクタの API リファレンス](/connectors/azureblob/)に関する記事を参照してください。 チャンクの詳細については、[チャンクを使用した大きいメッセージの処理](../logic-apps/logic-apps-handle-large-messages.md)に関する記事を参照してください。 <p><p>シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| コンテンツのチャンク サイズ | はい | コネクタごとに異なります | 52,428,800 バイト (52 MB) <br>(既定値)。 | コネクタごとに異なります | この制限は、チャンクをネイティブでサポートするアクションに適用されます。または、ランタイム構成でのチャンクを有効にできます。 <p><p>シングルテナント サービスで既定値を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
|||||||

### <a name="character-limits"></a>文字制限

| 名前 | 制限 | Notes |
|------|-------|-------|
| 式評価の制限 | 131,072 文字 | `@concat()`、`@base64()`、`@string()` 式をこの制限より長くすることはできません。 |
| 要求 URL の文字制限 | 16,384 文字 | |
||||

<a name="retry-policy-limits"></a>

### <a name="retry-policy"></a>再試行ポリシー

| 名前 | マルチ テナントの制限 | シングルテナントの制限 | Notes |
|------|--------------------|---------------------|-------|
| 再試行 | - 既定値: 4 回の試行 <br> - 最大: 90 回の試行 | - 既定値: 4 回の試行 | マルチテナント サービスで既定の制限を変更するには、[再試行ポリシー パラメーター](logic-apps-exception-handling.md#retry-policies)を使用します。 <p><p>シングルテナント サービスで既定の制限を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| 再試行間隔 | なし | 既定値: 7 秒 | マルチテナント サービスで既定の制限を変更するには、[再試行ポリシー パラメーター](logic-apps-exception-handling.md#retry-policies)を使用します。 <p><p>シングルテナント サービスで既定の制限を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| 再試行の最大間隔 | 既定値: 1 日 | 既定値: 1 時間 | マルチテナント サービスで既定の制限を変更するには、[再試行ポリシー パラメーター](logic-apps-exception-handling.md#retry-policies)を使用します。 <p><p>シングルテナント サービスで既定の制限を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
| 再試行の最小間隔 | 既定値: 5 秒 | 既定値: 5 秒 | マルチテナント サービスで既定の制限を変更するには、[再試行ポリシー パラメーター](logic-apps-exception-handling.md#retry-policies)を使用します。 <p><p>シングルテナント サービスで既定の制限を変更するには、「[シングルテナントの Azure Logic Apps でロジック アプリのホストとアプリの設定を編集する](edit-app-settings-host-settings.md)」を参照してください。 |
|||||

<a name="authentication-limits"></a>

### <a name="authentication-limits"></a>認証制限

次の表に、Request トリガーで開始され、[Azure Active Directory Open Authentication](../active-directory/develop/index.yml) (Azure AD OAuth) を有効にして Request トリガーへの受信呼び出しを承認するワークフローの値を示します。

| 名前 | 制限 | Notes |
| ---- | ----- | ----- |
| Azure AD 承認ポリシー | 5 個のポリシー | |
| 承認ポリシーごとの要求数 | 10 個のクレーム | |
| クレームの値 - 最大文字数 | 150 字 |
||||

<a name="switch-action-limits"></a>

## <a name="switch-action-limits"></a>アクションの制限を切り替える

次の表に、1 つのワークフロー定義の値を示します。

| 名前 | 制限 | Notes |
| ---- | ----- | ----- |
| アクションごとのケースの最大数 | 25 ||
||||

<a name="inline-code-action-limits"></a>

## <a name="inline-code-action-limits"></a>インライン コード アクションの制限

次の表に、1 つのワークフロー定義の値を示します。

| 名前 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|--------------|---------------|---------------------------------|-------|
| コード文字の最大数 | 1,024 文字 | 100,000 文字 | 1,024 文字 | 上限を増やすには、[Azure portal](create-single-tenant-workflows-azure-portal.md) で、または [Visual Studio Code と **Azure Logic Apps (Standard)** 拡張機能を使用して](create-single-tenant-workflows-visual-studio-code.md)、シングルテナントの Azure Logic Apps で実行する **Logic App (Standard)** リソースを作成します。 |
| コードを実行するための最長期間 | 5 秒 | 15 秒 | 1,024 文字 | 上限を増やすには、[Azure portal](create-single-tenant-workflows-azure-portal.md) で、または [Visual Studio Code と **Azure Logic Apps (Standard)** 拡張機能を使用して](create-single-tenant-workflows-visual-studio-code.md)、シングルテナントの Azure Logic Apps で実行する **Logic App (Standard)** リソースを作成します。 |
||||||

<a name="custom-connector-limits"></a>

## <a name="custom-connector-limits"></a>カスタム コネクタの制限

マルチテナントの Azure Logic Apps および統合サービス環境でのみ、既存の REST API または SOAP API のラッパーである[カスタム マネージド コネクタ](/connectors/custom-connectors)を作成して使用できます。 シングル テナントの Azure Logic Apps では、[カスタム組み込みコネクタ](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)のみを作成して使用できます。

カスタム コネクタの値の一覧を次の表に示します。

| 名前 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|--------------|---------------|---------------------------------|-------|
| カスタム コネクタ | Azure サブスクリプションあたり 1,000 | 無制限 | Azure サブスクリプションあたり 1,000 ||
| カスタム コネクタ - API の数 | SOAP ベース: 50 | 適用できません | SOAP ベース: 50 ||
| カスタム コネクタの 1 分あたりの要求数 | 接続ごとに、1 分あたり 500 の要求 | 実装に基づく | *カスタム コネクタ* ごとに、1 分あたり 2,000 の要求 ||
| [接続タイムアウト] | 2 分 | アイドル接続: <br>4 分 <p><p>アクティブ接続: <br>10 分 | 2 分 ||
||||||

詳細については、次のドキュメントを確認してください。

* [カスタム マネージド コネクタの概要](/connectors/custom-connectors)
* [組み込みコネクタの作成を有効にする - Visual Studio Code の Azure Logic Apps (Standard) 拡張機能](create-single-tenant-workflows-visual-studio-code.md#enable-built-in-connector-authoring)

<a name="managed-identity"></a>

## <a name="managed-identity-limits"></a>マネージド ID の制限

| 名前 | 制限 |
|------|-------|
| ロジック アプリあたりのマネージド ID | システム割り当て ID または 1 個のユーザー割り当て ID |
| Azure サブスクリプションにマネージド ID を持つ、リージョンごとのロジック アプリの数 | 1,000 |
|||

> [!NOTE] 
> Logic App (Standard) リソースでは、既定でシステム割り当てマネージド ID が自動的に有効になり、実行時に接続を認証します。 この ID は、接続の作成時に使用する認証資格情報または接続文字列とは異なります。 この ID を無効にした場合、接続は実行時に機能しません。 この設定を表示するには、ロジック アプリのメニューの **[設定]** で、 **[ID]** を選択します。

<a name="integration-account-limits"></a>

## <a name="integration-account-limits"></a>統合アカウントの制限

各 Azure サブスクリプションには、次の統合アカウントの制限があります。

* Azure リージョンごとに 1 つの [Free レベル](../logic-apps/logic-apps-pricing.md#integration-accounts)の統合アカウント このレベルは、米国西部や東南アジアなどの Azure のパブリック リージョンのみで利用できますが、[Azure China 21Vianet](/azure/china/overview-operations) または [Azure Government](../azure-government/documentation-government-welcome.md) で利用することはできません。

* [Developer SKU と Premium SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) の両方にわたる任意の[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 内の統合アカウントを含む、合計 1,000 個の統合アカウント。

* [Developer でも Premium でも](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)、各 ISE では追加コストなしで 1 つの統合アカウントを使用できます。ただし、含まれるアカウントの種類は ISE SKU によって異なります。 [コストを追加すれば](logic-apps-pricing.md#ise-pricing)その合計制限まで、ISE の統合アカウントを追加作成できます。

  | ISE SKU | 統合アカウントの制限 |
  |---------|----------------------------|
  | **Premium** | 合計 20 アカウント。追加コストなしで Standard アカウントが 1 つ含まれます。 この SKU では、[Standard](../logic-apps/logic-apps-pricing.md#integration-accounts) アカウントのみが与えられます。 Free または Basic アカウントは使用できません。 |
  | **開発者** | 合計 20 アカウント。[Free](../logic-apps/logic-apps-pricing.md#integration-accounts) アカウントが 1 つ含まれます (1 に制限)。 この SKU では、次のいずれかの組み合わせを利用できます。 <p>- Free アカウントが 1 つと最大 19 の [Standard](../logic-apps/logic-apps-pricing.md#integration-accounts) アカウント。 <br>- Free アカウントなしと最大 20 の Standard アカウント。 <p>Basic または追加の Free アカウントは許可されません。 <p><p>**重要**:[Developer SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) は、運用やパフォーマンス テストにではなく、実験、開発、テストに使用してください。 |
  |||

ISE の価格と課金のしくみについては、「[固定価格モデル](../logic-apps/logic-apps-pricing.md#ise-pricing)」を参照してください。 価格については、[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に関する記事を参照してください。

<a name="artifact-number-limits"></a>

### <a name="artifact-limits-per-integration-account"></a>統合アカウントごとのアーティファクトの制限

次の表に、各統合アカウント レベルに制限されるアーティファクト数の値を示します。 価格については、[Logic Apps の価格](https://azure.microsoft.com/pricing/details/logic-apps/)に関する記事を参照してください。 統合アカウントの価格と課金のしくみについては、[Logic Apps の価格モデル](../logic-apps/logic-apps-pricing.md#integration-accounts)に関する記事を参照してください。

> [!NOTE]
> Free レベルは、調査シナリオでのみ使用し、運用シナリオでは使用しないでください。 このレベルでは、スループットと使用率が制限され、サービス レベル アグリーメント (SLA) はありません。

| アーティファクト | Free | Basic | Standard |
|----------|------|-------|----------|
| EDI 取引契約 | 10 | 1 | 1,000 |
| EDI 取引先 | 25 | 2 | 1,000 |
| マップ | 25 | 500 | 1,000 |
| スキーマ | 25 | 500 | 1,000 |
| アセンブリ | 10 | 25 | 1,000 |
| 証明書 | 25 | 2 | 1,000 |
| バッチの構成 | 5 | 1 | 50 |
||||

<a name="artifact-capacity-limits"></a>

### <a name="artifact-capacity-limits"></a>アーティファクト容量制限

| アーティファクト | 制限 | Notes |
| -------- | ----- | ----- |
| アセンブリ | 8 MB | 2 MB を超えるファイルをアップロードするには、[Azure ストレージ アカウントと BLOB コンテナー](../logic-apps/logic-apps-enterprise-integration-schemas.md)を使用します。 |
| マップ (XSLT ファイル) | 8 MB | 2 MB を超えるファイルをアップロードするには、[Azure Logic Apps REST API - Maps](/rest/api/logic/maps/createorupdate) を使用します。 <p><p>**注**:マップで正常に処理できるデータまたはレコードの量は、Azure Logic Apps のメッセージ サイズとアクション タイムアウトの制限に基づいています。 たとえば、HTTP アクションを使用する場合、[HTTP メッセージ サイズとタイムアウトの制限](#http-limits)に基づき、マップでは、HTTP タイムアウトの制限内で操作が完了するのであれば、HTTP メッセージ サイズの制限までデータを処理できます。 |
| スキーマ | 8 MB | 2 MB を超えるファイルをアップロードするには、[Azure ストレージ アカウントと BLOB コンテナー](../logic-apps/logic-apps-enterprise-integration-schemas.md)を使用します。 |
||||

<a name="integration-account-throughput-limits"></a>

### <a name="throughput-limits"></a>スループットの制限

| ランタイム エンドポイント | Free | Basic | Standard | Notes |
|------------------|------|-------|----------|-------|
| 5 分あたりの読み取り呼び出し数 | 3,000 | 30,000 | 60,000 | この制限は、ロジック アプリの実行履歴から未加工の入力と出力を取得する呼び出しに適用されます。 必要に応じて複数のアカウントにワークロードを分散できます。 |
| 5 分あたりの起動呼び出し数 | 3,000 | 30,000 | 45,000 | 必要に応じて複数のアカウントにワークロードを分散できます。 |
| 5 分あたりの追跡呼び出し数 | 3,000 | 30,000 | 45,000 | 必要に応じて複数のアカウントにワークロードを分散できます。 |
| ブロックされた同時呼び出し数 | ～ 1,000 | ～ 1,000 | ～ 1,000 | すべての SKU で同じです。 必要に応じて、同時要求数を削減するか期間を短縮できます。 |
||||

<a name="b2b-protocol-limits"></a>

### <a name="b2b-protocol-as2-x12-edifact-message-size"></a>B2B プロトコル (AS2、X12、EDIFACT) のメッセージのサイズ

次の表に、B2B プロトコルに適用されるメッセージ サイズ制限を示します。

| 名前 | マルチテナント | シングルテナント | 統合サービス環境 | Notes |
|------|--------------|---------------|---------------------------------|-------|
| AS2 | v2 - 100 MB<br>v1 - 25 MB | 使用不可 | v2 - 200 MB <br>v1 - 25 MB | デコードおよびエンコードに適用 |
| X12 | 50 MB | 使用不可 | 50 MB | デコードおよびエンコードに適用 |
| EDIFACT | 50 MB | 使用不可 | 50 MB | デコードおよびエンコードに適用 |
||||

<a name="configuration"></a>
<a name="firewall-ip-configuration"></a>

## <a name="firewall-configuration-ip-addresses-and-service-tags"></a>ファイアウォールの構成:IP アドレスとサービス タグ

特定の IP アドレスへのトラフィックを制限する厳しいネットワーク要件やファイアウォールが環境に存在する場合、ロジック アプリ リソースが存在する Azure リージョンで Azure Logic Apps サービスまたはランタイムにより使用する [受信](#inbound)、[送信](#outbound) IP アドレス *両方* のアクセスを、その環境またはファイアウォールで許可する必要があります。 このアクセスを設定するために、[Azure Firewall 規則](../firewall/rule-processing.md)を作成できます。 同じリージョン内の *すべての* ロジック アプリは、同じ IP アドレス範囲を使用します。

> [!NOTE]
> [Power Automate](/power-automate/getting-started) を使用している場合、**HTTP** 要求や **HTTP + OpenAPI** などの一部のアクションは、Azure Logic Apps サービスを通じて直接実行され、ここに記載されている IP アドレスから取得されます。 Power Automate によって使用される IP アドレスについて詳しくは、[Power Automate の制限と構成](/power-automate/limits-and-config#ip-address-configuration)に関するページを参照してください。

たとえば、ロジック アプリを米国西部リージョンでデプロイしたと考えてください。 [HTTP トリガー、アクション](../connectors/connectors-native-http.md)などの組み込みトリガーおよびアクションによってロジック アプリで送受信する呼び出しをサポートするには、Azure Logic Apps サービスの受信 IP アドレス *と* 送信 IP アドレスのうち米国西部リージョンに存在するもの *すべて* を、ファイアウォールで許可する必要があります。

ワークフローで Office 365 Outlook コネクタや SQL コネクタなどの [マネージド コネクタ](../connectors/managed.md)が使用されている場合、または [カスタム コネクタ](/connectors/custom-connectors/)が使用されている場合、そのファイアウォールでは、ロジック アプリの Azure リージョン内にある "*すべて*" の [マネージド コネクタ送信 IP アドレス](/connectors/common/outbound-ip-addresses)に対するアクセスも許可する必要があります。 [Azure のオンプレミス データ ゲートウェイ リソース](logic-apps-gateway-connection.md)によってオンプレミスのリソースにアクセスするカスタム コネクタをワークフローで使用する場合、対応する "[*マネージド コネクタ*" の送信 IP アドレス](/connectors/common/outbound-ip-addresses)のアクセスを許可するように、インストールされたゲートウェイをセットアップする必要があります。 ゲートウェイでの通信設定の設定の詳細については、次のトピックを参照してください。

* [オンプレミス データ ゲートウェイの通信設定を調整する](/data-integration/gateway/service-gateway-communication)
* [オンプレミス データ ゲートウェイのプロキシ設定を構成する](/data-integration/gateway/service-gateway-proxy)

> [!IMPORTANT]
> [21Vianet によって運営される Microsoft Azure](/azure/china/) を使用している場合、マネージド コネクタとカスタム コネクタには予約済みまたは固定の IP アドレスがありません。 そのため、このクラウドでこれらのコネクタを使用するロジック アプリについては、ファイアウォール規則を設定できません。 Azure Logic Apps のサービス IP については、[21Vianet によって運営される Azure 用のドキュメント バージョン](https://docs.azure.cn/en-us/logic-apps/logic-apps-limits-and-config#firewall-ip-configuration)に関する記事を確認してください。

<a name="ip-setup-considerations"></a>

### <a name="firewall-ip-configuration-considerations"></a>ファイアウォールの IP 構成に関する考慮事項

IP アドレスを使用してファイアウォールを設定する前に、次の考慮事項を確認してください。

* ロジック アプリ ワークフローをシングルテナントの Azure Logic Apps で実行する場合は、接続の完全修飾ドメイン名 (FQDN) を確認する必要があります。 詳しくは、これらのトピックの中の対応セクションをご確認ください。

  * [シングルテナント ロジック アプリのファイヤーウォールのアクセス許可 - Azure portal](create-single-tenant-workflows-azure-portal.md#firewall-setup)
  * [シングルテナント ロジック アプリのファイヤーウォールのアクセス許可 - Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md#firewall-setup)

* ロジック アプリ ワークフローを[統合サービス環境 (ISE)](connect-virtual-network-vnet-isolated-environment-overview.md) で実行する場合は、[これらのポートも開く](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#network-ports-for-ise)必要があります。

* 作成するセキュリティ規則を簡素化するために、必要に応じて、各リージョンに IP アドレス プレフィックスを指定するのではなく、[サービス タグ](../virtual-network/service-tags-overview.md)を使用することもできます。 これらのタグは、Logic Apps サービスが使用可能なリージョン全体で動作します。

  * **LogicAppsManagement**:Logic Apps サービスの受信 IP アドレスのプレフィックスを表します。

  * **LogicApps**:Logic Apps サービスの送信 IP アドレスのプレフィックスを表します。

  * **AzureConnectors**:Logic Apps サービスへの受信 Webhook コールバックと、Azure Storage や Azure Event Hubs などの各サービスへの送信呼び出しを行う、マネージド コネクタの IP アドレス プレフィックスを表します。

* [ファイアウォールとファイアウォール規則](../storage/common/storage-network-security.md)を使用する Azure ストレージ アカウントへのアクセスに関する問題がロジック アプリにある場合は、[アクセスを有効にするためのさまざまなその他のオプション](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)があります。

  たとえば、ロジック アプリは、ファイアウォール規則を使用し、同じリージョンに存在するストレージ アカウントに直接アクセスすることはできません。 ただし、[ご利用のリージョンでマネージド コネクタに送信 IP アドレス](/connectors/common/outbound-ip-addresses)を許可する場合、Azure Table Storage または Azure Queue Storage コネクタの使用時を除き、ロジック アプリでは別のリージョンにあるストレージ アカウントにアクセスできます。 Table Storage または Queue Storage にアクセスするには、代わりに HTTP のトリガーとアクションを利用できます。 その他のオプションについては、[ファイアウォールの内側でストレージ アカウントにアクセスする](../connectors/connectors-create-api-azureblobstorage.md#access-storage-accounts-behind-firewalls)方法に関する記事を参照してください。

<a name="inbound"></a>

### <a name="inbound-ip-addresses"></a>受信 IP アドレス

このセクションには、Azure Logic Apps サービスのみの受信 IP アドレスが一覧表示されています。 Azure Government をご使用の場合は、「[Azure Government - 受信 IP アドレス](#azure-government-inbound)」を参照してください。

> [!TIP]
> セキュリティ規則を作成する際の複雑さを軽減するために、必要に応じて、各リージョンの受信 Logic Apps IP アドレスのプレフィックスを指定するのではなく、[サービス タグ](../virtual-network/service-tags-overview.md) **LogicAppsManagement** を使用することもできます。
>
> 一部のマネージド コネクタでは、Azure Logic Apps サービスへの受信 Webhook コールバックが行われます。 これらのマネージド コネクタの場合は、リージョンごとに受信マネージド コネクタの IP アドレス プレフィックスを指定する代わりに、必要に応じてこれらのマネージド コネクタ用の **AzureConnectors** サービス タグを使用することができます。 
> これらのタグは、Logic Apps サービスが使用可能なリージョン全体で動作します。
>
> 次のコネクタは、Logic Apps サービスへの受信 Webhook コールバックを行います。
>
> Adobe Creative Cloud、Adobe Sign、Adobe Sign Demo、Adobe Sign Preview、Adobe Sign Stage、Microsoft Azure Sentinel、Business Central、Calendly、Common Data Service、DocuSign、DocuSign Demo、Dynamics 365 for Fin & Ops、LiveChat、Office 365 Outlook、Outlook.com、Parserr、SAP*、Shifts for Microsoft Teams、Teamwork Projects、Typeform
>
> \* **SAP**:返される呼び出し元は、デプロイ環境がマルチテナント Azure か ISE かによって異なります。 マルチテナント環境では、オンプレミス データ ゲートウェイによって Logic Apps サービスへのコールバックが行われます。 ISE では、SAP コネクタによって Logic Apps サービスへのコールバックが行われます。

<a name="multi-tenant-inbound"></a>

#### <a name="multi-tenant--single-tenant---inbound-ip-addresses"></a>マルチテナントとシングルテナント - 受信 IP アドレス

| リージョン | IP |
|--------|----|
| オーストラリア東部 | 13.75.153.66、104.210.89.222、104.210.89.244、52.187.231.161 |
| オーストラリア南東部 | 13.73.115.153、40.115.78.70、40.115.78.237、52.189.216.28 |
| ブラジル南部 | 191.235.86.199、191.235.95.229、191.235.94.220、191.234.166.198 |
| ブラジル南東部 | 20.40.32.59、20.40.32.162、20.40.32.80、20.40.32.49 |
| カナダ中部 | 13.88.249.209、52.233.30.218、52.233.29.79、40.85.241.105 |
| カナダ東部 | 52.232.129.143、52.229.125.57、52.232.133.109、40.86.202.42 |
| インド中部 | 52.172.157.194、52.172.184.192、52.172.191.194、104.211.73.195 |
| 米国中部 | 13.67.236.76、40.77.111.254、40.77.31.87、104.43.243.39 |
| 東アジア | 168.63.200.173、13.75.89.159、23.97.68.172、40.83.98.194 |
| 米国東部 | 137.135.106.54、40.117.99.79、40.117.100.228、137.116.126.165 |
| 米国東部 2 | 40.84.25.234、40.79.44.7、40.84.59.136、40.70.27.253 |
| フランス中部 | 52.143.162.83、20.188.33.169、52.143.156.55、52.143.158.203 |
| フランス南部 | 52.136.131.145、52.136.129.121、52.136.130.89、52.136.131.4 |
| ドイツ北部 | 51.116.211.29、51.116.208.132、51.116.208.37、51.116.208.64 |
| ドイツ中西部 | 51.116.168.222、51.116.171.209、51.116.233.40、51.116.175.0 |
| 東日本 | 13.71.146.140、13.78.84.187、13.78.62.130、13.78.43.164 |
| 西日本 | 40.74.140.173、40.74.81.13、40.74.85.215、40.74.68.85 |
| JIO インド西部 | 20.193.206.48、20.193.206.49、20.193.206.50、20.193.206.51 |
| 韓国中部 | 52.231.14.182、52.231.103.142、52.231.39.29、52.231.14.42 |
| 韓国南部 | 52.231.166.168、52.231.163.55、52.231.163.150、52.231.192.64 |
| 米国中北部 | 168.62.249.81、157.56.12.202、65.52.211.164、65.52.9.64 |
| 北ヨーロッパ | 13.79.173.49、52.169.218.253、52.169.220.174、40.112.90.39 |
| ノルウェー東部 | 51.120.88.93、51.13.66.86、51.120.89.182、51.120.88.77 |
| 南アフリカ北部 | 102.133.228.4、102.133.224.125、102.133.226.199、102.133.228.9 |
| 南アフリカ西部 | 102.133.72.190、102.133.72.145、102.133.72.184、102.133.72.173 |
| 米国中南部 | 13.65.98.39、13.84.41.46、13.84.43.45、40.84.138.132 |
| インド南部 | 52.172.9.47、52.172.49.43、52.172.51.140、104.211.225.152 |
| 東南アジア | 52.163.93.214、52.187.65.81、52.187.65.155、104.215.181.6 |
| スイス北部 | 51.103.128.52、51.103.132.236、51.103.134.138、51.103.136.209 |
| スイス西部 | 51.107.225.180、51.107.225.167、51.107.225.163、51.107.239.66 |
| アラブ首長国連邦中部 | 20.45.75.193、20.45.64.29、20.45.64.87、20.45.71.213 |
| アラブ首長国連邦北部 | 20.46.42.220、40.123.224.227、40.123.224.143、20.46.46.173 |
| 英国南部 | 51.140.79.109、51.140.78.71、51.140.84.39、51.140.155.81 |
| 英国西部 | 51.141.48.98、51.141.51.145、51.141.53.164、51.141.119.150 |
| 米国中西部 | 52.161.26.172、52.161.8.128、52.161.19.82、13.78.137.247 |
| 西ヨーロッパ | 13.95.155.53、52.174.54.218、52.174.49.6 |
| インド西部 | 104.211.164.112、104.211.165.81、104.211.164.25、104.211.157.237 |
| 米国西部 | 52.160.90.237、138.91.188.137、13.91.252.184、157.56.160.212 |
| 米国西部 2 | 13.66.224.169、52.183.30.10、52.183.39.67、13.66.128.68 |
| 米国西部 3| 20.150.172.240、20.150.172.242、20.150.172.243、20.150.172.241 |
|||

<a name="azure-government-inbound"></a>

#### <a name="azure-government---inbound-ip-addresses"></a>Azure Government - 受信 IP アドレス

| Azure Government リージョン | IP |
|-------------------------|----|
| US Gov アリゾナ | 52.244.67.164、52.244.67.64、52.244.66.82 |
| US Gov テキサス | 52.238.119.104、52.238.112.96、52.238.119.145 |
| US Gov バージニア州 | 52.227.159.157、52.227.152.90、23.97.4.36 |
| US DoD Central | 52.182.49.204、52.182.52.106 |
|||

<a name="outbound"></a>

### <a name="outbound-ip-addresses"></a>送信 IP アドレス

このセクションには、Azure Logic Apps サービスの送信 IP アドレスが一覧表示されています。 Azure Government をご使用の場合は、「[Azure Government - 送信 IP アドレス](#azure-government-outbound)」を参照してください。 ワークフローで Office 365 Outlook コネクタや SQL コネクタなどの [マネージド コネクタ](../connectors/managed.md)が使用されている場合、または [カスタム コネクタ](/connectors/custom-connectors/)が使用されている場合、そのファイアウォールでは、ロジック アプリの Azure リージョン内にある "*すべて*" の [マネージド コネクタ送信 IP アドレス](/connectors/common/outbound-ip-addresses)に対するアクセスも許可する必要があります。 [Azure のオンプレミス データ ゲートウェイ リソース](logic-apps-gateway-connection.md)によってオンプレミスのリソースにアクセスするカスタム コネクタをワークフローで使用する場合、対応する "[*マネージド コネクタ*" の送信 IP アドレス](/connectors/common/outbound-ip-addresses)のアクセスを許可するように、インストールされたゲートウェイをセットアップする必要があります。 ゲートウェイでの通信設定の設定の詳細については、次のトピックを参照してください。

* [オンプレミス データ ゲートウェイの通信設定を調整する](/data-integration/gateway/service-gateway-communication)
* [オンプレミス データ ゲートウェイのプロキシ設定を構成する](/data-integration/gateway/service-gateway-proxy)

> [!TIP]
> セキュリティ規則を作成する際の複雑さを軽減するために、必要に応じて、各リージョンの送信 Logic Apps IP アドレスのプレフィックスを指定するのではなく、[サービス タグ](../virtual-network/service-tags-overview.md) **LogicApps** を使用することもできます。 必要に応じて、各リージョンに対して送信マネージド コネクタの IP アドレス プレフィックスを指定するのでなく、**AzureConnectors** サービス タグを、Microsoft Azure Storage または Azure Event Hubs などのそれぞれのサービスに対する送信呼び出しを行うマネージド コネクタに対して使用することもできます。 これらのタグは、Logic Apps サービスが使用可能なリージョン全体で動作します。

<a name="multi-tenant-outbound"></a>

#### <a name="multi-tenant--single-tenant---outbound-ip-addresses"></a>マルチテナントとシングルテナント - 送信 IP アドレス

| リージョン | Logic Apps IP |
|--------|---------------|
| オーストラリア東部 | 13.75.149.4、104.210.91.55、104.210.90.241、52.187.227.245、52.187.226.96、52.187.231.184、52.187.229.130、52.187.226.139 |
| オーストラリア南東部 | 13.73.114.207、13.77.3.139、13.70.159.205、52.189.222.77、13.77.56.167、13.77.58.136、52.189.214.42、52.189.220.75 |
| ブラジル南部 | 191.235.82.221、191.235.91.7、191.234.182.26、191.237.255.116、191.234.161.168、191.234.162.178、191.234.161.28、191.234.162.131 |
| ブラジル南東部 | 20.40.32.81、20.40.32.19、20.40.32.85、20.40.32.60、20.40.32.116、20.40.32.87、20.40.32.61、20.40.32.113 |
| カナダ中部 | 52.233.29.92、52.228.39.244、40.85.250.135、40.85.250.212、13.71.186.1、40.85.252.47、13.71.184.150 |
| カナダ東部 | 52.232.128.155、52.229.120.45、52.229.126.25、40.86.203.228、40.86.228.93、40.86.216.241、40.86.226.149、40.86.217.241 |
| インド中部 | 52.172.154.168、52.172.186.159、52.172.185.79、104.211.101.108、104.211.102.62、104.211.90.169、104.211.90.162、104.211.74.145 |
| 米国中部 | 13.67.236.125、104.208.25.27、40.122.170.198、40.113.218.230、23.100.86.139、23.100.87.24、23.100.87.56、23.100.82.16 |
| 東アジア | 13.75.94.173、40.83.127.19、52.175.33.254、40.83.73.39、65.52.175.34、40.83.77.208、40.83.100.69、40.83.75.165 |
| 米国東部 | 13.92.98.111、40.121.91.41、40.114.82.191、23.101.139.153、23.100.29.190、23.101.136.201、104.45.153.81、23.101.132.208 |
| 米国東部 2 | 40.84.30.147、104.208.155.200、104.208.158.174、104.208.140.40、40.70.131.151、40.70.29.214、40.70.26.154、40.70.27.236 |
| フランス中部 | 52.143.164.80、52.143.164.15、40.89.186.30、20.188.39.105、40.89.191.161、40.89.188.169、40.89.186.28、40.89.190.104 |
| フランス南部 | 52.136.132.40、52.136.129.89、52.136.131.155、52.136.133.62、52.136.139.225、52.136.130.144、52.136.140.226、52.136.129.51 |
| ドイツ北部 | 51.116.211.168、51.116.208.165、51.116.208.175、51.116.208.192、51.116.208.200、51.116.208.222、51.116.208.217、51.116.208.51 |
| ドイツ中西部 | 51.116.233.35、51.116.171.49、51.116.233.33、51.116.233.22、51.116.168.104、51.116.175.17、51.116.233.87、51.116.175.51 |
| 東日本 | 13.71.158.3、13.73.4.207、13.71.158.120、13.78.18.168、13.78.35.229、13.78.42.223、13.78.21.155、13.78.20.232 |
| 西日本 | 40.74.140.4、104.214.137.243、138.91.26.45、40.74.64.207、40.74.76.213、40.74.77.205、40.74.74.21、40.74.68.85 |
| JIO インド西部 | 20.193.206.128、20.193.206.129、20.193.206.130、20.193.206.131、20.193.206.132、20.193.206.133、20.193.206.134、20.193.206.135 |
| 韓国中部 | 52.231.14.11、52.231.14.219、52.231.15.6、52.231.10.111、52.231.14.223、52.231.77.107、52.231.8.175、52.231.9.39 |
| 韓国南部 | 52.231.204.74、52.231.188.115、52.231.189.221、52.231.203.118、52.231.166.28、52.231.153.89、52.231.155.206、52.231.164.23 |
| 米国中北部 | 168.62.248.37、157.55.210.61、157.55.212.238、52.162.208.216、52.162.213.231、65.52.10.183、65.52.9.96、65.52.8.225 |
| 北ヨーロッパ | 40.113.12.95、52.178.165.215、52.178.166.21、40.112.92.104、40.112.95.216、40.113.4.18、40.113.3.202、40.113.1.181 |
| ノルウェー東部 | 51.120.88.52、51.120.88.51、51.13.65.206、51.13.66.248、51.13.65.90、51.13.65.63、51.13.68.140、51.120.91.248 |
| 南アフリカ北部 | 102.133.231.188、102.133.231.117、102.133.230.4、102.133.227.103、102.133.228.6、102.133.230.82、102.133.231.9、102.133.231.51 |
| 南アフリカ西部 | 102.133.72.98、102.133.72.113、102.133.75.169、102.133.72.179、102.133.72.37、102.133.72.183、102.133.72.132、102.133.75.191 |
| 米国中南部 | 104.210.144.48、13.65.82.17、13.66.52.232、23.100.124.84、70.37.54.122、70.37.50.6、23.100.127.172、23.101.183.225 |
| インド南部 | 52.172.50.24、52.172.55.231、52.172.52.0、104.211.229.115、104.211.230.129、104.211.230.126、104.211.231.39、104.211.227.229 |
| 東南アジア | 13.76.133.155、52.163.228.93、52.163.230.166、13.76.4.194、13.67.110.109、13.67.91.135、13.76.5.96、13.67.107.128 |
| スイス北部 | 51.103.137.79、51.103.135.51、51.103.139.122、51.103.134.69、51.103.138.96、51.103.138.28、51.103.136.37、51.103.136.210 |
| スイス西部 | 51.107.239.66、51.107.231.86、51.107.239.112、51.107.239.123、51.107.225.190、51.107.225.179、51.107.225.186、51.107.225.151、51.107.239.83 |
| アラブ首長国連邦中部 | 20.45.75.200、20.45.72.72、20.45.75.236、20.45.79.239、20.45.67.170、20.45.72.54、20.45.67.134、20.45.67.135 |
| アラブ首長国連邦北部 | 40.123.230.45、40.123.231.179、40.123.231.186、40.119.166.152、40.123.228.182、40.123.217.165、40.123.216.73、40.123.212.104 |
| 英国南部 | 51.140.74.14、51.140.73.85、51.140.78.44、51.140.137.190、51.140.153.135、51.140.28.225、51.140.142.28、51.140.158.24 |
| 英国西部 | 51.141.54.185、51.141.45.238、51.141.47.136、51.141.114.77、51.141.112.112、51.141.113.36、51.141.118.119、51.141.119.63 |
| 米国中西部 | 52.161.27.190、52.161.18.218、52.161.9.108、13.78.151.161、13.78.137.179、13.78.148.140、13.78.129.20、13.78.141.75、13.71.199.128 - 13.71.199.159 |
| 西ヨーロッパ | 40.68.222.65、40.68.209.23、13.95.147.65、23.97.218.130、51.144.182.201、23.97.211.179、104.45.9.52、23.97.210.126、13.69.71.160、13.69.71.161、13.69.71.162、13.69.71.163、13.69.71.164、13.69.71.165、13.69.71.166、13.69.71.167 |
| インド西部 | 104.211.164.80、104.211.162.205、104.211.164.136、104.211.158.127、104.211.156.153、104.211.158.123、104.211.154.59、104.211.154.7 |
| 米国西部 | 52.160.92.112、40.118.244.241、40.118.241.243、157.56.162.53、157.56.167.147、104.42.49.145、40.83.164.80、104.42.38.32、13.86.223.0、13.86.223.1、13.86.223.2、13.86.223.3、13.86.223.4、13.86.223.5 |
| 米国西部 2 | 13.66.210.167、52.183.30.169、52.183.29.132、13.66.210.167、13.66.201.169、13.77.149.159、52.175.198.132、13.66.246.219 |
| 米国西部 3 | 20.150.181.32、20.150.181.33、20.150.181.34、20.150.181.35、20.150.181.36、20.150.181.37、20.150.181.38、20.150.173.192 |
|||

<a name="azure-government-outbound"></a>

#### <a name="azure-government---outbound-ip-addresses"></a>Azure Government - 送信 IP アドレス

| リージョン | Logic Apps IP |
|--------|---------------|
| US DoD Central | 52.182.48.215、52.182.92.143 |
| US Gov アリゾナ | 52.244.67.143、52.244.65.66、52.244.65.190 |
| US Gov テキサス | 52.238.114.217、52.238.115.245、52.238.117.119 |
| US Gov バージニア州 | 13.72.54.205、52.227.138.30、52.227.152.44 |
|||

## <a name="next-steps"></a>次のステップ

* [初めてのロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)方法を確認する
* [一般的な例とシナリオ](../logic-apps/logic-apps-examples-and-scenarios.md)を確認する
