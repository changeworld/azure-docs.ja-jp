---
title: データをデータにAzure API for FHIRするAzure Synapse Analytics
description: この記事では、FHIR データを Synapse に移動する方法について説明します
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/13/2021
ms.author: ginle
ms.openlocfilehash: 1fc264109b0cc2d6534f0c8b213dfc250c700d15
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116796"
---
# <a name="moving-data-from-azure-api-for-fhir-to-azure-synapse-analytics"></a>データのデータのAzure API for FHIRへのAzure Synapse Analytics

この記事では、Azure API for FHIR から [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)にデータを移動する方法について説明します。これは、データ統合、エンタープライズ データ ウェアハウス、ビッグ データ分析を組み合わせて使用する無限の分析サービスです。 

FHIR サーバーから Synapse にデータを移動するには、FHIR 操作を使用してデータをエクスポートした後、データを変換して Synapse に読み込む一連の手順が必要です `$export` 。 この記事では、いくつかの方法の 2 つについて説明します。どちらも、Synapse に移動しながら FHIR リソースを表形式に変換する方法を示します。

* **T-SQL を使用してエクスポートされたデータを Synapse に読み込む:** 操作 `$export` を使用して、FHIR リソースを **Azure Data Lake Gen 2 (ADL Gen 2) BLOB** ストレージに形式で移動 `NDJSON` します。 T-SQL を使用して、Synapse のサーバー **レスまたは専用 SQL** プールにストレージからデータを読み込む。 Synapse パイプライン を使用して、これらの手順を堅牢な [データ移動パイプラインに変換します](../../synapse-analytics/get-started-pipelines.md)。
* **FHIR Analytics Pipelines OSS レポポからツールを使用します。**[FHIR Analytics パイプライン](https://github.com/microsoft/FHIR-Analytics-Pipelines)のレポジターには、FHIR データを **Common Data Model (CDM)** フォルダー に移動し、CDM フォルダーから Synapse に移動する Azure Data Factory **(ADF)** パイプラインを作成できるツールが含まれています。

## <a name="load-exported-data-to-synapse-using-t-sql"></a>T-SQL を使用してエクスポートされたデータを Synapse に読み込む

### <a name="export-for-moving-fhir-data-into-azure-data-lake-gen-2-storage"></a>`$export` FHIR データを Azure Data Lake Gen 2 ストレージに移動する場合

:::image type="content" source="media/export-data/export-azure-storage-option.png" alt-text="Azure Storage から Synapse へのアクセスに関する$export":::

#### <a name="configure-your-fhir-server-to-support-export"></a>サポートする FHIR サーバーを構成する `$export`

Azure API for FHIR FHIR 仕様で定義された操作を実装して、FHIR データのすべてまたはフィルター処理されたサブセット `$export` を形式でエクスポート `NDJSON` します。 さらに、エクスポート中に FHIR [データ](./de-identified-export.md) を匿名化するために、識別されていないエクスポートがサポートされます。 を使用すると、既定で識別機能は に既に `$export` 統合されています `$export` 。

Azure blob storage に FHIR データをエクスポートするには、まず、データをストレージアカウントにエクスポートするように FHIR サーバーを構成する必要があります。 (1) 管理対象 Id を有効にする必要があります。 (2) ストレージアカウントの Access Control にアクセスし、ロールの割り当てを追加します。 (3) 用のストレージアカウントを選択し `$export` ます。 詳細な手順については、 [こちら](./configure-export-data.md)を参照してください。

データを任意の種類の Azure ストレージアカウントにエクスポートするようにサーバーを構成できますが、Synapse に最適なアラインメントを行うには、ADL Gen 2 にエクスポートすることをお勧めします。

#### <a name="using-export-command"></a>コマンドの使用 `$export`

FHIR サーバーを構成した後、 [ドキュメント](./export-data.md#using-export-command) に従って、システム、患者、またはグループレベルで fhir リソースをエクスポートできます。 たとえば、次のコマンドを使用して、で患者に関連するすべての FHIR データをエクスポートできます。この場合、 `Group` `$export` フィールドに ADL Gen 2 blob ストレージ名を指定し `{{BlobContainer}}` ます。

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}  
```

`_type`上記の呼び出しでパラメーターを使用し `$export` て、エクスポートするリソースを制限することもできます。 たとえば、次の呼び出しでは、、、およびのリソースのみがエクスポートされ `Patient` `MedicationRequest` `Observation` ます。

```rest
https://{{FHIR service base URL}}/Group/{{GroupId}}/$export?_container={{BlobContainer}}&
_type=Patient,MedicationRequest,Condition
```  

サポートされているさまざまなパラメーターの詳細については、 `$export` [クエリパラメーター](./export-data.md#settings-and-parameters)に関するページのセクションをご覧ください。

### <a name="create-a-synapse-workspace"></a>Synapse ワークスペースを作成する

Synapse を使用する前に、Synapse ワークスペースが必要になります。 Azure portal で Azure Synapse Analytics サービスを作成します。 詳細なステップバイステップガイドについては、 [こちら](../../synapse-analytics/get-started-create-workspace.md)を参照してください。 `ADLSGEN2`ワークスペースを作成するにはアカウントが必要です。 Azure Synapse ワークスペースは、このストレージアカウントを使用して Synapse ワークスペースデータを格納します。

ワークスペースを作成したら、でワークスペースにサインインする https://web.azuresynapse.net か、Azure portal で Synapse studio を起動して、Synapse studio でワークスペースを表示できます。

#### <a name="creating-a-linked-service-between-azure-storage-and-synapse"></a>Azure storage と Synapse の間にリンクされたサービスを作成する

データを Synapse に移動するには、Azure Storage アカウントを Synapse に接続するリンクされたサービスを作成する必要があります。 詳細な手順については、 [こちら](../../synapse-analytics/data-integration/data-integration-sql-pool.md#create-linked-services)を参照してください。

1. [Synapse Studio管理] タブに **移動** し、[外部接続]で [リンクされたサービス]**を選択します**。
2. [新規 **] を** 選択して、新しいリンクされたサービスを追加します。
3. 一覧 **Azure Data Lake Storage Gen2** を選択し、 [続行] を **選択します**。
4. ご利用の認証資格情報を入力します。 入力し終えたら **[作成]** を選択します。

ADL Gen 2 ストレージと Synapse の間にリンクされたサービスができたので、Synapse SQL プールを使用して FHIR データを読み込み、分析する準備ができました。

### <a name="decide-between-serverless-and-dedicated-sql-pool"></a>サーバーレス SQL プールと専用 SQL プールの決定

Azure Synapse Analyticsには、サーバーレス SQL プールと専用 SQL プールという 2 つの異なる SQL プールがあります。 サーバーレス SQL プールを使用すると、リソースをプロビジョニングすることなく、サーバーレス SQL エンドポイントを使用して BLOB ストレージ内のデータに対して直接クエリを実行できます。 専用 SQL プールは、高パフォーマンスとコンカレンシーのための処理能力を備え、エンタープライズ規模のデータ ウェアハウス機能に推奨されます。 2 つの SQL プールの詳細については、SQL アーキテクチャに関する [Synapse](../../synapse-analytics/sql/overview-architecture.md) のドキュメント ページを参照してください。

#### <a name="using-serverless-sql-pool"></a>サーバーレス SQL プールの使用

サーバーレスであるから、セットアップするインフラストラクチャや維持するクラスターはありません。 ワークスペースが作成されるとすぐに、Synapse Studioデータのクエリを開始できます。

たとえば、次のクエリを使用して、選択したフィールドを から表 `Patient.ndjson` 形式構造に変換できます。

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

上記のクエリでは、 関数は Azure Storage 内のファイルにアクセスし、JSON テキストを解析し、JSON 入力プロパティを行および列 `OPENROWSET` `OPENJSON` として返します。 このクエリが実行されるたび、サーバーレス SQL プールは BLOB ストレージからファイルを読み取り、JSON を解析して、フィールドを抽出します。

次に示すように、外部テーブルの Parquet 形式[](../../synapse-analytics/sql/develop-tables-external-tables.md)で結果をマライズして、クエリのパフォーマンスを向上させる方法も可能です。

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

#### <a name="using-dedicated-sql-pool"></a>専用 SQL プールの使用

専用 SQL プールは、メモリ内のパフォーマンスを実現するマネージド テーブルと階層キャッシュをサポートします。 単純な T-SQL クエリを使用してビッグ データをインポートし、分散クエリ エンジンの機能を使用して高パフォーマンスの分析を実行できます。

ストレージから専用 SQL プールにデータを読み込む最も簡単かつ迅速な方法は、T-sql でコマンドを使用することです。これにより、 **`COPY`** CSV、Parquet、および ORC ファイルを読み取ることができます。 次のクエリ例のように、コマンドを使用して、 `COPY` `NDJSON` テーブル構造に行を読み込みます。 

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

上の表に JSON 行を作成したら、 `StagingPatient` 関数を使用してさまざまな表形式のデータを作成し、その `OPENJSON` 結果をテーブルに格納できます。 次に、 `Patient` リソースからいくつかのフィールドを抽出してテーブルを作成するための SQL クエリのサンプルを示し `Patient` ます。

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

## <a name="use-fhir-analytics-pipelines-oss-tools"></a>FHIR 分析パイプライン OSS ツールを使用する

:::image type="content" source="media/export-data/analytics-pipeline-option.png" alt-text="ADF パイプラインを使用して、データを CDM フォルダーに移動してから Synapse に移動する":::

> [!Note]
> [Fhir 分析パイプライン](https://github.com/microsoft/FHIR-Analytics-Pipelines) は、MIT ライセンスの下でリリースされたオープンソースツールであり、Azure サービスの MICROSOFT の SLA には含まれていません。

### <a name="adf-pipeline-for-moving-fhir-data-into-cdm-folder"></a>FHIR データを CDM フォルダーに移動する ADF パイプライン

Common Data Model (CDM) フォルダーは、適切に定義され、標準化されたメタデータ構造と自己言及的なデータに準拠する data lake 内のフォルダーです。 これらのフォルダーは、データプロデューサーとデータコンシューマー間のメタデータの相互運用性を容易にします。 FHIR データを CDM フォルダーに移動する前に、データをテーブル構成に変換することができます。

### <a name="generating-table-configuration"></a>テーブル構成を生成しています

複製するリポジトリは、すべてのスクリプトとソースコードを取得します。 `npm install`依存関係をインストールするには、を使用します。 フォルダーから次のコマンドを実行し `Configuration-Generator` て、YAML 形式の指示に従ってテーブル構成フォルダーを生成します。

```bash
Configuration-Generator> node .\generate_from_yaml.js -r {resource configuration file} -p {properties group file} -o {output folder}
```

サンプルファイルを使用することができ、 `YAML` `resourcesConfig.yml` `propertiesGroupConfig.yml` リポジトリに用意されています。

### <a name="generating-adf-pipeline"></a>ADF パイプラインを生成しています

生成されたテーブル構成の内容とその他のいくつかの構成を使用して、ADF パイプラインを生成できるようになりました。 この ADF パイプラインは、トリガーされると、API を使用して FHIR サーバーからデータをエクスポート `$export` し、関連付けられている CDM メタデータと共に CDM フォルダーに書き込みます。

1. Azure Active Directory (AD) アプリケーションとサービスプリンシパルを作成します。 ADF パイプラインは、Azure バッチ サービスを使用して変換を実行し、バッチ サービスAzure ADアプリケーションを必要とします。 ドキュメント [Azure AD従ってください](../../active-directory/develop/howto-create-service-principal-portal.md)。
2. エクスポート ストレージの場所へのアクセス権をサービス プリンシパルに付与します。 エクスポート ストレージ `Access Control` の で、アプリケーションに `Storage Blob Data Contributor` ロールを付与Azure ADします。
3. エグレス パイプラインをデプロイします。 Azure でのカスタム `fhirServiceToCdm.json` デプロイにテンプレートを使用します。 この手順では、次の Azure リソースを作成します。
    - という名前の ADF パイプライン `{pipelinename}-df` 。
    - クライアント シークレットを格納する名前 `{pipelinename}-kv` のキー コンテナー。
    - 変換を実行する名前 `{pipelinename}batch` を持つバッチ アカウント。
    - という名前のストレージ アカウント `{pipelinename}storage` 。
4. アプリケーションへのアクセス権を付与Azure Data Factory。 FHIR サービスのアクセス 制御パネルで、データ ファクトリに ロールと `FHIR data exporter` `FHIR data reader` ロールを付与します `{pipelinename}-df` 。
5. テーブル構成フォルダーの内容を構成コンテナーにアップロードします。
6. に移動 `{pipelinename}-df` し、パイプラインをトリガーします。 エクスポートされたデータは、ストレージ アカウント の CDM フォルダーに表示されます `{pipelinename}storage` 。 CSV ファイルを含む各テーブルに 1 つのフォルダーが表示されます。

### <a name="from-cdm-folder-to-synapse"></a>CDM フォルダーから Synapse へ

CDM 形式でエクスポートされ、ADL Gen 2 ストレージに格納されたデータを取得したら、CDM フォルダー内のデータを Synapse に移動できます。

次のような構成ファイルを使用して、Synapse パイプラインへの CDM を作成できます。

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

上記の構成ファイルを使用して、次のスクリプトを実行します。

```bash
.\DeployCdmToSynapsePipeline.ps1 -Config: config.json
```

Sql ユーザーとして ADF マネージ Id を SQL データベースに追加します。 ユーザーと割り当てロールを作成するためのサンプル SQL スクリプトを次に示します。

```sql
CREATE USER [datafactory-name] FROM EXTERNAL PROVIDER
GO
EXEC sp_addrolemember db_owner, [datafactory-name]
GO
```

## <a name="next-steps"></a>次のステップ

この記事では、を使用して `$export` ADL Gen 2 blob storage にデータを移動し、データを SYNAPSE SQL プールに読み込み、(2) fhir データを CDM フォルダーに移動するために ADF パイプラインを使用して Synapse に移動するために、2つの異なる方法で FHIR データを Synapse に移動します。

次に、Synapse にデータを移動しながら、医療情報が保護されていることを確認するために、FHIR データの匿名化について学習できます。
 
>[!div class="nextstepaction"]
>[識別されていないデータのエクスポート](./de-identified-export.md)








