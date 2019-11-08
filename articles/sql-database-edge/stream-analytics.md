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
ms.openlocfilehash: 1b6b3f4e4be9d056bc53fac2eb2f1f3fcd768085
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510948"
---
# <a name="using-sql-database-dac-package-and-stream-analytics-job-with-sql-database-edge"></a>SQL Database Edge で SQL Database DAC パッケージと Stream Analytics ジョブを使用する

Azure SQL Database Edge プレビューは、IoT およびエッジのデプロイ向けに最適化されたリレーショナル データベース エンジンです。 これは業界最高レベルのパフォーマンス、セキュリティ、およびクエリ処理機能を提供する、Microsoft SQL Server データベース エンジンの最新バージョンに基づいて構築されています。 SQL Server の業界をリードするリレーショナル データベース管理機能と共に、Azure SQL Database Edge はリアルタイム分析や複雑なイベント処理のための組み込みのストリーミング機能を提供します。

また Azure SQL Database Edge には、SQLPackage.exe のネイティブ実装が用意されています。これにより、ユーザーは SQL Database Edge のデプロイ時に [SQL Database DAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) パッケージをデプロイできます。

Azure SQL Database Edge では、IoT Edge モジュールの *モジュール ツインの必要なプロパティ* オプションによって 2 つのオプション パラメーターが公開されています。

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
| SQLPackage | SQL Database DAC パッケージを含む *.zip ファイルの Azure Blob Storage URI。
| ASAJobInfo | ASA Edge ジョブの Azure Blob Storage URI。 ASA Edge ジョブの発行の詳細については、[SQL Database Edge の ASA Edge ジョブの発行]()に関する記事を参照してください。

## <a name="using-sql-database-dac-packages-with-sql-database-edge"></a>SQL Database Edge での SQL Database DAC パッケージの使用

SQL Database Edge で SQL Database DAC パッケージ (*.dacpac) を使用するには、以下の手順に従ってください。

1. SQL Database DAC パッケージを作成または抽出します。 [既存のデータベースからの DAC の抽出](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/)に関する記事で説明されている概念を使用して、既存の SQL Database の DacPac を生成することができます。

2. * *.dacpac* を圧縮し、Azure Blob ストレージ アカウントにアップロードします。 Azure Blob Storage へのファイルのアップロードの詳細については、[Azure Portal を使用した BLOB のアップロード、ダウンロード、および一覧表示](../storage/blobs/storage-quickstart-blobs-portal.md)に関するページを参照してください。

3. Azure portal を使用して zip ファイルの SAS 署名を生成します。 詳細については、「[Shared Access Signature (SAS) によるアクセスの委任](../storage/common/storage-sas-overview.md)」を参照してください。

4. DAC パッケージの SAS URI が含まれるように SQL Database Edge モジュール構成を更新します。 SQL Database Edge モジュールを更新するには

    1. Azure portal で、IoT Hub のデプロイに移動します。

    2. 左側のウィンドウで、 **[IoT Edge]** をクリックします。

    3. **[IoT Edge]** ページで、SQL Database Edge モジュールがデプロイされている IoT Edge を見つけてクリックします。

    4. *[IoT Edge デバイス]* のデバイス ページで、 **[Set Module]/(モジュールの設定/)** をクリックします。 

    5. **[Set Module]/(モジュールの設定/)** ページで、SQL Database Edge モジュールに対して *[configure]/(構成/)* をクリックします。 

    6. **[IoT Edge のカスタム モジュール]** ウィンドウで、 *[Set modules twin's desired properties]/(モジュール ツインの必要なプロパティの設定/)* を選択し、次の例に示すように、SQLPackage オプションの URI が含まれるように必要なプロパティを更新します。 

        > [!NOTE]
        > 以下の SAS URI は、例示のみを目的としています。 この URI をデプロイの実際の URI に置き換えてください。

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "https://StorageAccountName.blob.core.windows.net/SQLpackageFiles/MeasurementsDB.zip?sp=r&st=2019-09-01T00:00:00Z&se=2019-09-30T00:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=Uh8X0E50qzlxkiKVBJKU3ccVQYwF235qTz7AXp4R3gI%3D",
                }
            }
        ```

    7. **[Save]** をクリックします。

    8. **[モジュールの設定]** ページで、 *[次へ]* をクリックします。

    9. **モジュールの設定** ページで、*次へ* をクリックし、**送信** をクリックします。

5. モジュールの更新後、dacpac ファイルがダウンロードおよび解凍され、SQL Database Edge インスタンスにデプロイされます。

## <a name="using-streaming-jobs-with-sql-database-edge"></a>SQL Database Edge でのストリーミング ジョブの使用

Azure SQL Database Edge には、Stream Analytics ランタイムがネイティブで実装されています。 これにより、ユーザーは Azure Stream Analytics Edge ジョブを作成し、そのジョブを SQL Database Edge ストリーミング ジョブとしてデプロイできます。 Stream Analytics Edge ジョブを作成するには、次の手順に従います。

1. プレビュー [URL](https://portal.azure.com/?microsoft_azure_streamanalytics_edgeadapterspreview=true) を使用して、Azure portal を参照します。 このプレビュー URL を使用すると、Stream Analytics Edge ジョブの SQL Database 出力を構成できます。

2. 新しい **Azure Stream Analytics on IoT Edge** ジョブを作成し、**Edge** をターゲットとするホスティング環境を選択します。

3. Azure Stream Analytics ジョブの "*入力*" と "*出力*" を定義します。 各 SQL 出力 (以下で構成) は、データベース内の 1 つのテーブルに関連付けられます。 複数のテーブルにデータをストリームする必要がある場合は、複数の SQL Database 出力を作成する必要があります。 SQL 出力は、異なるデータベースを指すように構成できます。

    *入力 - Edge ジョブの入力として EdgeHub を選択し、リソース情報を入力します。*

    *出力 - 出力として SQL Database を選択し、[手動で SQL Database 設定を指定] を選択して、データベースとテーブルの構成の詳細を指定します。*

    |フィールド      | 説明 |
    |---------------|-------------|
    |出力エイリアス | 出力エイリアスの名前。|
    |Database | SQL Database の名前。 これは、SQL Database Edge インスタンスに存在する有効なデータベース名である必要があります。|
    |サーバー名 | SQL インスタンスの名前 (または IP アドレス) とポート番号の詳細。 SQL Database Edge のデプロイの場合、**tcp:.,1433** をサーバー名として使用できます。|
    |ユーザー名 | 前述のデータベースへのデータ閲覧者とデータ作成者のアクセス権を持つ SQL ログイン アカウント。|
    |パスワード | 前述の SQL ログイン アカウントのパスワード。|
    |テーブル | ストリーミング ジョブの出力対象となるテーブルの名前。|
    |パーティション分割の継承| この SQL 出力構成オプションを使用すると、前のクエリ ステップや入力のパーティション構成を継承できます。 これを有効にすると、ディスク ベースのテーブルへの書き込みと、ジョブの完全並列トポロジにより、スループットの向上を期待できます。|
    |バッチ サイズ| バッチ サイズは、すべての一括挿入トランザクションで送信されるレコードの最大数です。|

    入力/出力の構成例を以下に示します。

    ```txt
        Input:
            Input from EdgeHub
            Input alias: input
            Event serialization format: JSON
            Encoding: UTF-8
            Event compression type: None

        Output :
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

