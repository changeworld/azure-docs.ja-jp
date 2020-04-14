---
title: LightIngest は、Azure Data Explorer へのインジェストのためのコマンドライン ユーティリティです。
description: Azure Data Explorer へのアドホック データ インジェストのためのコマンドライン ユーティリティである LightIngest について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 95d943685cf511acb88f9e48d36a9dd43b0a27d2
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548018"
---
# <a name="install-and-use-lightingest"></a>LightIngest のインストールと使用

LightIngest は、Azure Data Explorer へのアドホック データ インジェストのためのコマンドライン ユーティリティです。
このユーティリティは、ローカル フォルダーまたは Azure Blob Storage コンテナーからソース データをプルできます。

## <a name="prerequisites"></a>前提条件

* LightIngest - [Microsoft.Azure.Kusto.Tools NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)の一部としてダウンロードします

    ![Lightingest のダウンロード](media/lightingest/lightingest-download-area.png)

* WinRAR - [www.win-rar.com/download.html](http://www.win-rar.com/download.html) からダウンロードします

## <a name="install-lightingest"></a>LightIngest をインストールする

1. LightIngest をダウンロードしたコンピューター上の場所に移動します。 
1. WinRAR を使用して、*tools* ディレクトリをコンピューターに抽出します。

## <a name="run-lightingest"></a>LightIngest を実行する

1. コンピューター上の抽出した *tools* ディレクトリに移動します。
1. 場所バーから既存の場所情報を削除します。
    
      ![場所情報を削除する](media/lightingest/lightingest-location-bar.png)

1. 「`cmd`」と入力して、**Enter** キーを押します。
1. コマンド プロンプトで、「`LightIngest.exe`」に続けて関連するコマンドライン引数を入力します。

    > [!Tip]
    > サポートされているコマンド ライン引数を確認するには、「`LightIngest.exe /help`」と入力します。
    >
    >![コマンド ライン ヘルプ](media/lightingest/lightingest-cmd-line-help.png)

1. 「`ingest-`」に続けて、インジェストを管理する Azure Data Explorer クラスターへの接続文字列を入力します。
    接続文字列を二重引用符で囲み、その後に [Kusto 接続文字列の指定](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto)を入力します。

    次に例を示します。
    ```
    ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* 推奨される方法は、`https://ingest-{yourClusterNameAndRegion}.kusto.windows.net` のインジェスト エンドポイントで LightIngest を動作させることです。 これにより、Azure Data Explorer サービスはインジェストの負荷を管理でき、一時的なエラーから簡単に回復できます。 ただし、エンジン エンドポイント (`https://{yourClusterNameAndRegion}.kusto.windows.net`) で直接動作するように LightIngest を構成することもできます。

> [!Note]
> エンジン エンドポイントを直接インジェストする場合は、`ingest-` を含める必要はありませんが、エンジンを保護し、インジェストの成功率を向上させる DM 機能はありません。

* インジェストのパフォーマンスを最適にするには、LightIngest で生データのサイズが認識され、圧縮されていないローカル ファイルのサイズを推定できるようにすることが重要です。 ただし、LightIngest では、先にダウンロードしない限り、圧縮された BLOB の生のサイズを正しく見積もることができないことがあります。 したがって、圧縮された BLOB を取り込むときは、BLOB メタデータの `rawSizeBytes` プロパティを圧縮されていないデータ サイズ (バイト単位) に設定します。

## <a name="general-command-line-arguments"></a>一般的なコマンドライン引数

|引数名         |短い名前   |Type    |Mandatory |説明                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |string  |Mandatory |インジェストを処理する Kusto エンドポイントを指定する [Azure Data Explorer 接続文字列](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto)。 二重引用符で囲む必要があります |
|-database             |-db          |string  |省略可能  |対象の Azure Data Explorer データベースの名前 |
|-table                |             |string  |Mandatory |対象の Azure Data Explorer テーブルの名前 |
|-sourcePath           |-source      |string  |Mandatory |ソース ファイルまたは BLOB コンテナーのルート URI へのパス。 データが BLOB 内にある場合は、ストレージ アカウント キーまたは SAS が含まれている必要があります。 二重引用符で囲むことをお勧めします |
|-prefix               |             |string  |省略可能  |取り込むソース データが Blob Storage に存在する場合、この URL プレフィックスが、コンテナー名を除くすべての BLOB で共有されます。 <br>たとえば、データが `MyContainer/Dir1/Dir2` にある場合は、プレフィックスを `Dir1/Dir2` にする必要があります。 二重引用符で囲むことをお勧めします |
|-pattern              |             |string  |省略可能  |ソース ファイルまたは BLOB を選択するパターン。 ワイルドカードがサポートされます。 たとえば、「 `"*.csv"` 」のように入力します。 二重引用符で囲むことをお勧めします |
|-zipPattern           |             |string  |省略可能  |取り込む ZIP アーカイブ内のファイルを選択するときに使用する正規表現。<br>アーカイブ内の他のファイルはすべて無視されます。たとえば、`"*.csv"` などです。 二重引用符で囲むことをお勧めします |
|-format               |-f           |string  |省略可能  |ソース データの形式。 [サポートされている形式](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats)のいずれかである必要があります |
|-ingestionMappingPath |-mappingPath |string  |省略可能  |インジェスト列マッピング ファイルへのパス (Json および Avro 形式の場合は必須)。 「[データ マッピング](https://docs.microsoft.com/azure/kusto/management/mappings)」を参照してください |
|-ingestionMappingRef  |-mappingRef  |string  |省略可能  |事前に作成されたインジェスト列マッピングの名前 (Json および Avro 形式の場合は必須)。 「[データ マッピング](https://docs.microsoft.com/azure/kusto/management/mappings)」を参照してください |
|-creationTimePattern  |             |string  |省略可能  |設定した場合、ファイルまたは BLOB のパスから CreationTime プロパティを抽出するために使用されます。 「[CreationTimePattern 引数の使用](#using-creationtimepattern-argument)」を参照してください |
|-ignoreFirstRow       |-ignoreFirst |[bool]    |省略可能  |設定した場合、各ファイルまたは BLOB の最初のレコードが無視されます (たとえば、ソース データにヘッダーが含まれている場合) |
|-tag                  |             |string  |省略可能  |取り込まれたデータと関連付けるための[タグ](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging)。 複数回の出現が許可されています |
|-dontWait             |             |[bool]    |省略可能  |"true" に設定した場合、インジェストの完了を待機しません。 大量のファイルまたは BLOB を取り込むときに便利です |

### <a name="using-creationtimepattern-argument"></a>CreationTimePattern 引数の使用

`-creationTimePattern` 引数を指定すると、ファイルまたは BLOB のパスから CreationTime プロパティが抽出されます。 パターンに項目のパス全体を反映する必要はなく、使用するタイムスタンプを囲むセクションだけでかまいません。

引数の値には次の値を含める必要があります。
* 単一引用符で囲まれた、タイムスタンプの直前の定数テスト
* タイムスタンプ形式 (標準 [.NET DateTime 表記](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings))
* タイムスタンプの直後の定数テキスト。 たとえば、BLOB 名が `historicalvalues19840101.parquet` で終わる場合 (タイムスタンプは年の 4 桁、月の 2 桁、月の日にちの 2 桁で構成されています)、`-creationTimePattern` 引数の対応する値は次のようになります。

```
ingest-{Cluster name and region}.kusto.windows.net;AAD Federated Security=True -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -creationTimePattern:"'historicalvalues'yyyyMMdd'.parquet'"
 -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
```

### <a name="command-line-arguments-for-advanced-scenarios"></a>高度なシナリオのためのコマンドライン引数

|引数名         |短い名前   |Type    |Mandatory |説明                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-compression          |-cr          |double  |省略可能  |圧縮率のヒント。 圧縮されたファイルまたは BLOB を取り込むときに、Azure Data Explorer が生データのサイズを評価できるようにするのに便利です。 元のサイズを圧縮サイズで割った値として計算されます |
|-limit                |-l           |整数 (integer) |省略可能  |設定した場合、インジェストを最初の N 個のファイルに限定します |
|-listOnly             |-list        |[bool]    |省略可能  |設定した場合、インジェストに選択された項目のみが表示されます| 
|-ingestTimeout        |             |整数 (integer) |省略可能  |すべての取り込み操作が完了するまでのタイムアウト (分)。 既定値は `60` です|
|-forceSync            |             |[bool]    |省略可能  |設定した場合、同期インジェストを強制的に実行します。 既定値は `false` です |
|-dataBatchSize        |             |整数 (integer) |省略可能  |各取り込み操作の合計サイズ制限 (MB、非圧縮) を設定します |
|-filesInBatch         |             |整数 (integer) |省略可能  |各取り込み操作のファイルまたは BLOB の数の制限を設定します |
|-devTracing           |-trace       |string  |省略可能  |設定した場合、診断ログがローカル ディレクトリに書き込まれます (既定では現在のディレクトリの `RollingLogs` ですが、スイッチ値を設定して変更できます) |

## <a name="blob-metadata-properties"></a>BLOB メタデータのプロパティ
Azure BLOB で使用する場合、LightIngest では特定の BLOB メタデータ プロパティを使用してインジェスト プロセスが拡張されます。

|メタデータのプロパティ                            | 使用法                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | 設定すると、非圧縮データ サイズとして解釈されます                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | UTC タイムスタンプとして解釈されます。 設定すると、Kusto の作成時刻を上書きするために使用されます。 バックフィル シナリオに役立ちます |

## <a name="usage-examples"></a>使用例

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>ストレージ アカウント キーまたは SAS トークンを使用して BLOB を取り込む

* ストレージ アカウント `ACCOUNT`、フォルダー `DIR`、コンテナー `CONT`、一致パターン `*.csv.gz` を指定して、10 個の BLOB を取り込みます
* 取り込み先はデータベース `DB`のテーブル `TABLE` であり、取り込み先にインジェスト マッピング `MAPPING` が事前に作成されています
* ツールは、取り込み操作が完了するまで待機します
* 以下を指定するための異なるオプションに注意してください: ターゲット データベース、ストレージ アカウント キーと SAS トークン

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>ヘッダー行を含めずに、コンテナー内のすべての BLOB を取り込む

* ストレージ アカウント `ACCOUNT`、フォルダー `DIR1/DIR2`、コンテナー `CONT`、一致パターン `*.csv.gz` を指定して、すべての BLOB を取り込みます
* 取り込み先はデータベース `DB`のテーブル `TABLE` であり、取り込み先にインジェスト マッピング `MAPPING` が事前に作成されています
* ソース BLOB にはヘッダー行が含まれるため、各 BLOB の最初のレコードを削除するようツールに指示します
* ツールは、インジェストのためのデータを送信し、取り込み操作が完了するのを待機しません

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>パスからすべての JSON ファイルを取り込む

* パス `PATH` の下にある、パターン `*.json` に一致するすべてのファイルを取り込みます
* 取り込み先はデータベース `DB`のテーブル `TABLE` であり、インジェスト マッピングはローカル ファイル `MAPPING_FILE_PATH` で定義されています
* ツールは、インジェストのためのデータを送信し、取り込み操作が完了するのを待機しません

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>ファイルを取り込んで、診断トレース ファイルを書き込む

* パス `PATH` の下にある、パターン `*.json` に一致するすべてのファイルを取り込みます
* 取り込み先はデータベース `DB`のテーブル `TABLE` であり、インジェスト マッピングはローカル ファイル `MAPPING_FILE_PATH` で定義されています
* ツールは、インジェストのためのデータを送信し、取り込み操作が完了するのを待機しません
* 診断トレース ファイルは、ローカル環境の `LOGS_PATH` フォルダーの下に書き込まれます

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>変更ログ
|Version        |[変更点]                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>`-zipPattern` 引数を追加しました</li><li>`-listOnly` 引数を追加しました</li><li>実行が開始される前に、引数の概要が表示されます</li><li>`-creationTimePattern` 引数に従って、BLOB メタデータ プロパティから、または BLOB 名またはファイル名から、CreationTime が読み取られます</li></ul>|
