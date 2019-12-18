---
title: Azure SQL Database Edge で SQL Database DAC パッケージと Stream Analytics ジョブを使用する | Microsoft Docs
description: SQL Database Edge での Stream Analytics ジョブの使用について説明します
keywords: SQL Database Edge、Stream Analytics、SQLPackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 21a8bb6953fd879b17816361f536596571678697
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384171"
---
# <a name="using-sql-database-dac-packages-and-stream-analytics-jobs-with-sql-database-edge"></a>SQL Database Edge で SQL Database DAC パッケージと Stream Analytics ジョブを使用する

Azure SQL Database Edge プレビューは、IoT およびエッジのデプロイ向けに最適化されたリレーショナル データベース エンジンです。 これは業界最高レベルのパフォーマンス、セキュリティ、およびクエリ処理機能を提供する、Microsoft SQL Server データベース エンジンの最新バージョンに基づいて構築されています。 SQL Server の業界をリードするリレーショナル データベース管理機能と共に、Azure SQL Database Edge はリアルタイム分析や複雑なイベント処理のための組み込みのストリーミング機能を提供します。

また Azure SQL Database Edge には、SqlPackage.exe のネイティブ実装が用意されています。これにより、SQL Database Edge のデプロイ時に [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) パッケージをデプロイできます。

Azure SQL Database Edge では、IoT Edge モジュールの `module twin's desired properties` オプションによって 2 つのオプション パラメーターが公開されています。

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|フィールド | 説明 |
|------|-------------|
| SqlPackage | SQL Database DAC パッケージを含む *.zip ファイルの Azure Blob ストレージ URI。
| ASAJobInfo | ASA Edge ジョブの Azure Blob ストレージ URI。 詳細については、[SQL Database Edge の ASA Edge ジョブの発行](/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)に関するページを参照してください。

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database Edge での SQL Database DAC パッケージの使用

SQL Database Edge で SQL Database DAC パッケージ (*.dacpac) を使用するには、次の手順を実行します。

1. SQL Database DAC パッケージを作成または抽出します。 既存の SQL Server データベースに対して DAC パッケージを生成する方法については、「[データベースからの DAC の抽出](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)」を参照してください。

2. *.dacpac を zip 圧縮し、Azure Blob ストレージ アカウントにアップロードします。 Azure Blob ストレージへのファイルのアップロードの詳細については、「[Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../storage/blobs/storage-quickstart-blobs-portal.md)」を参照してください。

3. Azure portal を使用して、zip ファイルの共有アクセス署名を生成します。 詳細については、[Shared Access Signature (SAS) を使用したアクセスの委任](../storage/common/storage-sas-overview.md)に関するページを参照してください。

