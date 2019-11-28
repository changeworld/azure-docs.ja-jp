---
title: Azure Data Explorer と Azure Data Factory の統合
description: このトピックでは、Azure Data Explorer を Azure Data Factory と統合して、コピー、検索、およびコマンドのアクティビティを使用します
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tomersh26
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: dd2b3bd584bb39810e0a5c9acde1a961330c273d
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093229"
---
# <a name="integrate-azure-data-explorer-with-azure-data-factory"></a>Azure Data Explorer と Azure Data Factory の統合

[Azure Data Factory](/azure/data-factory/) (ADF) は、異なるデータ ストアを統合し、データに対してアクティビティを実行できるクラウドベースのデータ統合サービスです。 ADF を使用して、データ移動とデータ変換を調整し、自動化するためのデータ駆動型ワークフローを作成できます。 Azure Data Explorer は、Azure Data Factory 内で[サポートされるデータ ストア](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)の 1 つです。 

## <a name="azure-data-factory-activities-for-azure-data-explorer"></a>Azure Data Explorer に対する Azure Data Factory アクティビティ

Azure Data Explorer ユーザーは、Azure Data Factory とのさまざまな統合を利用できます。

### <a name="copy-activity"></a>コピー アクティビティ
 
Azure Data Factory コピー アクティビティは、データ ストア間でデータを転送するために使用されます。 Azure Data Explorer は、Azure Data Explorer からサポートされている任意のデータ ストアにデータがコピーされるソースとして、また、サポートされる任意のデータ ストアから Azure Data Explorer にデータがコピーされるシンクとしてサポートされています。 詳細については、「[Azure Data Factory を使用して Azure Data Explorer をコピー先またはコピー元としてデータをコピーする](/azure/data-factory/connector-azure-data-explorer)」を参照してください。 詳細なチュートリアルについては、[Azure Data Factory から Azure Data Explorer へのデータの読み込み](data-factory-load-data.md)に関する記事を参照してください。
Azure Data Explorer は、Azure 内でデータをコピーするときに使用される Azure IR (Integration Runtime) と、オンプレミス、または Azure Virtual Network などのアクセス制御が可能なネットワーク内にあるデータ ストアとの間でデータをコピーするときに使用されるセルフホステッド IR によってサポートされています。 詳細については、[使用する IR](/azure/data-factory/concepts-integration-runtime#determining-which-ir-to-use) に関する記事をご覧ください
 
> [!TIP]
> コピー アクティビティを使用し、**リンクされたサービス**または**データセット**を作成する場合は、古いデータ ストア **Kusto** ではなく、データ ストア **Azure Data Explorer (Kusto)** を選択します。  

### <a name="lookup-activity"></a>ルックアップ アクティビティ
 
ルックアップ アクティビティは、Azure Data Explorer 上でクエリを実行するために使用されます。 クエリの結果は、ルックアップ アクティビティの出力として返され、[ADF ルックアップに関するドキュメント](/azure/data-factory/control-flow-lookup-activity#use-the-lookup-activity-result-in-a-subsequent-activity)で説明されているように、パイプライン内の次のアクティビティで使用できます。  
応答サイズの上限である 5,000 行と 2 MB に加え、アクティビティには 1 時間のクエリ タイムアウト制限もあります。

### <a name="command-activity"></a>コマンド アクティビティ

コマンド アクティビティを使用すると、Azure Data Explorer [管理コマンド](/azure/kusto/concepts/#control-commands)を実行できます。 クエリとは異なり、管理コマンドではデータまたはメタデータを変更できる可能性があります。 一部の管理コマンドは、`.ingest` や `.set-or-append` などのコマンドを使用して Azure Data Explorer にデータを取り込むこと、または `.export` などのコマンドを使用して Azure Data Explorer から外部データ ストアにデータをコピーすることを目的としています。
コマンド アクティビティの詳細なチュートリアルについては、「[Azure Data Factory コマンド アクティビティを使用して Azure Data Explorer 制御コマンドを実行する](data-factory-command-activity.md)」を参照してください。  管理コマンドを使用したデータのコピーは、コピー アクティビティよりも高速でコストの低いオプションになることがあります。 コマンド アクティビティとコピー アクティビティのどちらを使用するかを決定するには、[データをコピーするときのコピー アクティビティとコマンド アクティビティ間での選択](#select-between-copy-and-azure-data-explorer-command-activities-when-copy-data)に関するページを参照してください。

### <a name="copy-in-bulk-from-a-database-template"></a>データベース テンプレートから一括コピーする

「[Azure Data Factory テンプレートを使用してデータベースから Azure Data Explorer に一括コピーする](data-factory-template.md)」は、事前定義された Azure Data Factory パイプラインです。 テンプレートを使用して、データベースまたはテーブルごとにさまざまなパイプラインを作成し、データのコピーを高速化します。 

## <a name="select-between-copy-and-azure-data-explorer-command-activities-when-copy-data"></a>データをコピーするときにコピーと Azure Data Explorer コマンド アクティビティ間で選択する 

このセクションでは、データ コピーのニーズに合わせて適切なアクティビティを選択する方法について説明します。

Azure Data Explorer との間でデータをコピーする場合、Azure Data Factory では次の 2 つのオプションを使用できます。
* コピー アクティビティ。
* Azure Data Explorer 内でデータを転送する管理コマンドの 1 つを実行する Azure Data Explorer コマンド アクティビティ。 

### <a name="copy-data-from-azure-data-explorer"></a>Azure Data Explorer からデータをコピーする
  
コピー アクティビティまたは [`.export`](/azure/kusto/management/data-export/) コマンドを使用して、Azure Data Explorer からデータをコピーできます。 `.export` コマンドでは、クエリを実行してから、クエリの結果をエクスポートします。 

Azure Data Explorer からデータをコピーするためのコピー アクティビティと `.export` コマンドの比較については、次の表を参照してください。

| | コピー アクティビティ | .export コマンド |
|---|---|---|
| **フローの説明** | ADF により Kusto に対してクエリが実行され、結果が処理されてターゲット データ ストアに送信されます。 <br>(**ADX > ADF > シンク データ ストア**) | ADF により、コマンドを実行し、ターゲット データ ストアにデータを直接送信する `.export` 管理コマンドが Azure Data Explorer に送信されます。 <br>(**ADX > シンク データ ストア**) |
| **サポートされるターゲット データ ストア** | さまざまな[サポートされるデータ ストア](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLSv2、Azure BLOB、SQL Database |
| **パフォーマンス** | 一元化 | <ul><li>分散 (既定)。複数のノードからデータを同時にエクスポート</li><li>高速で、COGS が効率的。</li></ul> |
| **サーバーの制限** | [クエリの制限](/azure/kusto/concepts/querylimits)を拡張/無効化できます。 既定では、ADF クエリには以下が含まれます。 <ul><li>500,000 レコードまたは 64 MB のサイズ制限。</li><li>10 分の時間制限。</li><li>`noTruncation` が false に設定されている。</li></ul> | 既定では、クエリの制限を拡張または無効にします。 <ul><li>サイズ制限が無効になっている。</li><li>サーバー タイムアウトが 1 時間に延長される。</li><li>`MaxMemoryConsumptionPerIterator` と `MaxMemoryConsumptionPerQueryPerNode` が最大値 (5 GB、TotalPhysicalMemory/2) に拡張される。</li></ul>

> [!TIP]
> コピー先が `.export` コマンドでサポートされているデータ ストアの 1 つであり、どのコピー アクティビティ機能もご自身のニーズにとって重要でない場合は、`.export` コマンドを選択します。

### <a name="copying-data-to-azure-data-explorer"></a>Azure Data Explorer へのデータのコピー

コピー アクティビティ、または[クエリからの取り込み](/azure/kusto/management/data-ingestion/ingest-from-query) (`.set-or-append`、`.set-or-replace`、`.set`、`.replace)` や[ストレージからの取り込み](/azure/kusto/management/data-ingestion/ingest-from-storage) (`.ingest`) などの取り込みコマンドを使用して、Azure Data Explorer にデータをコピーできます。 

Azure Data Explorer にデータをコピーするためのコピー アクティビティと取り込みコマンドの比較については、次の表を参照してください。

| | コピー アクティビティ | クエリからの取り込み<br> `.set-or-append` / `.set-or-replace` / `.set` / `.replace` | ストレージからの取り込み <br> `.ingest` |
|---|---|---|---|
| **フローの説明** | ADF により、ソース データ ストアからデータが取得され、それが表形式に変換されて、必要なスキーママッピング変更が行われます。 その後、ADF によってデータが Azure BLOB にアップロードされてチャンクに分割され、BLOB がダウンロードされて ADX テーブルに取り込まれます。 <br> (**ソース データ ストア > ADF > Azure BLOB > ADX**) | これらのコマンドでは、クエリまたは `.show` コマンドを実行し、クエリの結果をテーブルに取り込むことができます (**ADX > ADX**)。 | このコマンドでは、1 つまたは複数のクラウド ストレージの成果物からデータを "プル" することで、データがテーブルに取り込まれます。 |
| **サポートされるソース データ ストア** |  [さまざまなオプション](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) | ADLS Gen 2、Azure BLOB、SQL (sql_request プラグインを使用)、Cosmos (cosmosdb_sql_request プラグインを使用)、および HTTP または Python API を提供するその他のデータ ストア。 | ファイルシステム、Azure Blob Storage、ADLS Gen 1、ADLS Gen 2 |
| **パフォーマンス** | 取り込みはキューに登録されて管理されるため、負荷分散、再試行、エラー処理を提供することで、取り込みのサイズが小さく保たれ、高可用性が保証されます。 | <ul><li>これらのコマンドは、大量のデータをインポートするようには設計されていません。</li><li>予想どおりに動作し、コストが低くなります。 ただし、運用環境のシナリオでは、トラフィック速度とデータ サイズが大きい場合にはコピー アクティビティを使用します。</li></ul> |
| **サーバーの制限** | <ul><li>サイズ制限はありません。</li><li>最大タイムアウト制限:取り込まれた BLOB ごとに 1 時間。 |<ul><li>クエリ部分にはサイズ制限だけがあります。これは、`noTruncation=true` を指定することでスキップできます。</li><li>最大タイムアウト制限:1 時間。</li></ul> | <ul><li>サイズ制限はありません。</li><li>最大タイムアウト制限:1 時間。</li></ul>|

> [!TIP]
> * ADF から Azure Data Explorer にデータをコピーする場合は、`ingest from query` コマンドを使用します。  
> * 大規模なデータ セット (> 1 GB) の場合は、コピー アクティビティを使用します。  

## <a name="required-permissions"></a>必要なアクセス許可

次の表に、Azure Data Factory との統合におけるさまざまな手順に必要なアクセス許可を示します。

| 手順 | Operation | 最小レベルのアクセス許可 | メモ |
|---|---|---|---|
| **リンクされたサービスの作成** | データベース ナビゲーション | "*データベース表示者*" <br>ADF を使用してログインしているユーザーには、データベース メタデータを読み取る権限が必要です。 | ユーザーは、データベース名を手動で指定できます。 |
| | 接続をテスト | "*データベース監視者*" または "*テーブル取り込み者*" <br>サービス プリンシパルには、データベース レベルの `.show` コマンドまたはテーブル レベルの取り込みを実行する権限が必要です。 | <ul><li>TestConnection では、データベースではなく、クラスターへの接続が検証されます。 データベースが存在しない場合でも成功する可能性があります。</li><li>テーブル管理者のアクセス許可では不十分です。</li></ul>|
| **データセットの作成** | テーブル ナビゲーション | "*データベース監視者*" <br>ADF を使用してログインしたユーザーには、データベース レベルの `.show` コマンドを実行する権限が必要です。 | ユーザーは、テーブル名を手動で指定できます。|
| **データセットの作成**または**コピー アクティビティ** | データのプレビュー | "*データベース表示者*" <br>サービス プリンシパルには、データベース メタデータを読み取る権限が必要です。 | | 
|   | スキーマのインポート | "*データベース表示者*" <br>サービス プリンシパルには、データベース メタデータを読み取る権限が必要です。 | ADX が表形式から表形式へのコピーのソースである場合、ユーザーがスキーマを明示的にインポートしなかった場合でも、ADF によってスキーマが自動的にインポートされます。 |
| **シンクとしての ADX** | by-name 列マッピングの作成 | "*データベース監視者*" <br>サービス プリンシパルには、データベース レベルの `.show` コマンドを実行する権限が必要です。 | <ul><li>すべての必須操作は "*テーブル取り込み者*" で機能します。</li><li> 一部のオプションの操作は失敗する場合があります。</li></ul> |
|   | <ul><li>テーブルに CSV マッピングを作成する</li><li>マッピングを削除する</li></ul>| "*テーブル取り込み者*" または "*データベース管理者*" <br>サービス プリンシパルには、テーブルに変更を加える権限が必要です。 | |
|   | データの取り込み | "*テーブル取り込み者*" または "*データベース管理者*" <br>サービス プリンシパルには、テーブルに変更を加える権限が必要です。 | | 
| **ソースとしての ADX** | クエリの実行 | "*データベース表示者*" <br>サービス プリンシパルには、データベース メタデータを読み取る権限が必要です。 | |
| **Kusto コマンド** | | 各コマンドのアクセス許可レベルに従います。 |

## <a name="performance"></a>パフォーマンス 

Azure Data Explorer がソースであり、クエリを含むルックアップ、コピー、またはコマンド アクティビティを使用する場合、パフォーマンス情報に関する「[クエリのベスト プラクティス](/azure/kusto/query/best-practices)」と、[コピー アクティビティに関する ADF のドキュメント](/azure/data-factory/copy-activity-performance)を参照してください。
  
このセクションでは、Azure Data Explorer がシンクであるコピー アクティビティの使用について説明します。 Azure Data Explorer シンクの推定スループットは 11-13 MBps です。 次の表は、Azure Data Explorer シンクのパフォーマンスに影響するパラメーターの詳細を示しています。

| パラメーター | メモ |
|---|---|
| **コンポーネントの地理的距離** | すべてのコンポーネントを同じリージョンに配置します。<ul><li>ソースおよびシンク データ ストア。</li><li>ADF 統合ランタイム。</li><li>お使いの ADX クラスター。</li></ul>少なくとも、お使いの統合ランタイムが ADX クラスターと同じリージョンにあることを確認してください。 |
| **DIU の数** | ADF によって使用される 4 つの DIU ごとに 1 つの VM。 <br>DIU を増やすことは、ソースが複数のファイルを含むファイルベースのストアである場合にのみ役立ちます。 各 VM では、異なるファイルが並列に処理されます。 したがって、1 つの大きなファイルをコピーする場合は、複数の小さなファイルをコピーする場合よりも待ち時間が長くなります。|
|**ADX クラスターの量と SKU** | ADX ノードの数が多いと、取り込み処理時間が短縮されます。|
| **並列処理** | 非常に大量のデータをデータベースからコピーするには、データをパーティション分割し、各パーティションを並列にコピーする ForEach ループを使用するか、[データベースから Azure Data Explorer テンプレートへの一括コピー](data-factory-template.md)を使用します。 注:コピー アクティビティの **[設定]**  >  **[並列処理の次数]** は、ADX には関係ありません。 |
| **データ処理の複雑さ** | 待ち時間は、ソース ファイル形式、列マッピング、および圧縮によって異なります。|
| **統合ランタイムを実行している VM** | <ul><li>Azure のコピーでは、ADF VM とマシン SKU を変更することはできません。</li><li> オンプレミスから Azure へのコピーの場合は、セルフホステッド IR をホストしている VM が十分に強力であることを確認します。</li></ul>|

## <a name="monitor-activity-progress"></a>アクティビティの進行状況の監視

* アクティビティの進行状況を監視する場合、"*書き込まれたデータ*" プロパティは "*読み取られたデータ*" プロパティよりもはるかに大きくなる可能性があります。なぜなら、"*読み取られたデータ*" はバイナリ ファイル サイズに従って計算されるのに対し、"*書き込まれたデータ*" はデータがシリアル化解除および圧縮解除された後にメモリ内のサイズに基づいて計算されるからです。

* アクティビティの進行状況を監視するときに、データが Azure Data Explorer シンクに書き込まれていることを確認できます。 Azure Data Explorer テーブルに対してクエリを実行すると、データが到着していないことがわかります。 これは、Azure Data Explorer にコピーするときに 2 つのステージがあるためです。 
    * 最初のステージでは、ソース データが読み取られ、それが 900 MB のチャンクに分割されて、各チャンクが Azure BLOB にアップロードされます。 最初のステージは、ADF アクティビティの進行状況ビューに表示されます。 
    * 2 番目のステージは、すべてのデータが Azure BLOB にアップロードされた後に開始されます。 Azure Data Explorer エンジン ノードにより、BLOB がダウンロードされ、データがシンク テーブルに取り込まれます。 その後、データが Azure Data Explorer テーブルに表示されます。

## <a name="next-steps"></a>次の手順

* [Azure Data Factory を使用して Azure Data Explorer にデータをコピーする](data-factory-load-data.md)方法について確認する。
* [データベースから Azure Data Explorer への一括コピーに Azure Data Factory テンプレートを使用する](data-factory-template.md)方法について学習する。
* [Azure Data Factory コマンド アクティビティを使用して Azure Data Explorer 制御コマンドを実行する](data-factory-command-activity.md)方法について学習する。
* データのクエリのための [Azure Data Explorer のクエリ](/azure/data-explorer/web-query-data)について確認する。