4. エッジ ジョブの ASA ジョブ クエリを定義します。 このクエリでは、定義済みの入力/出力エイリアスを、クエリの入力および出力の名前として使用する必要があります。 詳細については、「[Stream Analytics クエリ言語リファレンス](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)」をご覧ください。

5. Edge ジョブのストレージ アカウントの設定を行います。 ストレージ アカウントは、エッジ ジョブの発行ターゲットとして使用されます。

6. [Configure]/(構成/) で、[Publish]/(発行/) を選択し、[Publish]/(発行/) ボタンをクリックします。 SQL Database Edge モジュールで使用するための SAS URL を保存します。

### <a name="deploy-the-stream-analytics-edge-job-to-the-sql-database-edge"></a>Stream Analytics Edge ジョブを SQL Database Edge にデプロイする

ストリーミング ジョブを SQL Database Edge モジュールにデプロイするには、前の手順のストリーミング ジョブの SAS URI が含まれるように SQL Database Edge モジュール構成を更新します。 SQL Database Edge モジュールを更新するには

1. Azure portal で、IoT Hub のデプロイに移動します。

2. 左側のウィンドウで、 **[IoT Edge]** をクリックします。

3. **[IoT Edge]** ページで、SQL Database Edge モジュールがデプロイされている IoT Edge を見つけてクリックします。

4. *[IoT Edge デバイス]* のデバイス ページで、 **[Set Module]/(モジュールの設定/)** をクリックします。 

5. **[Set Module]/(モジュールの設定/)** ページで、SQL Database Edge モジュールに対して *[configure]/(構成/)* をクリックします。 

6. **[IoT Edge のカスタム モジュール]** ウィンドウで、 *[Set modules twin's desired properties]/(モジュール ツインの必要なプロパティの設定/)* を選択し、次の例に示すように、ASAJobInfo オプションの URI が含まれるように必要なプロパティを更新します。 

    > [!NOTE]
    > 以下の SAS URI は、例示のみを目的としています。 この URI をデプロイの実際の URI に置き換えてください。

    ```json
        {
            "properties.desired":
            {
                "ASAJobInfo": "https://storageAccountName.blob.core.windows.net/asajobs/ASAEdgeJobs/5a9b34db-7a5c-4606-adae-4c8609eaa1c7/d85b5aa6-4d38-4703-bb34-af7f0bd7916d/ASAEdgeJobDefinition.zip?sv=2018-03-28&sr=b&sig=HH%2BFMsEy378RaTxIy2Xo6rM6wDaqoBaZ5zFDBqdZiS0%3D&st=2019-09-01T22%3A24%3A34Z&se=2019-09-30T22%3A34%3A34Z&sp=r"   
            }
        }
    ```

7. **[Save]** をクリックします。

8. **[モジュールの設定]** ページで、 *[次へ]* をクリックします。

9. **モジュールの設定** ページで、*次へ* をクリックし、**送信** をクリックします。

10. モジュールの更新後、Stream Analytics ジョブ ファイルがダウンロードおよび解凍され、SQL Database Edge インスタンスにデプロイされます。

## <a name="next-steps"></a>次の手順

- 価格と可用性に関連する詳細については、[Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/) に関するページを参照してください。
- お使いのサブスクリプションに対して Azure SQL Database Edge を有効にするように要求します。
- 使用を開始するには、以下を参照してください。
  - [Azure portal を介して SQL Database Edge をデプロイする](deploy-portal.md)
