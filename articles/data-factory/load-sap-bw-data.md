---
title: SAP Business Warehouse からデータを読み込む
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory または Synapse Analytics を使用して SAP Business Warehouse (BW) からデータをコピーします
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/09/2021
ms.openlocfilehash: 70db6de930ad60da87180fdf28555e542a458ecf
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824608"
---
# <a name="copy-data-from-sap-business-warehouse-with-azure-data-factory-or-synapse-analytics"></a>Azure Data Factory または Synapse Analytics を使用して SAP Business Warehouse からデータをコピーする
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory または Synapse Analytics パイプラインを使用して SAP Business Warehouse (BW) からオープン ハブ経由で Azure Data Lake Storage Gen2 にデータをコピーする方法について説明します。 同様のプロセスを使用して、他の[サポートされるシンク データ ストア](copy-activity-overview.md#supported-data-stores-and-formats)にデータをコピーできます。

> [!TIP]
> SAP BW オープン ハブ統合や差分抽出フローなど、SAP BW からのデータのコピーに関する一般的な情報については、「[SAzure Data Factory を使用するオープン ハブを介して SAP Business Warehouse からデータをコピーする](connector-sap-business-warehouse-open-hub.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

- **Azure Data Factory または Synapse ワークスペース**: ない場合は、[データ ファクトリを作成する](quickstart-create-data-factory-portal.md#create-a-data-factory)手順、または [Synapse ワークスペースを作成する](../synapse-analytics/quickstart-create-workspace.md)手順に従ってください。

- **宛先の種類が "データベース テーブル" となっている SAP BW オープン ハブ宛先 (OHD)** : OHD の作成方法、またはサービスとの統合用に OHD が正しく構成されていることを確認する方法については、この記事の「[SAP BW オープン ハブ宛先の構成](#sap-bw-open-hub-destination-configurations)」セクションを参照してください。

- **SAP BW ユーザーには次のアクセス許可が必要です**

  - リモート関数呼び出し (RFC) と SAP BW に対する認可。
  - **S_SDSAUTH** 認可オブジェクトの "実行" アクティビティに対するアクセス許可。

- **[セルフホステッド統合ランタイム (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime)と SAP .NET Connector 3.0**。 次の設定手順に従います。

  1. セルフホステッド統合ランタイム バージョン 3.13 以降をインストールして登録します。 (このプロセスについては、この記事の後半で説明します。)

  2. [Microsoft .NET 3.0 用の 64 ビット SAP Connector](https://support.sap.com/en/product/connectors/msnet.html) をSAP の Web サイトからダウンロードし、それをセルフホステッド IR と同じコンピューターにインストールします。 次の図に示すように、インストールの間の **[Optional setup steps]\(省略可能なセットアップ手順\)** ダイアログ ボックスでは、 **[Install Assemblies to GAC]\(アセンブリを GAC にインストールする\)** を必ず選択します。

     :::image type="content" source="media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png" alt-text="SAP .NET Connector の設定ダイアログ ボックス":::

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>SAP BW オープン ハブからの完全コピーを行う

Azure portal で、サービスに移動します。 **[Open Azure Data Factory Studio]\(Azure Data Factory Studio を開く\)** または **[Synapse Studio を開く]** タイルで **[開く]** を選択して、別のタブでサービス UI を開きます。

1. ホーム ページで、 **[取り込み]** を選択し、データ コピー ツールを開きます。

2. **[プロパティ]** ページで、 **[タスクの種類]** の **[組み込みコピー タスク]** を選択して、 **[Task cadence or task schedule]\(タスクの周期またはタスクのスケジュール\)** の **[Run once now]\(今すぐ 1 度だけ実行する\)** を選択し、 **[次へ]** を選択します。

3. **[ソース データ ストア]** ページで、 **[+ 新しい接続]** を選択します。 コネクタ ギャラリーから **[SAP BW Open Hub]\(SAP BW オープン ハブ\)** を選択し、 **[続行]** を選択します。 コネクタをフィルター処理するには、検索ボックスに「**SAP**」と入力します。

4. **[New connection (SAP BW Open Hub)]\(新しい接続 (SAP BW オープン ハブ)\)** ページで、以下の手順に従って新しい接続を作成します。

   1. **[Connect via integration runtime]\(統合ランタイム経由で接続\)** の一覧から、既存のセルフホステッド IR を選択します。 または、まだない場合は作成を選択します。

      新しいセルフホステッド IR を作成するには、 **[+ 新規]** を選択してから、 **[Self-hosted]\(セルフホステッド\)** を選択します。 **[名前]** を入力してから、 **[次へ]** を選択します。 **[高速セットアップ]** を選択して現在のコンピューターにインストールするか、提供される **手動セットアップ** の手順に従います。

      「[前提条件](#prerequisites)」で説明したように、セルフホステッド IR が実行されているのと同じコンピューターに、Microsoft .NET 3.0 用の SAP Connector がインストールされていることを確認します。

   2. SAP BW の **[サーバー名]** 、 **[システム番号]** 、 **[クライアント ID]** 、 **[言語]** (**EN** 以外の場合)、 **[ユーザー名]** 、および **[パスワード]** を入力します。

   3. **[テスト接続]** を選択して設定を検証し、 **[作成]** を選択します。

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png" alt-text="SAP BW オープン ハブのリンクされたサービス ページを作成する":::

   4. **[ソース データ ストア]** ページで、 **[接続]** ブロックで新しく作成した接続を選択します。

   5. オープン ハブ宛先の選択のセクションで、SAP BW で利用できるオープン ハブ宛先を参照します。 各行の末尾にある [プレビュー] ボタンを選択すると、各宛先のデータをプレビューできます。 データ コピー元の OHD を選択し、 **[次へ]** を選択します。
   
   :::image type="content" source="./media/load-sap-bw-data/source-data-store-page.png" alt-text="[ソース データ ストア] ページを示すスクリーンショット。":::

5. 必要がある場合は、フィルターを指定します。 OHD に、1 つの要求 ID を使用した 1 回のデータ転送プロセス (DTP) 実行からのデータのみが含まれている場合、または DTP が確実に完了していてそのデータをコピーする場合は、 **[詳細]** セクションの **[Exclude Last Request]\(最後の要求を除外する\)** チェック ボックスをオフにします。 **[Preview data]\(データのプレビュー\)** ボタンを選択すると、データをプレビューできます。

   これらの設定について詳しくは、この記事の「[SAP BW オープン ハブ宛先の構成](#sap-bw-open-hub-destination-configurations)」をご覧ください。 **[次へ]** を選択します。

   :::image type="content" source="media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png" alt-text="SAP BW オープン ハブ フィルターの構成":::

6. **[Destination data store]\(コピー先データ ストア\)** ページで **[+ 新しい接続]**  >  **[Azure Data Lake Storage Gen2]**  >  **[続行]** の順に選択します。

7. **[New connection (Azure Data Lake Storage Gen2)]\(新しい接続 (Azure Data Lake Storage Gen2)\)** ページで、以下の手順に従って接続を作成します。
   1. **[名前]** ドロップダウン リストから、Data Lake Storage Gen2 対応のアカウントを選択します。
   2. **[作成]** を選択して接続を作成します。

   :::image type="content" source="media/load-sap-bw-data/create-adls-gen2-linked-service.png" alt-text="ADLS Gen2 のリンクされたサービス ページを作成する":::

8. **[Destination data store]\(コピー先データ ストア\)** ページの **[接続]** セクションで新しく作成された接続を選択して、出力フォルダー名として「**copyfromopenhub**」と入力します。 **[次へ]** を選択します。

   :::image type="content" source="./media/load-sap-bw-data/destination-data-store-page.png" alt-text="[Destination data store]\(コピー先データ ストア\) ページを示すスクリーンショット。":::

9. **[File format setting]\(ファイル形式設定\)** ページで、 **[次へ]** を選択して、既定の設定を使用します。

    :::image type="content" source="media/load-sap-bw-data/specify-sink-format.png" alt-text="シンク形式の指定ページ":::

10. **[設定]** ページで、 **[タスク名]** を指定し、 **[詳細]** を展開します。 **[Degree of copy parallelism]\(コピーの並列度\)** に値を入力し (5 など)、SAP BW から並列で読み込みます。 **[次へ]** を選択します。

    :::image type="content" source="media/load-sap-bw-data/configure-copy-settings.png" alt-text="コピー設定の構成":::

11. **[Summary]\(概要\)** ページで設定を確認します。 **[次へ]** を選択します。

    :::image type="content" source="./media/load-sap-bw-data/summary-page.png" alt-text="[Summary]\(概要\) ページを示すスクリーンショット。":::

12. **[Deployment]\(デプロイ\)** ページで **[Monitor]\(監視\)** を選択してパイプラインを監視します。

13. ページの左側の **[Monitor]\(監視\)** タブが自動的に選択されることに注意してください。 **[パイプライン実行]** ページの **[パイプライン名]** 列のリンクを使用して、アクティビティの詳細を表示したりパイプラインを再実行したりできます。

14. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、 **[パイプライン名]** 列にあるリンクを選択します。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 パイプラインの実行ビューに戻るには、上部の **[すべてのパイプラインの実行]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。

    :::image type="content" source="media/load-sap-bw-data/activity-monitoring.png" alt-text="アクティビティ監視画面":::

15. 各コピー アクティビティの実行状況の詳細を監視するには、 **[Details]\(詳細\)** リンク (アクティビティ監視ビューの各コピー アクティビティと同じ行にある眼鏡アイコン) を選択します。 使用可能な詳細には、ソースからシンクにコピーされたデータ量、データのスループット、実行ステップと期間、使用された構成が含まれます。

    :::image type="content" source="media/load-sap-bw-data/activity-monitoring-details.png" alt-text="アクティビティの監視の詳細":::

16. 各コピー アクティビティの **最大要求 ID** を表示するには、アクティビティ監視ビューに戻り、各コピー アクティビティと同じ行にある **[出力]** を選択します。

    :::image type="content" source="media/load-sap-bw-data/activity-output.png" alt-text="アクティビティ出力画面":::

    :::image type="content" source="media/load-sap-bw-data/activity-output-details.png" alt-text="アクティビティ出力詳細ビュー":::

## <a name="incremental-copy-from-sap-bw-open-hub"></a>SAP BW オープン ハブからの増分コピー

> [!TIP]
> SAP BW オープン ハブ コネクタによる SAP BW からの増分データのコピー方法については、[SAP BW オープン ハブ コネクタの差分抽出フロー](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)に関する記事を参照してください。 この記事は、基本的なコネクタ構成の理解にも役立ちます。

次に、SAP BW オープン ハブからの増分コピーの構成に進みましょう。

増分コピーでは、**要求 ID** に基づく "高基準値" メカニズムが使用されます。 その ID は、DTP によって SAP BW オープン ハブ宛先で自動的に生成されます。 次の図はこのワークフローを示したものです。

:::image type="content" source="media/load-sap-bw-data/incremental-copy-workflow.png" alt-text="増分コピーのワークフローのフロー チャート":::

ホーム ページの **[その他を参照]** セクションで、 **[パイプラインのテンプレート]** を選択して、組み込みのテンプレートを使用します。

1. **SAP BW** を検索して、**Incremental copy from SAP BW to Azure Data Lake Storage Gen2 (SAP BW から Azure Data Lake Storage Gen2 への増分コピー)** テンプレートを選択します。 このテンプレートでは、データが Azure Data Lake Storage Gen2 にコピーされます。 他のシンクの種類のコピーにも、同様のワークフローを使用できます。

2. テンプレートのメイン ページで、次の 3 つの接続を選択または作成した後、ウィンドウの右下隅にある **[このテンプレートを使用する]** を選択します。

   - **Azure Blob Storage**: このチュートリアルでは、"*コピーされた最大の要求 ID* " である高基準値の格納に Azure Blob Storage を使用します。
   - **SAP BW オープン ハブ**: これは、データのコピー元のソースです。 詳しい構成については、前の完全コピーのチュートリアルをご覧ください。
   - **Azure Data Lake Storage Gen2**: これは、データのコピー先のシンクです。 詳しい構成については、前の完全コピーのチュートリアルをご覧ください。

   :::image type="content" source="media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png" alt-text="SAP BW テンプレートからの増分コピー":::

3. このテンプレートでは、次の 3 つのアクティビティを使用してパイプラインが生成され、成功時にそれらがチェーンされます: "*参照*"、"*データ コピー*"、"*Web*"。

   パイプラインの **[パラメーター]** タブに移動します。指定する必要のあるすべての構成が表示されます。

   :::image type="content" source="media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png" alt-text="SAP BW からの増分コピーの構成":::

   - **SAPOpenHubDestinationName**: データのコピー元のオープン ハブ テーブル名を指定します。

   - **Data_Destination_Container**:データのコピー先の Azure Data Lake Storage Gen2 コンテナーを指定します。 コンテナーが存在しない場合、実行時にコピー アクティビティによって作成されます。
  
   - **Data_Destination_Directory**:データのコピー先の Azure Data Lake Storage Gen2 コンテナーの下のフォルダー パスを指定します。 パスが存在しない場合、実行時にコピー アクティビティによってパスが作成されます。
  
   - **HighWatermarkBlobContainer**:高基準値を格納するコンテナーを指定します。

   - **HighWatermarkBlobDirectory**:高基準値を格納するコンテナーの下のフォルダー パスを指定します。

   - **HighWatermarkBlobName**: `requestIdCache.txt` など、高基準値を格納する BLOB 名を指定します。 BLOB ストレージで、HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName の対応するパスに移動します (例: *container/path/requestIdCache.txt*)。 コンテンツ 0 の BLOB を作成します。

      :::image type="content" source="media/load-sap-bw-data/blob.png" alt-text="BLOB コンテンツ":::

   - **LogicAppURL**: このテンプレートで、Azure Logic Apps を呼び出す WebActivity を使用して、BLOB ストレージに高基準値を設定します。 または、Azure SQL Database を使用してそれを格納できます。 ストアド プロシージャ アクティビティを使用して、値を更新します。

      次の図で示すように、最初にロジック アプリを作成する必要があります。 次に、 **[HTTP POST の URL]** に貼り付けます。

      :::image type="content" source="media/load-sap-bw-data/logic-app-config.png" alt-text="ロジック アプリの構成":::

      1. Azure Portal にアクセスします。 新しい **Logic Apps** サービスを選択します。 **[+ 空のロジック アプリ]** を選択して、**Logic Apps デザイナー** に移動します。

      2. **[HTTP 要求の受信時]** のトリガーを選択します。 HTTP 要求本文を次のように指定します。

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. **[BLOB の作成]** アクションを追加します。 **[フォルダー パス]** と **[BLOB 名]** には、前に *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* および *HighWatermarkBlobName* で構成したものと同じ値を使用します。

      4. **[保存]** を選択します。 その後、 **[HTTP POST の URL]** の値をコピーして、パイプラインで使用します。

4. パイプライン パラメーターを指定した後、 **[デバッグ]**  >  **[完了]** を選択し、実行を起動して構成を検証します。 または、 **[公開]** を選択してすべての変更を公開し、 **[トリガーの追加]** を選択して実行します。

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW オープン ハブ宛先の構成

このセクションでは、SAP BW オープン ハブ コネクタを使用してデータをコピーするための、SAP BW 側の構成について説明します。

### <a name="configure-delta-extraction-in-sap-bw"></a>SAP BW での差分抽出の構成

履歴コピーと増分コピーの両方、または増分コピーのみが必要な場合は、SAP BW で差分抽出を構成します。

1. オープン ハブ宛先を作成します。 SAP トランザクション RSA1 に OHD を作成できます。これにより、必要な変換とデータ転送プロセスが自動的に作成されます。 次の設定を使用します。

   - **[ObjectType]\(オブジェクトの種類\)** : 任意のオブジェクト型を使用できます。 ここでは、**InfoCube** を例として使用します。
   - **[送信先の種類]** : **[Database Table]\(データベース テーブル\)** を選択します。
   - **[Key of the Table]\(テーブルのキー\)** : **[Technical Key]\(テクニカル キー\)** を選択します。
   - **抽出**: **[Keep Data and Insert Records into Table]\(データを保持し、テーブルにレコードを挿入する\)** を選択します。

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-delta.png" alt-text="SAP BW OHD 差分抽出の作成ダイアログ ボックス":::

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-delta2.png" alt-text="SAP BW OHD 差分 2 抽出の作成ダイアログ ボックス":::

   DTP の SAP ワーク プロセスの並列実行の数を増やすことができます。

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-delta3.png" alt-text="DTP の並列処理の数を選択できる並列処理の設定を示すスクリーンショット。":::

2. プロセス チェーン内で DTP のスケジュールを設定します。

   キューブの差分 DTP は、必要な行が圧縮されていない場合にのみ動作します。 オープン ハブ テーブルへの DTP の前に、BW キューブの圧縮が実行されていないことを確認します。 これを最も簡単に行う方法は、DTP を既存のプロセス チェーンに統合することです。 次の例では、(OHD への) DTP は、"*調整*" (集約ロールアップ) ステップと "*折りたたみ*" (キューブ圧縮) ステップの間のプロセス チェーンに挿入されます。

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-process-chain.png" alt-text="SAP BW プロセス チェーンの作成のフロー チャート":::

### <a name="configure-full-extraction-in-sap-bw"></a>SAP BW での完全抽出の構成

差分抽出に加えて、同じ SAP BW InfoProvider の完全抽出を行うことができます。 これは通常、増分なしで完全コピーを行う場合や、[差分抽出を再同期](#resync-delta-extraction)する場合に適用されます。

同じ OHD に対して複数の DTP を持つことはできません。 そのため、差分抽出の前に追加の OHD を作成する必要があります。

:::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-full.png" alt-text="完全な SAP BW OHD を作成する":::

完全読み込み OHD の場合は、差分抽出以外のオプションを選択します。

- OHD で: **[Extraction]\(抽出\)** オプションを **[Delete Data and Insert Records]\(データの削除とレコードの挿入\)** に設定します。 そうしないと、BW プロセス チェーンで DTP を繰り返すときにデータが何度も抽出されます。

- DTP で: **[Extraction Mode]\(抽出モード\)** を **[Full]\(完全\)** に設定します。 次の図に示すように、OHD が作成された直後に、自動的に作成された DTP を **[Delta]\(差分\)** から **[Full]\(完全\)** に変更する必要があります。

   :::image type="content" source="media/load-sap-bw-data/create-sap-bw-ohd-full2.png" alt-text="&quot;[Full]\(完全\)&quot; 抽出用に構成された SAP BW OHD 作成ダイアログ ボックス":::

- BW オープン ハブ コネクタで、 **[Exclude last request]\(最後の要求を除外する\)** をオフにします。 そうしないと、何も抽出されません。

通常は、完全 DTP を手動で実行します。 または、完全 DTP のプロセス チェーンを作成できます。 通常は、既存のプロセス チェーンから独立した別のチェーンです。 いずれの場合も、"*コピーを使用して抽出を始める前に、DTP が完了していることを確認します*"。 そうしないと、部分的なデータのみがコピーされます。

### <a name="run-delta-extraction-the-first-time"></a>差分抽出の初回実行

最初の差分抽出は、技術的には "*完全抽出*" です。 既定の SAP BW オープン ハブ コネクタでは、データをコピーするときに、前回の要求が除外されます。 最初の差分抽出では、後続の DTP によって別の要求 ID でテーブルに差分データが生成されるまで、コピー アクティビティによってデータは抽出されません。 このシナリオを回避するには 2 つの方法があります。

- 最初の差分抽出で **[Exclude last request]\(最後の要求を除外する\)** オプションをオフにします。 初回の差分抽出を始める前に、最初のデルタ DTP が完了したことを確認します。
-  次のセクションで説明するように、デルタ抽出の再同期の手順を使用します。

### <a name="resync-delta-extraction"></a>差分抽出を再同期する

以下のシナリオでは SAP BW キューブ内のデータが変更されますが、差分 DTP では考慮されません。

- SAP BW の (任意のフィルター条件を使用した行の) 選択的削除
- SAP BW 要求の (問題のある要求の) 削除

SAP オープン ハブ宛先は、(2015 年以降のすべて SAP BW サポート パッケージでは) データ マートで制御されるデータ ターゲットではありません。 そのため、OHD 内のデータを変更せずに、キューブからデータを削除することができます。 その後、サービスでキューブのデータを再同期する必要があります。

1. (SAP の完全 DTP を使用して) サービス内で完全抽出を実行します。
2. 差分 DTP のオープン ハブ テーブル内の行をすべて削除します。
3. 差分 DTP の状態を **フェッチ済み** に設定します。

この後、後続のすべての差分 DTP と差分抽出は、意図したとおりに機能します。

差分 DTP の状態を **フェッチ済み** に設定するには、次のオプションを使用して手動で差分 DTP を実行できます。

*No Data Transfer; Delta Status in Source:Fetched (データ転送なし。ソース内の差分状態: フェッチ済み)*

## <a name="next-steps"></a>次のステップ

SAP BW オープン ハブ コネクタのサポートについて学習します。

> [!div class="nextstepaction"]
>[SAP Business Warehouse オープン ハブ コネクタ](connector-sap-business-warehouse-open-hub.md)