4. DAC パッケージの共有アクセス URI が含まれるように SQL Database Edge モジュール構成を更新します。 SQL Database Edge モジュールを更新するには、次の手順を実行します。

    1. Azure portal で、IoT Hub のデプロイに移動します。

    2. 左側のウィンドウで、 **[IoT Edge]** を選択します。

    3. **[IoT Edge]** ページで、SQL Database Edge モジュールがデプロイされている IoT エッジを見つけて選択します。

    4. **[IoT Edge デバイス]** のデバイス ページで、 **[Set Module]\(モジュールの設定\)** を選択します。

    5. **[Set Module]\(モジュールの設定\)** ページで、SQL Database Edge モジュールに対して **[Configure]\(構成\)** を選択します。

    6. **[IoT Edge Custom Modules]\(IoT Edge カスタム モジュール\)** ウィンドウで、 **[Set module twin's desired properties]\(モジュール ツインの必要なプロパティの設定\)** を選択します。 次の例に示すように、`SQLPackage` オプションの URI が含まれるように、必要なプロパティを更新します。

        > [!NOTE]
        > 次の JSON の SAS URI は単なる例です。 この URI をデプロイの実際の URI に置き換えます。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. **[保存]** を選択します。

    8. **[Set modules]\(モジュールの設定\)** ページで、 **[次へ]** を選択します。

    9. **[Set modules]\(モジュールの設定\)** ページで、 **[次へ]** 、 **[送信]** の順に選択します。

5. モジュールの更新後、DAC パッケージ ファイルがダウンロードおよび解凍され、SQL Database Edge インスタンスにデプロイされます。

## <a name="using-streaming-jobs-with-sql-database-edge"></a>SQL Database Edge でのストリーミング ジョブの使用

Azure SQL Database Edge には、ストリーム分析ランタイムがネイティブで実装されています。 この実装により、Azure Stream Analytics エッジ ジョブを作成し、そのジョブを SQL Database Edge ストリーミング ジョブとしてデプロイすることができます。 Stream Analytics エッジ ジョブを作成するには、次の手順を完了します。

1. プレビュー [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true) を使用して Azure portal にアクセスします。 このプレビュー URL を使用すると、Stream Analytics エッジ ジョブの SQL Database 出力を構成できます。

2. 新しい **Azure Stream Analytics on IoT Edge** ジョブを作成します。 **Edge** をターゲットにするホスティング環境を選択します。

3. Azure Stream Analytics ジョブの入力と出力を定義します。 ここで設定する SQL 出力はそれぞれ、データベース内の 1 つのテーブルに関連付けられます。 複数のテーブルにデータをストリームする必要がある場合は、複数の SQL Database 出力を作成する必要があります。 異なるデータベースを指すように SQL 出力を構成できます。

    **入力**。 EdgeHub をエッジ ジョブの入力として選択し、リソース情報を提供します。

    **出力**。 SQL Database を出力として選択します。 **[Provide SQL Database settings manually]\(SQL データベース設定を手動で行う\)** を選択します。 データベースとテーブルの構成の詳細を提供します。

    |フィールド      | 説明 |
    |---------------|-------------|
    |出力エイリアス | 出力エイリアスの名前。|
    |Database | SQL データベースの名前。 SQL Database Edge インスタンスに存在するデータベースの有効な名前である必要があります。|
    |サーバー名 | SQL インスタンスの名前 (または IP アドレス) とポート番号の詳細。 SQL Database Edge のデプロイの場合、**tcp:.,1433** をサーバー名に使用できます。|
    |ユーザー名 | 以前に指定したデータベースへのデータ閲覧者とデータ作成者のアクセス権を持つ SQL サインイン アカウント。|
    |パスワード | 以前に指定した SQL サインイン アカウントのパスワード。|
    |テーブル | ストリーミング ジョブの出力対象となるテーブルの名前。|
    |パーティション分割の継承| 以前のクエリ ステップまたは入力のパーティション構成を継承できます。 このオプションを有効にすると、ディスクベースのテーブルに書き込んでいて、ジョブの完全並列トポロジがある場合にスループットの向上を期待できます。|
    |バッチ サイズ| すべての一括挿入トランザクションで送信されるレコードの最大数。|

    次に示すのは、入力/出力の構成例です。

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output:
            Output alias: output
            Database:  MeasurementsDB
            Server name: tcp:.,1433
            Username: sa
            Password: <Password>
            Table: TemperatureMeasurements
            Inherit Partitioning: Merge all input partitions into a single writer
            Max batch count: 10000
    ```

    > [!NOTE]
    > Azure Stream Analytics 用の SQL 出力アダプターの詳細については、「[Azure SQL Database への Azure Stream Analytics の出力](../stream-analytics/stream-analytics-sql-output-perf.md)」を参照してください。

4. エッジ ジョブの ASA ジョブ クエリを定義します。 このクエリでは、定義済みの入力/出力エイリアスを、クエリの入力および出力の名前として使用する必要があります。 詳細については、[Stream Analytics クエリ言語リファレンス](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)を参照してください。

5. エッジ ジョブのストレージ アカウントの設定を行います。 ストレージ アカウントは、エッジ ジョブの発行ターゲットとして使用されます。

6. **[Configure]\(構成\)** で、 **[Publish]\(発行\)** を選択し、 **[Publish]\(発行\)** ボタンを選択します。 SQL Database Edge モジュールで使用するための SAS URI を保存します。

### <a name="deploy-the-stream-analytics-edge-job-to-sql-database-edge"></a>Stream Analytics エッジ ジョブを SQL Database Edge にデプロイする

ストリーミング ジョブを SQL Database Edge モジュールにデプロイするには、前の手順のストリーミング ジョブの SAS URI が含まれるように SQL Database Edge モジュール構成を更新します。 SQL Database Edge モジュールを更新するには、次の手順を実行します。

1. Azure portal で、IoT Hub のデプロイに移動します。

2. 左側のウィンドウで、 **[IoT Edge]** を選択します。

3. **[IoT Edge]** ページで、SQL Database Edge モジュールがデプロイされている IoT エッジを見つけて選択します。

4. **[IoT Edge デバイス]** のデバイス ページで、 **[Set Module]\(モジュールの設定\)** を選択します。

5. **[Set Module]\(モジュールの設定\)** ページで、SQL Database Edge モジュールに対して **[Configure]\(構成\)** を選択します。

6. **[IoT Edge Custom Modules]\(IoT Edge カスタム モジュール\)** ウィンドウで、 **[Set module twin's desired properties]\(モジュール ツインの必要なプロパティの設定\)** を選択します。 次の例に示すように、`ASAJobInfo` オプションの URI が含まれるように、必要なプロパティを更新します。

    > [!NOTE]
    > 次の JSON の SAS URI は単なる例です。 この URI をデプロイの実際の URI に置き換えます。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. **[保存]** を選択します。

8. **[Set modules]\(モジュールの設定\)** ページで、 **[次へ]** を選択します。

9. **[Set modules]\(モジュールの設定\)** ページで、 **[次へ]** 、 **[送信]** の順に選択します。

10. モジュールの更新後、Stream Analytics ジョブ ファイルがダウンロードおよび解凍され、SQL Database Edge インスタンスにデプロイされます。

## <a name="next-steps"></a>次の手順

- 価格と可用性の詳細については、[Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) に関するページを参照してください。
- お使いのサブスクリプションに対して Azure SQL Database Edge の有効化を要求します。
- 開始するには、[Azure portal を使用した SQL Database Edge のデプロイ](deploy-portal.md)に関するページを参照してください。
