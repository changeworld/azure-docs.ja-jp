---
title: Azure API for FHIR 内のデータを Azure Synapse Analytics に移動する
description: この記事では、FHIR データを Synapse に移動する方法について説明します
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/13/2021
ms.author: ginle
ms.openlocfilehash: e548361f6cf8d638609dd9daa83a175fa49f3679
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779506"
---
# <a name="moving-data-from-azure-api-for-fhir-to-azure-synapse-analytics"></a>Azure API for FHIR からデータを Azure Synapse Analytics に移動する

この記事では、Azure API for FHIR から [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/) にデータを移動する方法について説明します。これは、データ統合、エンタープライズ データ ウェアハウス、ビッグ データ分析を統合する制限のない分析サービスです。 

FHIR サーバーから Synapse にデータを移動するには、FHIR `$export` 操作を使用してデータをエクスポートした後、データを変換して Synapse に読み込む一連の手順が必要です。 この記事では、いくつか存在するの方法のうち 2 つについて説明します。どちらも、Synapse に移動しながら FHIR リソースを表形式に変換する方法を示します。

* **T-SQL を使用してエクスポートされたデータを Synapse に読み込む:** `$export` 操作を使用して、FHIR リソースを **Azure Data Lake Gen 2 (ADL Gen 2) BLOB ストレージ** に `NDJSON` 形式で移動します。 T-SQL を使用して、ストレージから Synapse の **サーバーレス または専用の SQL プール** にデータを読み込みます。 [Synapse パイプライン](../../synapse-analytics/get-started-pipelines.md) を使用して、これらの手順を堅牢なデータ移動パイプラインに変換します。
* **FHIR Analytics Pipelines OSS リポジトリのツールを使用する:** [FHIR Analytics Pipeline](https://github.com/microsoft/FHIR-Analytics-Pipelines) リポジトリには、**Azure Data Factory (ADF) パイプライン** を作成し、FHIR データを **Common Data Model (CDM) フォルダー** に移動し、CDM フォルダーから Synapse に移動できるツールが含まれています。

## <a name="load-exported-data-to-synapse-using-t-sql"></a>T-SQL を使用してエクスポートされたデータを Synapse に読み込む

### <a name="export-for-moving-fhir-data-into-azure-data-lake-gen-2-storage"></a>FHIR データを Azure Data Lake Gen 2 ストレージに移動する `$export`

:::image type="content" source="media/export-data/export-azure-storage-option.png" alt-text="$export を使用して Azure Storage から Synapse へ":::

#### <a name="configure-your-fhir-server-to-support-export"></a>`$export` をサポートする FHIR サーバーを構成する

Azure API for FHIR では、FHIR 仕様で定義された `$export` 操作を実装して、FHIR データのすべてまたはフィルター処理されたサブセットを `NDJSON` 形式でエクスポートします。 さらに、エクスポート中に FHIR データを匿名化するために、[匿名化エクスポート](./de-identified-export.md)がサポートされます。 `$export` を使用すると、既定で匿名化機能があらかじめ `$export` に統合されています。

FHIR データを Azure Blob ストレージにエクスポートするには、まず、データをストレージ アカウントにエクスポートするように FHIR サーバーを構成する必要があります。 (1) マネージド ID を有効にし、(2) ストレージ アカウントの Access Control に移動し、ロールの割り当てを追加して、(3) `$export` のストレージ アカウントを選択する必要があります。 より順を追った説明については、[こちら](./configure-export-data.md)を確認してください。

任意の種類の Azure Storage アカウントにデータをエクスポートするようにサーバーを構成できますが、Synapse との最適な調整を行う場合は、ADL Gen 2 にエクスポートすることをお勧めします。

#### <a name="using-export-command"></a>`$export` コマンドの使用

FHIR サーバーを構成したら、[ドキュメント](./export-data.md#using-export-command)に従って、System、Patient、または Group レベルで FHIR リソースをエクスポートできます。 たとえば、次の `$export` コマンドを使用して、`Group` 内の患者に関連付けられているすべての FHIR データをエクスポートできます。ここでは、 フィールド `{{BlobContainer}}` に ADL Gen 2 BLOB ストレージ名を指定します。

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}  
```

また、上記の `$export` 呼び出しで `_type` パラメーターを使用して、エクスポートするリソースを制限することもできます。 たとえば、次の呼び出しでは `Patient`、`MedicationRequest`、`Observation` リソースだけがエクスポートされます。

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}&
_type=Patient,MedicationRequest,Condition
```  

サポートされているさまざまなパラメーターの詳細については、[クエリ パラメーター](./export-data.md#settings-and-parameters)に関する `$export` ページ セクションを参照してください。

### <a name="create-a-synapse-workspace"></a>Synapse ワークスペースを作成する

Synapse を使用する前に、Synapse ワークスペースが必要になります。 Azure portal で Azure Synapse Analytics サービスを作成します。 より順を追ったガイドについては、[こちら](../../synapse-analytics/get-started-create-workspace.md)を確認してください。 ワークスペースを作成するには、`ADLSGEN2` アカウントが必要です。 Azure Synapse ワークスペースでは、このストレージ アカウントを使用して、ワークスペース データを格納します。

ワークスペースを作成した後、 https://web.azuresynapse.net で Synapse Studio のワークスペースにサインインするか、Azure portal で Synapse Studio を起動することで、ワークスペースを表示できます。

#### <a name="creating-a-linked-service-between-azure-storage-and-synapse"></a>Azure Storage と Synapse の間のリンクされたサービスの作成

データを Synapse に移動するには、リンク サービスを作成して、自分の Azure Storage アカウントを Synapse に接続する必要があります。 より順を追った説明については、[こちら](../../synapse-analytics/data-integration/data-integration-sql-pool.md#create-linked-services)を確認してください。

1. Synapse Studio で、 **[管理]** タブに移動し、 **[外部接続]** の下の **[リンク サービス]** を選択します。
2. 新しいリンク サービスを追加するには、 **[新規]** を選択します。
3. リストから **[Azure Data Lake Storage Gen2]** を選択し、 **[続行]** を選択します。
4. ご利用の認証資格情報を入力します。 入力し終えたら **[作成]** を選択します。

これで、ADL Gen 2 ストレージと Synapse の間にリンク サービスが作成されたので、Synapse SQL プールを使用して FHIR データを読み込み、分析する準備ができました。

### <a name="decide-between-serverless-and-dedicated-sql-pool"></a>サーバーレスか専用 SQL プールかを決定する

Azure Synapse Analytics では、サーバーレス SQL プールと専用 SQL プールという 2 つの異なる SQL プールが提供されています。 サーバーレス SQL プールを使用すると、リソースをプロビジョニングすることなく、サーバーレス SQL エンドポイントを使用して BLOB ストレージ内のデータに対して直接クエリを実行できます。 専用 SQL プールは、ハイ パフォーマンスとコンカレンシーのための処理能力を備え、エンタープライズ規模のデータ ウェアハウス機能に推奨されます。 2 つの SQL プールの詳細については SQL アーキテクチャに関する [Synapse のドキュメント ページ](../../synapse-analytics/sql/overview-architecture.md)をご覧ください。

#### <a name="using-serverless-sql-pool"></a>サーバーレス SQL プールを使用する

サーバーレスであるため、設定するインフラストラクチャも管理するクラスターもありません。 ワークスペースが作成されるとすぐに、Synapse Studio データのクエリを開始できます。

たとえば、次のクエリを使用して、`Patient.ndjson` から選択したフィールドを表形式構造に変換できます。

```sql
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson', 
FORMAT = 'csv', 
FIELDTERMINATOR ='0x0b', 
FIELDQUOTE = '0x0b')  
WITH (doc NVARCHAR(MAX)) AS rows     
CROSS APPLY OPENJSON(doc)     
WITH ( 
    ResourceId VARCHAR(64) '$.id', 
    Active VARCHAR(10) '$.active', 
    FullName VARCHAR(100) '$.name[0].text', 
    Gender VARCHAR(20) '$.gender', 
       ...
) 
```

上記のクエリでは、`OPENROWSET` 関数により Azure Storage 内のファイルにアクセスし、`OPENJSON` により JSON テキストを解析し、JSON 入力プロパティを行および列として返します。 このクエリが実行されるたび、サーバーレス SQL プールでは BLOB ストレージからファイルが読み取られ、JSON が解析され、フィールドが抽出されます。

次に示すように、[外部テーブル](../../synapse-analytics/sql/develop-tables-external-tables.md)に Parquet 形式で結果を具体化して、クエリのパフォーマンスを向上させる方法も可能です。

```sql
-- Create External data source where the parquet file will be written 
CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH ( 
    LOCATION = 'https://{{youraccount}}.blob.core.windows.net/{{exttblcontainer}}' 
); 
GO 

-- Create External File Format 
CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH ( 
    FORMAT_TYPE = PARQUET, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec' 
); 
GO 

CREATE EXTERNAL TABLE [dbo].[Patient] WITH ( 
        LOCATION = 'PatientParquet/', 
        DATA_SOURCE = [MyDataSource], 
        FILE_FORMAT = [ParquetFF] 
) AS 
SELECT * FROM  
OPENROWSET(bulk 'https://{{youraccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
-- Use rest of the SQL statement from the previous example --
```

#### <a name="using-dedicated-sql-pool"></a>専用 SQL プールを使用する

専用 SQL プールでは、メモリ内のパフォーマンスを実現するマネージド テーブルと階層キャッシュがサポートされます。 簡単な T-SQL クエリを使用してビッグ データをインポートしてから、分散クエリ エンジンの機能を利用してハイ パフォーマンスの分析を実行できます。

ストレージから専用の SQL プールにデータを読み込む最も簡単で最速の方法は、CSV、Parquet、ORC ファイルを読み取る **`COPY`** コマンドを T-SQL で使用することです。 次のクエリ例と同様に、`COPY` コマンドを使用して、テーブル構造に `NDJSON` 行を読み込んでください。 

```sql
-- Create table with HEAP, which is not indexed and does not have a column width limitation of NVARCHAR(4000) 
CREATE TABLE StagingPatient ( 
Resource NVARCHAR(MAX) 
) WITH (HEAP) 
COPY INTO StagingPatient 
FROM 'https://{{yourblobaccount}}.blob.core.windows.net/{{yourcontainer}}/Patient.ndjson' 
WITH ( 
FILE_TYPE = 'CSV', 
ROWTERMINATOR='0x0a', 
FIELDQUOTE = '', 
FIELDTERMINATOR = '0x00' 
) 
GO
```

上記の `StagingPatient` 表に JSON 行を作成したら、`OPENJSON` 関数を使用し、結果をテーブルに格納して、さまざまな表形式のデータを作成できます。 `Patient` リソースからいくつかのフィールド抽出して `Patient` テーブルを作成するための SQL クエリのサンプルを次に示します。

```sql
SELECT RES.* 
INTO Patient 
FROM StagingPatient
CROSS APPLY OPENJSON(Resource)   
WITH (
    ResourceId VARCHAR(64) '$.id',
    FullName VARCHAR(100) '$.name[0].text',
    FamilyName VARCHAR(50) '$.name[0].family',
    GivenName VARCHAR(50) '$.name[0].given[0]',
    Gender VARCHAR(20) '$.gender',
    DOB DATETIME2 '$.birthDate',
    MaritalStatus VARCHAR(20) '$.maritalStatus.coding[0].display',
    LanguageOfCommunication VARCHAR(20) '$.communication[0].language.text'
) AS RES 
GO

```

## <a name="use-fhir-analytics-pipelines-oss-tools"></a>FHIR Analytics Pipelines OSS ツール

:::image type="content" source="media/export-data/analytics-pipeline-option.png" alt-text="ADF パイプラインを使用して CDM フォルダーにデータを移動し、Synapse に移動する":::

> [!Note]
> [FHIR Analytics パイプライン](https://github.com/microsoft/FHIR-Analytics-Pipelines)は MIT ライセンスでリリースされたオープン ソース ツールであり、Azure サービスの Microsoft SLA の対象ではありません。

### <a name="adf-pipeline-for-moving-fhir-data-into-cdm-folder"></a>FHIR データを CDM フォルダーに移動する ADF パイプライン

Common Data Model (CDM) フォルダーは、データ レイク内のフォルダーであり、定義された標準化メタデータ構造と自己記述型のデータに準拠しています。 これらのフォルダーは、データ プロデューサーとデータ コンシューマー間のメタデータの相互運用性を促進します。 FHIR データを CDM フォルダーに移動する前に、データをテーブル構成に変換できます。

### <a name="generating-table-configuration"></a>テーブル構成の生成

すべてのスクリプトとソース コードを取得するには、リポジトリを複製します。 `npm install` を使用して依存関係をインストールします。 YAML 形式の手順を使用して、`Configuration-Generator` フォルダーから次のコマンドを実行してテーブル構成フォルダーを生成します。

```bash
Configuration-Generator> node .\generate_from_yaml.js -r {resource configuration file} -p {properties group file} -o {output folder}
```

リポジトリで提供される `YAML` サンプル ファイル、`resourcesConfig.yml`、`propertiesGroupConfig.yml` を使用することができます。

### <a name="generating-adf-pipeline"></a>ADF パイプラインの生成

これで、生成されたテーブル構成の内容と他のいくつかの構成を使用して、ADF パイプラインを生成できます。 この ADF パイプラインがトリガーされると、`$export` API を使用して FHIR サーバーからデータがエクスポートされ、CDM フォルダーに関連付けられた CDM メタデータと共に書き込まれます。

1. Azure Active Directory (AD) のアプリケーションおよびサービス プリンシパルを作成します。 ADF パイプラインでは、Azure バッチ サービスを使用して変換を実行し、バッチ サービスでは Azure AD アプリケーションが必要です。 [Azure AD のドキュメント](../../active-directory/develop/howto-create-service-principal-portal.md)に従います。
2. エクスポート ストレージの場所へのアクセス権をサービス プリンシパルに付与します。 エクスポート ストレージの `Access Control` で、Azure AD アプリケーションに `Storage Blob Data Contributor` ロールを付与します。
3. エグレス パイプラインをデプロイします。 Azure でのカスタム デプロイにテンプレート `fhirServiceToCdm.json` を使用します。 この手順では、次の Azure リソースを作成します。
    - `{pipelinename}-df` という名前の ADF パイプライン。
    - クライアント シークレットを格納する `{pipelinename}-kv` という名前のキー コンテナー。
    - 変換を実行する `{pipelinename}batch` という名前のバッチ アカウント。
    - `{pipelinename}storage` という名前のストレージ アカウント。
4. Azure Data Factory にアクセス権を付与します。 FHIR サービスのアクセス 制御パネルで、データ ファクトリ `{pipelinename}-df` に `FHIR data exporter` および `FHIR data reader` ロールを付与します。
5. テーブル構成フォルダーの内容を構成コンテナーにアップロードします。
6. `{pipelinename}-df` に移動し、パイプラインをトリガーします。 エクスポートされたデータは、ストレージ アカウント `{pipelinename}storage` の CDM フォルダーに表示されます。 CSV ファイルの各テーブルにつき 1 つのフォルダーが表示されます。

### <a name="from-cdm-folder-to-synapse"></a>CDM フォルダーから Synapse へ

データが CDM 形式でエクスポートされ、ADL Gen 2 ストレージに格納されたたら、CDM フォルダー内のデータを Synapse に移動できます。

次のような構成ファイルを使用して、CDM から Synapse へのパイプラインを作成できます。

```json
{
  "ResourceGroup": "",
  "TemplateFilePath": "../Templates/cdmToSynapse.json",
  "TemplateParameters": {
    "DataFactoryName": "",
    "SynapseWorkspace": "",
    "DedicatedSqlPool": "",
    "AdlsAccountForCdm": "",
    "CdmRootLocation": "cdm",
    "StagingContainer": "adfstaging",
    "Entities": ["LocalPatient", "LocalPatientAddress"]
  }
}
```

上記の構成ファイルで次のスクリプトを実行します。

```bash
.\DeployCdmToSynapsePipeline.ps1 -Config: config.json
```

ADF マネージド ID を SQL ユーザーとして SQL データベースに追加します。 ユーザーを作成してロールを割り当てるサンプル SQL スクリプトを次に示します。

```sql
CREATE USER [datafactory-name] FROM EXTERNAL PROVIDER
GO
EXEC sp_addrolemember db_owner, [datafactory-name]
GO
```

## <a name="next-steps"></a>次のステップ

この記事では、FHIR データを Synapse に移動する次の 2 つの方法を学びました。(1) `$export` を使用して ADL Gen 2 BLOB ストレージにデータを移動し、データを Synapse SQL プールに読み込む、(2) ADF パイプラインを使用して FHIR データを CDM フォルダーに移動する。

次に、医療情報が保護されていることを保証するため、Synapse にデータを移動する際の FHIR データの匿名化について学習できます。
 
>[!div class="nextstepaction"]
>[匿名化データをエクスポートする](./de-identified-export.md)








