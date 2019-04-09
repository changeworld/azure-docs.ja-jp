---
title: Azure Data Factory を使用した SAP Business Warehouse からのデータ読み込み | Microsoft Docs
description: Azure Data Factory を使用して SAP Business Warehouse (BW) からデータをコピーする
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9458903378576a50db9be92b9377987829e1ba41
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200159"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>Azure Data Factory を使用して SAP Business Warehouse (BW) からデータを読み込む

この記事では、Data Factory を使用して "_SAP Business Warehouse (BW) からオープン ハブ経由で Azure Data Lake Storage Gen2 にデータを読み込む_" 方法に関するチュートリアルを示します。 他の[サポートされるシンク データ ストア](copy-activity-overview.md#supported-data-stores-and-formats)にデータをコピーする場合も、同様の手順で実行できます。 

> [!TIP]
> SAP BW オープン ハブ統合や差分抽出フローの概要など、一般的な SAP BW からのデータのコピーについては、[SAP BW オープン ハブ コネクタに関する記事](connector-sap-business-warehouse-open-hub.md)をご覧ください。

## <a name="prerequisites"></a>前提条件

- **Azure Data Factory (ADF):** データ ファクトリがない場合は、「[Data Factory を作成する」](quickstart-create-data-factory-portal.md#create-a-data-factory)のセクションに従って作成します。 

- **宛先の種類が "データベース テーブル"である SAP BW オープン ハブ宛先 (OHD)。** 「[SAP BW オープン ハブ宛先の構成](#sap-bw-open-hub-destination-configurations)」セクションに従って作成するか、既存の OHD が ADF と統合されるように正しく構成されているかどうかを確認します。

- **使用される SAP BW ユーザーには次のアクセス許可が必要です。**

  - RFC と SAP BW の認可。
  - 認可オブジェクト "**S_SDSAUTH**" の "**実行**" アクティビティに対するアクセス許可。

- **[セルフホスト統合ランタイム](concepts-integration-runtime.md#self-hosted-integration-runtime)と SAP .NET 3.0 コネクタが必要です**。 実行する必要がある準備作業の詳細を以下に示します。

  1. バージョン > = 3.13 のセルフホステッド IR をインストールして登録します (次のチュートリアルで説明します)。 

  2. SAP の Web サイトから [64 ビットの SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html) をダウンロードし、それをセルフホステッド IR マシンにインストールします。  インストール時には、[Optional setup steps]\(省略可能なセットアップ手順\) ウィンドウで、次の図のように **[Install Assemblies to GAC]\(アセンブリを GAC にインストールする\)** オプションを選択したことを確認します。

     ![SAP .NET Connector を設定する](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>SAP BW オープン ハブからのフル コピー

Azure portal 上で、データ ファクトリに移動し、**[Author & Monitor]\(作成と監視\)** を選択して、別のタブで ADF UI を起動します。 

1. **[Let's get started]\(始めましょう\)** ページで、**[データのコピー]** を選択してデータのコピー ツールを起動します。 

2. **[プロパティ]** ページで、**[タスク名]** フィールドに名前を指定し、**[次へ]** を選択します。

3. **[ソース データ ストア]** ページで、**[新しい接続の作成]** をクリックし、コネクタ ギャラリーから **[SAP BW オープン ハブ]** を選択して **[続行]** を選択します。 検索ボックスに「SAP」と入力して、コネクタをフィルター処理できます。

4. **[Specify SAP BW Open Hub connection]\(SAP BW オープン ハブ接続の指定\)** ページで、 

   ![SAP BW オープン ハブのリンクされたサービスの作成](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. **[Connect via Integration Runtime]\(統合ランタイム経由で接続\)** を選択します。ドロップダウン リストをクリックして、既存のセルフホステッド IR を選択するか、セルフホステッド IR をまだ設定していない場合はそれを作成します。 

      新規に作成するには、ドロップダウンで **[新規]** をクリックし、**[セルフホスト]** の種類を選択し、**[名前]** を指定して **[次へ]** をクリックします。**[高速セットアップ]** を選択して現在のマシンにインストールするか、**[手動セットアップ]** の手順に従います。

      「[前提条件](#prerequisites)」で説明したように、セルフホステッド IR が実行されているのと同じマシンに **SAP .NET Connector 3.0** もインストールされていることを確認します。

   2. SAP BW の **[サーバー名]**、**[システム番号]**、**[クライアント ID]**、**[言語]** (EN 以外の場合)、**[ユーザー名]**、および **[パスワード]** を指定します。

   3. **[テスト接続]** をクリックして設定を検証し、**[完了]** を選択します。

   4. 新しい接続が作成されたことを確認します。 **[次へ]** を選択します。

5. **[Select Open Hub destinations]\(オープン ハブ宛先の選択\)** ページで、SAP BW で利用できるオープン ハブ宛先を参照し、データのコピー元を選択して **[次へ]** をクリックします。

   ![SAP BW のオープン ハブ テーブルの選択](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. 必要に応じてフィルターを指定します。 オープン ハブ宛先に、1 つの要求 ID を使用した 1 回のデータ転送プロセス (DTP) 実行からのデータのみが含まれている場合、または DTP が確実に完了し、すべてのデータをコピーする場合に、**[Exclude Last Request]\(最後の要求を除外する\)** をオフにします。 これらの設定が SAP BW 構成にどのように関連するかについて詳しくは、「[SAP BW オープン ハブ宛先の構成](#sap-bw-open-hub-destination-configurations)」セクションをご覧ください。 **[検証]** をクリックして、返されたデータをもう一度確認し、**[次へ]** を選択します。

   ![SAP BW オープン ハブ フィルターの構成](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. **[配布先データ ストア]** ページで、**[新しい接続の作成]** をクリックし、**[Azure Data Lake Storage Gen2]** を選択して、**[続行]** を選択します。

8. **[Specify Azure Data Lake Storage connection]\(Azure Data Lake Storage の接続の指定\)** ページで、 

   ![ADLS Gen2 のリンクされたサービスの作成](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. [ストレージ アカウント名] ボックスの一覧から目的の Data Lake Storage Gen2 に対応するアカウントを選択します。
   2. **[完了]** を選択して、接続を作成します。 次に、**[次へ]** を選択します。

9. **[Choose the output file or folder]\(出力ファイルまたはフォルダーの選択\)** ページで、出力フォルダー名として「copyfromopenhub」と入力し、**[次へ]** を選択します。

   ![出力フォルダーの選択](media/load-sap-bw-data/choose-output-folder.png)

10. **[File format setting]\(ファイル形式設定\)** ページで、**[次へ]** を選択して、既定の設定を使用します。

    ![シンクの形式の指定](media/load-sap-bw-data/specify-sink-format.png)

11. **[設定]** ページで、**[パフォーマンス設定]** を展開し、SAP BW から並列で読み込むために 5 などの **[Degree of copy parallelism]\(コピーの並列度\)** を設定します。 **[次へ]** をクリックします。

    ![コピー設定の構成](media/load-sap-bw-data/configure-copy-settings.png)

12. **[サマリー]** ページで設定を確認し、**[次へ]** を選択します。

13. **[デプロイ]** ページで **[監視]** を選択してパイプラインを監視します。

    ![[Deployment]\(デプロイ\) ページ](media/load-sap-bw-data/deployment.png)

14. 左側の **[監視]** タブが自動的に選択されたことがわかります。 **[アクション]** 列には、アクティビティの実行の詳細を表示するリンクとパイプラインを再実行するリンクが表示されます。

    ![パイプラインの監視](media/load-sap-bw-data/pipeline-monitoring.png)

15. パイプラインの実行に関連付けられているアクティビティの実行を表示するには、**[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプライン内のアクティビティ (コピー アクティビティ) は 1 つだけなので、エントリは 1 つのみです。 パイプラインの実行ビューに戻るには、上部の **[パイプライン]** リンクを選択します。 **[最新の情報に更新]** を選択して、一覧を更新します。

    ![アクティビティの監視](media/load-sap-bw-data/activity-monitoring.png)

16. 各コピー アクティビティの実行状況の詳細を監視するには、アクティビティ監視ビューの **[アクション]** の下の **[詳細]** リンク (眼鏡のイメージ) を選択します。 ソースからシンクにコピーされるデータの量、データのスループット、実行ステップと対応する期間、使用される構成などの詳細を監視することができます。

    ![アクティビティの監視の詳細](media/load-sap-bw-data/activity-monitoring-details.png)

17. コピーされた **[maximum Request ID]\(要求 ID の最大数\)** を確認します。 アクティビティ監視ビューに戻り、**[アクション]** の下の **[出力]** をクリックします。

    ![アクティビティの出力](media/load-sap-bw-data/activity-output.png)

    ![アクティビティの出力の詳細](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>SAP BW オープン ハブからの増分コピー

> [!TIP]
>
> ADF SAP BW オープン ハブ コネクタが SAP BW から増分データをコピーするためにどのように動作するかについては、[SAP BW オープン ハブ コネクタの差分抽出フロー](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)に関するページをご覧ください。また、この記事を先頭から読んで、コネクタ関連の構成の基礎を理解してください。

次に、SAP BW オープン ハブからの増分コピーの構成に進みましょう。 

増分コピーでは、DTP によって SAP BW オープン ハブ宛先に自動的に生成された**要求 ID** に基づく高基準値メカニズムを使用しています。 このアプローチのワークフローを表したのが次の図です。

![増分コピーのワークフロー](media/load-sap-bw-data/incremental-copy-workflow.png)

ADF UI の **[Let's get started]\(始めましょう\)** ページで、**[Create pipeline from template]\(テンプレートからパイプラインを作成\)** を選択して組み込みテンプレートを活用します。 

1. "SAP BW" を検索して **Incremental copy from SAP BW to Azure Data Lake Storage Gen2 (SAP BW から Azure Data Lake Storage Gen2 への増分コピー)** という名前のテンプレートを選択します。 このテンプレートでは、データが ADLS Gen2 にコピーされ、後で同様のフローに従って他のシンクの種類にコピーすることができます。

2. テンプレートのメイン ページで、次の 3 つの接続を選択または作成し、右下にある **[このテンプレートを使用]** を選択します。

   - **Azure BLOB**: このチュートリアルでは、コピーされた最大の要求 ID である高基準値の格納に Azure BLOB を使用します。
   - **SAP BW オープン ハブ**: コピーするデータのソースです。 詳しい構成については、前のフル コピーのチュートリアルをご覧ください。
   - **ADLS Gen2**: データのコピー先のシンクです。 詳しい構成については、前のフル コピーのチュートリアルをご覧ください。

   ![SAP BW テンプレートからの増分コピー](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. このテンプレートでは、3 つのアクティビティ (**参照、データのコピー、Web**) を使用してパイプラインが生成され、成功時にそれらがチェーンされます。 パイプラインの **[パラメーター]** タブに移動し、提供する必要のあるすべての構成を表示します。

   ![SAP BW 構成からの増分コピー](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: データのコピー元のオープン ハブ テーブル名を指定します。

   - **ADLSGen2SinkPath**: データのコピー先の ADLS Gen2 パスを指定します。 パスが存在しない場合は、実行時に ADF コピー アクティビティによって作成されます。

   - **HighWatermarkBlobPath**: `container/path`など、高基準値を格納するパスを指定します。 

   - **HighWatermarkBlobName**: `requestIdCache.txt`など、高基準値を格納する BLOB 名を指定します。 "*container/path/requestIdCache.txt*" など、HighWatermarkBlobPath+HighWatermarkBlobName の対応するパスに、0 のコンテンツを含む BLOB を作成します。 

      ![BLOB コンテンツ](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: このテンプレートで、Logic Apps を呼び出す Web アクティビティを使用して、BLOB ストレージに高基準値を設定します。 また、SQL データベースを使用してそれを格納し、ストアド プロシージャ アクティビティを使用して値を更新することもできます。 

      ここでは、まず、次のようにロジック アプリを作成してから、このフィールドに **HTTP POST URL** をコピーする必要があります。 

      ![ロジック アプリの構成](media/load-sap-bw-data/logic-app-config.png)

      1. Azure portal の新しい **Logic Apps** サービスに移動し、**[空のロジック アプリ]** をクリックして **[Logic Apps デザイナー]** に移動します。

      2. **[HTTP 要求の受信時]** のトリガーを選択します。 HTTP 要求本文を次のように指定します。

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. **[BLOB の作成]** のアクションを追加します。 [フォルダー パス] と [BLOB 名] では、上記の HighWatermarkBlobPath と HighWatermarkBlobName で構成された同じ値を使用します。

      4. **[保存]** をクリックし、ADF パイプラインで使用する **HTTP POST URL** の値をコピーします。

4. ADF パイプライン パラメーターのすべての値を指定した後、**[デバッグ]** -> **[完了]** をクリックして、構成の検証の実行を起動します。 または、**[すべて公開]** を選択してすべての変更を公開し、**[トリガー]** をクリックして実行します。

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW オープン ハブ宛先の構成

ここでは、ADF で SAP BW オープン ハブ コネクタを使用してデータをコピーするために SAP BW 側で必要な構成について説明します。

### <a name="configure-delta-extraction-in-sap-bw"></a>SAP BW での差分抽出の構成

履歴コピーと増分コピーの両方、または増分コピーのみが必要な場合は、SAP BW で差分抽出を構成します。

1. オープン ハブ宛先 (OHD) を作成する

   SAP トランザクション RSA1 に OHD を作成できます。これにより、必要な変換とデータ転送プロセス (DTP) が自動的に作成されます。 次の設定を使用します。

   - オブジェクトの種類は任意に指定できます。 ここでは、InfoCube を例として使用します。
   - **送信先の種類:***データベース テーブル*
   - **Key of the Table (テーブルのキー):***Technical Key (テクニカル キー)*
   - **抽出:***Keep Data and Insert Records into Table (データを保持し、テーブルにレコードを挿入)*

   ![SAP BW OHD 差分抽出の作成](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   DTP の SAP ワーク プロセスの並列実行の数を増やすことができます。

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. プロセス チェーン内で DTP をスケジュール設定する

   キューブの差分 DTP は、必要な行がまだ圧縮されていない場合にのみ動作します。 そのため、オープン ハブ テーブルへの DTP の前に BW キューブの圧縮が実行されていないことを確認する必要があります。 これを最も簡単に行う方法は、この DTP を既存のプロセス チェーンに統合することです。 次の例では、(OHD への) DTP は調整 (集約ロールアップ) と折りたたみ (キューブ圧縮) のステップの間のプロセス チェーンに挿入されます。

   ![create-sap-bw-process-chain](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>SAP BW での完全抽出の構成

差分抽出に加えて、同じ InfoProvider の完全抽出を行うことができます。 通常は、増分の必要がないフル コピーを行う場合や、[差分抽出を再同期](#re-sync-delta-extraction)する場合に適用されます。

同じ OHD に対して複数の DTP を持つことはできません。 そのため、追加の OHD を作成してから差分抽出を作成する必要があります。

![create-sap-bw-ohd-full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

完全読み込み OHD の場合は、差分抽出以外のオプションを選択します。

- OHD で、[抽出] オプションを "*Delete Data and Insert Records (データの削除とレコードの挿入)*" として設定します。 そうしないと、BW プロセス チェーンで DTP を繰り返すときにデータが何度も抽出されます。

- DTP で、[Extraction Mode]\(抽出モード\) を "*Full (完全)*" として設定します。 OHD が作成された直後に、自動的に作成された DTP を [Delta]\(差分\) から [Full]\(完全\) に変更する必要があります。

   ![create-sap-bw-ohd-full2](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- ADF SAP BW オープン ハブ コネクタで、オプション *[Exclude last request]\(最後の要求を除外する\)* をオフにします。 そうしないと、何も抽出されません。 

通常は、完全 DTP を手動で実行します。 または、完全 DTP のプロセス チェーンを作成することもできます。通常は、既存のプロセス チェーンから独立した別のプロセス チェーンになります。 いずれの場合も、**ADF コピーを使用して抽出を開始する前に DTP が完了したことを確認する**必要があります。そうしないと、部分的なデータがコピーされます。

### <a name="run-delta-extraction-the-first-time"></a>差分抽出の初回実行

最初の差分抽出は、技術的には**完全抽出**です。 既定では、ADF SAP BW オープン ハブ コネクタは、データをコピーするときに前回の要求を除外することにご注意ください。 初回の差分抽出の場合、ADF コピー アクティビティでは、後続の DTP によって別の要求 ID でテーブルに差分データが生成されるまで、データは抽出されません。 このシナリオを回避できる方法が 2 つあります。

1. 最初の差分抽出に対してオプション [Exclude last request]\(最後の要求を除外する\) をオフにします。この場合、初回の差分抽出を開始する前に最初の差分 DTP が完了したことを確認する必要があります
2. 以下に説明するデルタ抽出の再同期の手順を使用します。

### <a name="re-sync-delta-extraction"></a>差分抽出の再同期

SAP BW キューブ内のデータが変更されても差分 DTP によって考慮されないシナリオがいくつかあります。

- SAP BW の (任意のフィルター条件を使用した行の) 選択的削除
- SAP BW 要求の (問題のある要求の) 削除

SAP オープン ハブ宛先は、(2015 年以降のすべて SAP BW サポート パッケージでは) データ マートで制御されるデータ ターゲットではありません。 そのため、OHD 内のデータを変更せずに、キューブからデータを削除することができます。 この場合は、次の手順を実行して、ADF 内のデータを使用してキューブのデータを再同期する必要があります。

1. (SAP の完全 DTP を使用して) ADF 内で完全抽出を実行する
2. 差分 DTP のオープン ハブ テーブル内の行をすべて削除する
3. フェッチする差分 DTP の状態を設定する

その後、すべての後続の差分 DTP と ADF 差分抽出は期待どおりに正しく機能します。

次のオプションを使用して、差分 DTP を手動で実行して、差分 DTP の状態を [Fetched]\(フェッチ済み\) に設定できます:"*No Data Transfer; Delta Status in Source:Fetched (データ転送なし。ソース内の差分状態: フェッチ済み)*"。

## <a name="next-steps"></a>次の手順

次の記事に進んで、SAP BW オープン ハブ コネクタのサポートについて確認します。 

> [!div class="nextstepaction"]
>[SAP Business Warehouse オープン ハブ コネクタ](connector-sap-business-warehouse-open-hub.md)
