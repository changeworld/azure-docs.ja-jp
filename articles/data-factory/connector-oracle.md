---
title: Oracle をコピー先またはコピー元としてデータをコピーする
titleSuffix: Azure Data Factory & Azure Synapse
description: Data Factory または Azure Synapse Analytics パイプラインを使用して、サポートされるソース ストアから Oracle データベースにデータをコピーしたり、Oracle からサポートされるシンク ストアにコピーしたりする方法について説明します。
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: a7cd2530762ae3045e2bcd6302e4e5f31f9dd993
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777946"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Azure Data Factory または Azure Synapse Analytics を使用して Oracle との間でデータをコピーする

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-onprem-oracle-connector.md)
> * [現在のバージョン](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Oracle データベースをコピー先またはコピー元としてデータをコピーする方法について説明します。 これは、[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Oracle コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Oracle データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 サポートされている任意のデータ ソース ストアから Oracle データベースにデータをコピーすることもできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Oracle コネクタでは以下がサポートされています。

- Oracle データベースの次のバージョン:
    - Oracle 19c R1 (19.1) 以降
    - Oracle 18c R1 (18.1) 以降
    - Oracle 12c R1 (12.1) 以降
    - Oracle 11g R1 (11.1) 以降
    - Oracle 10g R1 (10.1) 以降
    - Oracle 9i R2 (9.2) 以降
    - Oracle 8i R3 (8.1.7) 以降
    - Oracle Database Cloud Exadata Service
- Oracle ソースからの並列コピー。 詳細については、「[Oracle からの並列コピー](#parallel-copy-from-oracle)」セクションを参照してください。

> [!Note]
> Oracle プロキシ サーバーはサポートされていません。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)] 

統合ランタイムには、組み込みの Oracle ドライバーがあります。 そのため、Oracle をコピー元またはコピー先としてデータをコピーするときに、ドライバーを手動でインストールする必要はありません。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-oracle-using-ui"></a>UI を使用して Oracle のリンク サービスを作成する

次の手順を使用して、Azure portal UI で Oracle のリンク サービスを作成します。

1. Azure Data Factory または Synapse ワークスペースの [管理] タブに移動し、[リンク サービス] を選択して、[新規] をクリックします。

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Azure Data Factory の UI で新しいリンク サービスを作成するスクリーンショット。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Azure Synapse の UI を使用した新しいリンク サービスの作成を示すスクリーンショット。":::

2. Oracle を検索し、Oracle コネクタを選択します。

    :::image type="content" source="media/connector-oracle/oracle-connector.png" alt-text="Oracle コネクタのスクリーンショット。":::    

1. サービスの詳細を構成し、接続をテストして、新しいリンク サービスを作成します。

    :::image type="content" source="media/connector-oracle/configure-oracle-linked-service.png" alt-text="Oracle のリンク サービスの構成のスクリーンショット。":::

## <a name="connector-configuration-details"></a>コネクタの構成の詳細

次のセクションでは、Oracle コネクタに固有のエンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Oracle のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **Oracle** に設定する必要があります。 | はい |
| connectionString | Oracle Database インスタンスに接続するために必要な情報を指定します。 <br/>パスワードを Azure Key Vault に格納して、接続文字列から `password` 構成をプルすることもできます。 詳細については、下記の例と、「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」を参照してください。 <br><br>**サポートされる接続の種類**:**Oracle SID** または **Oracle サービス名** を使用してデータベースを識別できます。<br>- SID を使用する場合: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- サービス名を使用する場合: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>Oracle ネイティブ接続オプションの詳細については、Oracle サーバー上の [TNSNAMES.ORA](http://www.orafaq.com/wiki/Tnsnames.ora) にエントリを追加することを選択できます。また Oracle のリンク サービスでは、Oracle サービス名の接続の種類を使用することを選択して、対応するサービス名を構成できます。 | はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

>[!TIP]
>"ORA-01025:UPI パラメーターの値が有効範囲外です" というエラーが発生し、Oracle のバージョンが 8i である場合は、接続文字列に `WireProtocolMode=1` を追加してください。 その後、やり直してください。

フェールオーバー シナリオ用の Oracle インスタンスが複数ある場合は、Oracle がリンクされたサービスを作成し、プライマリ ホスト、ポート、ユーザー名、パスワードなどを入力できます。プロパティ名 `AlternateServers`、値 `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` の "**追加接続プロパティ**" を新しく追加します。このとき、角かっこを忘れずに入力すること、区切り文字としてコロン (`:`) を使用することに注意してください。 以下の例では、代替サーバーの値により、接続フェールオーバー用の代替データベース サーバーが 2 台定義されます。`(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)`

接続文字列には他にも、ケースに応じてさまざまな接続プロパティを設定できます。それらのプロパティを次に示します。

| プロパティ | 説明 | 使用できる値 |
|:--- |:--- |:--- |
| ArraySize |1 回のネットワーク ラウンド トリップでコネクタがフェッチできるバイト数。 例: `ArraySize=‭10485760‬`。<br/><br/>値を大きくすると、ネットワーク経由でデータをフェッチする回数が減り、スループットが向上します。 値を小さくすると、サーバーがデータを転送する際の待ち時間がわずかにあるため、応答時間が長くなります。 | 1 から 4294967296 (4 GB) の整数。 既定値は `60000` です。 この値が 1 である場合、バイト数は定義されません。ちょうど 1 行分のデータの領域を割り当てることを意味します。 |

Oracle の接続で暗号化を有効にするには、2 つのオプションがあります。

-   **Triple-DES Encryption (3DES) と Advanced Encryption Standard (AES)** を使用するには、Oracle サーバー側で Oracle Advanced Security (OAS) に移動し、暗号化の設定を構成します。 詳細については、こちらの [Oracle のドキュメント](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)を参照してください。 Oracle Application Development Framework (ADF) コネクタは暗号化方法を自動的にネゴシエートし、Oracle への接続を確立するときにユーザーが OAS で構成した方法を使用します。

-   **TLS** を使用するには以下の手順に従ってください。

    1.  TLS/SSL 証明書情報を取得します。 TLS/SSL 証明書の Distinguished Encoding Rules (DER) でエンコードされた証明書情報を取得し、出力 (----- Begin Certificate … End Certificate -----) をテキスト ファイルとして保存します。

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **例:** DERcert.cer から証明書情報を抽出し、出力を cert.txt に保存します。

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  `keystore` または `truststore` をビルドします。 次のコマンドでは、PKCS-12 形式のパスワード含まれる、または含まれない、`truststore` ファイルが作成されます。

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **例:** パスワードを含む MyTrustStoreFile という名前の PKCS12 `truststore` ファイルを作成します。

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  セルフホステッド IR マシンに `truststore` ファイルを配置します。 たとえば、C:\MyTrustStoreFile にファイルを配置します。
    4.  サービスで、`EncryptionMethod=1` および対応する `TrustStore`/`TrustStorePassword` 値を含む Oracle 接続文字列を構成します。 たとえば、「 `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>` 」のように入力します。

**例:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: パスワードを Azure Key Vault に格納する**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```
## <a name="dataset-properties"></a>データセットのプロパティ

このセクションでは、Oracle データセットでサポートされるプロパティの一覧を示します。 データセットの定義に使用できるセクションとプロパティの一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

Oracle をコピー元またはコピー先としてデータをコピーするには、データセットの type プロパティを `OracleTable` に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは `OracleTable` に設定する必要があります。 | はい |
| schema | スキーマの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| table | テーブル/ビューの名前。 |ソースの場合はいいえ、シンクの場合ははい  |
| tableName | スキーマがあるテーブル/ビューの名前。 このプロパティは下位互換性のためにサポートされています。 新しいワークロードでは、`schema` と `table` を使用します。 | ソースの場合はいいえ、シンクの場合ははい |

**例:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Oracle のソースとシンクでサポートされるプロパティの一覧を示します。 アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関するページを参照してください。 

### <a name="oracle-as-source"></a>ソースとしての Oracle

>[!TIP]
>データ パーティション分割を使用して、Oracle からデータを効率的に読み込む方法の詳細については、「[Oracle からの並列コピー](#parallel-copy-from-oracle)」を参照してください。

Oracle からデータをコピーするは、コピー アクティビティのソースの種類を `OracleSource` に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは `OracleSource` に設定する必要があります。 | はい |
| oracleReaderQuery | カスタム SQL クエリを使用してデータを読み取ります。 たとえば `"SELECT * FROM MyTable"` です。<br>パーティション分割された読み込みを有効にするときは、クエリ内で対応する組み込みのパーティション パラメーターをすべてフックする必要があります。 例については、「[Oracle からの並列コピー](#parallel-copy-from-oracle)」セクションを参照してください。 | いいえ |
| partitionOptions | Oracle からのデータの読み込みに使用されるデータ パーティション分割オプションを指定します。 <br>使用できる値は、以下のとおりです。**None** (既定値)、**PhysicalPartitionsOfTable**、および **DynamicRange**。<br>パーティション オプションが有効になっている場合 (つまり、`None` ではない場合)、Oracle データベースから同時にデータを読み込む並列処理の次数は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) の設定によって制御されます。 | いいえ |
| partitionSettings | データ パーティション分割の設定のグループを指定します。 <br>パーティション オプションが `None` でない場合に適用されます。 | いいえ |
| partitionNames | コピーする必要がある物理パーティションのリスト。 <br>パーティション オプションが `PhysicalPartitionsOfTable` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfTabularPartitionName` をフックします。 例については、「[Oracle からの並列コピー](#parallel-copy-from-oracle)」セクションを参照してください。 | いいえ |
| partitionColumnName | 並列コピーの範囲パーティション分割で使用される **整数型** のソース列の名前を指定します。 指定されていない場合は、テーブルの主キーが自動検出され、パーティション列として使用されます。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionColumnName` をフックします。 例については、「[Oracle からの並列コピー](#parallel-copy-from-oracle)」セクションを参照してください。 | いいえ |
| partitionUpperBound | データをコピーするパーティション列の最大値。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionUpbound` をフックします。 例については、「[Oracle からの並列コピー](#parallel-copy-from-oracle)」セクションを参照してください。 | いいえ |
| partitionLowerBound | データをコピーするパーティション列の最小値。 <br>パーティション オプションが `DynamicRange` である場合に適用されます。 クエリを使用してソース データを取得する場合は、WHERE 句で `?AdfRangePartitionLowbound` をフックします。 例については、「[Oracle からの並列コピー](#parallel-copy-from-oracle)」セクションを参照してください。 | いいえ |

**例: パーティションなしで基本的なクエリを使用してデータをコピーする**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>シンクとしての Oracle

Oracle にデータをコピーするには、コピー アクティビティのシンクの種類を `OracleSink` に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは、`OracleSink` に設定する必要があります。 | はい |
| writeBatchSize | バッファー サイズが `writeBatchSize` に達したら、SQL テーブルにデータを挿入します。<br/>使用可能な値: 整数 (行数)。 |いいえ (既定値は 10,000) |
| writeBatchTimeout | タイムアウトする前に一括挿入操作の完了を待つ時間です。<br/>使用可能な値: 期間。 たとえば "00:30:00" (30 分) を指定できます。 | いいえ |
| preCopyScript | コピー アクティビティの毎回の実行で、データを Oracle に書き込む前に実行する SQL クエリを指定します。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 | いいえ |
| maxConcurrentConnections |アクティビティの実行中にデータ ストアに対して確立されたコンカレント接続数の上限。 コンカレント接続を制限する場合にのみ、値を指定します。| いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Oracle からの並列コピー

Oracle コネクタでは、Oracle からデータを並列コピーするために、組み込みのデータ パーティション分割を提供します。 データ パーティション分割オプションは、コピー アクティビティの **[ソース]** タブにあります。

:::image type="content" source="./media/connector-oracle/connector-oracle-partition-options.png" alt-text="パーティションのオプションのスクリーンショット":::

パーティション分割されたコピーを有効にすると、サービスによって Oracle ソースに対する並列クエリが実行され、パーティションごとにデータが読み込まれます。 並列度は、コピー アクティビティの [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定によって制御されます。 たとえば、`parallelCopies` を 4 に設定した場合、指定したパーティション オプションと設定に基づいて 4 つのクエリが同時に生成され、実行されます。各クエリでは、Oracle データベースからデータの一部を取得します。

特に、Oracle データベースから大量のデータを読み込む場合は、データ パーティション分割を使用した並列コピーを有効にすることをお勧めします。 さまざまなシナリオの推奨構成を以下に示します。 ファイルベースのデータ ストアにデータをコピーする場合は、複数のファイルとしてフォルダーに書き込む (フォルダー名のみを指定する) ことをお勧めします。この場合、1 つのファイルに書き込むよりもパフォーマンスが優れています。

| シナリオ                                                     | 推奨設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 物理パーティションに分割された大きなテーブル全体から読み込む。          | **パーティション オプション**: テーブルの物理パーティション。 <br><br/>実行中に、サービスによって物理パーティションが自動的に検出され、パーティションごとにデータがコピーされます。 |
| 物理パーティションがなく、データ パーティション分割用の整数列がある大きなテーブル全体から読み込む。 | **パーティション オプション**: 動的範囲パーティション。<br>**パーティション列**: データのパーティション分割に使用される列を指定します。 指定されていない場合は、主キー列が使用されます。 |
| カスタム クエリを使用して大量のデータを読み込む (物理パーティションがある場合)。 | **パーティション オプション**: テーブルの物理パーティション。<br>**クエリ**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`<br>**パーティション名**: データのコピー元のパーティション名を指定します。 指定されていない場合は、Oracle データセットで指定したテーブルの物理パーティションがサービスによって自動検出されます。<br><br>実行中に、サービスによって `?AdfTabularPartitionName` が実際のパーティション名に置き換えられ、Oracle に送信されます。 |
| カスタム クエリを使用して大量のデータを読み込む (物理パーティションがなく、データ パーティション分割用の整数列がある場合)。 | **パーティション オプション**: 動的範囲パーティション。<br>**クエリ**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`<br>**パーティション列**: データのパーティション分割に使用される列を指定します。 整数データ型の列に対してパーティション分割を実行できます。<br>**パーティションの上限** と **パーティションの下限**: パーティション列に対してフィルター処理を実行して、下限から上限までの範囲内のデータのみを取得する場合に指定します。<br><br>実行中に、サービスによって `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound`、`?AdfRangePartitionLowbound` が各パーティションの実際の列名および値の範囲に置き換えられ、Oracle に送信されます。 <br>たとえば、パーティション列 "ID" で下限が 1、上限が 80 に設定され、並列コピーが 4 に設定されている場合、サービスは 4 つのパーティションでデータを取得します。 これらの ID の範囲はそれぞれ [1, 20]、[21, 40]、[41, 60]、[61, 80] です。 |

> [!TIP]
> パーティション分割されていないテーブルからデータをコピーするときは、"動的範囲" パーティション オプションを使用して、整数列に対してパーティション分割を行うことができます。 ソース データにこのような種類の列が含まれていない場合は、ソース クエリで [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) 関数を利用して列を生成し、それをパーティション列として使用できます。

**例: 物理パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**例: 動的範囲パーティションを使用してクエリを実行する**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Oracle のデータ型マッピング

Oracle をコピー元またはコピー先としてデータをコピーするとき、次の中間データ型のマッピングがサービス内で使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Oracle データ型 | 中間データ型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(Oracle 10g 以上でのみサポート) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal、String (有効桁数が 28 を超える場合) |
| INTEGER |Decimal、String (有効桁数が 28 を超える場合) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER (p,s) |Decimal、String (p が 28 を超える場合) |
| 有効桁数と小数点以下桁数がない NUMBER |Double |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| timestamp |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| 符号なし INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> INTERVAL YEAR TO MONTH データ型と INTERVAL DAY TO SECOND データ型はサポートされません。

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
コピー アクティビティによってソース、シンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
