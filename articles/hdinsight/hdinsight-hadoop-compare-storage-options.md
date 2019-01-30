---
title: Azure HDInsight クラスターで使用するストレージ オプションを比較する
description: ストレージの種類と、それらが Azure HDInsight でどのように動作するかの概要を提供します。
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/20/2019
ms.openlocfilehash: 14db76068cc11d3f57a72e3e540a5e0da7e1c254
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853614"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで使用するストレージ オプションを比較する

Azure HDInsight ユーザーは、HDInsight クラスターの作成時に、次のいくつかの異なるストレージ オプションを選択できます。

* Azure Data Lake Storage Gen2
* Azure Storage
* Azure Data Lake Storage Gen1

この記事では、これらの異なるストレージの種類とそれらの固有の機能の概要を提供します。

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Azure HDInsight で Azure Data Lake Storage Gen2 と Apache Hadoop を使用する

Azure Data Lake Storage Gen2 の詳細については、「[Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)」を参照してください。

Azure Data Lake Storage Gen2 は、Hadoop 互換ファイル システム、Azure Active Directory、POSIX ベースのアクセス制御リスト (ACL) などのコア機能を Azure Data Lake Storage Gen1 から引き継ぎ、それらを Azure Blob Storage に統合します。 この組み合わせにより、Azure Data Lake Storage Gen1 のパフォーマンスを利用しつつ、Blob Storage の階層化とデータのライフ サイクル管理も使用することができます。

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 のコア機能

* Hadoop と互換性のあるアクセス:Azure Data Lake Storage Gen2 では、Hadoop 分散ファイル システム (HDFS) を利用する場合と同様に、データの管理およびアクセスを可能にします。 Azure BLOB ファイル システム (ABFS) ドライバーは、Data Lake Storage Gen2 に格納されたデータにアクセスするための Azure HDInsight や Azure Databricks を含むすべての Apache Hadoop 環境内で使用できます。

* POSIX アクセス許可のスーパー セット:Data Lake Gen2 のセキュリティ モデルは、ACL および POSIX のアクセス許可に加え、Data Lake Storage Gen2 固有の追加設定をサポートしています。 設定は、管理ツールまたはフレームワーク (Apache Hive や Apache Spark など) を通じて構成できます。

* コスト効率:Data Lake Storage Gen2 は、低コストのストレージ容量とトランザクションを備えています。 Azure Blob Storage のライフサイクルなどの機能は、データのライフサイクルの進展に応じて請求金額を調整することで、コストの削減に役立ちます。

* Blob ストレージ ツール、フレームワーク、およびアプリの操作:Data Lake Storage Gen2 では、Blob ストレージ用に今日存在するさまざまなツール、フレームワーク、およびアプリケーションを継続的に利用できます。

* 最適化されたドライバー:ABFS ドライバーは、ビッグ データ分析のために特別に最適化されています。 該当する REST API は、dfs エンドポイント、dfs.core.windows.net を介して表示されます。

### <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2 の新機能

#### <a name="managed-identities-for-secure-file-access"></a>セキュア ファイル アクセスのためのマネージド ID

Azure HDInsight は、マネージド ID を使用して、Azure Data Lake Storage Gen2 内のファイルへのクラスター アクセスをセキュリティで保護します。 マネージド ID は、Azure サービスに自動的に管理される一連の資格情報を提供する Azure Active Directory の機能です。 これらの資格情報は、AD 認証をサポートする任意のサービスを認証するために使用できます。 マネージド ID の使用には、コードまたは構成ファイルに資格情報を格納する必要はありません。

詳しくは、[Azure リソースのマネージド ID ](../active-directory/managed-identities-azure-resources/overview.md)を参照してください。

#### <a name="azure-blob-filesystem-abfs-driver"></a>Azure BLOB ファイルシステム (ABFS) ドライバー

Apache Hadoop アプリケーションは、ローカル ディスク ストレージからネイティブにデータを読み書きすることを想定しています。 ABFS などの Hadoop ファイル システム ドライバーは、通常の Hadoop ファイル システム操作をエミュレートすることによって、Hadoop アプリケーションがクラウド ストレージで動作できるようにします。 このドライバーは、アプリケーションから受信されたコマンドを実際のクラウド ストレージ プラットフォームで認識される操作に変換します。

以前は、Hadoop ファイルシステム ドライバーは、すべてのファイルシステム操作をクライアント側での Azure Storage REST API 呼び出しに変換してから、REST API を呼び出していました。 しかし、このクライアント側の変換では、ファイルの名前変更のような 1 つのファイルシステム操作に対して複数の REST API が呼び出されていました。 ABFS では、Hadoop ファイル システム ロジックの一部がクライアント側からサーバー側に移動されたため、Azure Data Lake Storage Gen2 API が Blob API と並列に実行されるようになりました。 この移行により、Hadoop ファイルシステムの一般的な操作が 1 つの REST API 呼び出しで実行できるようになったため、パフォーマンスが向上します。

詳細については、「[The Azure Blob Filesystem driver (ABFS):A dedicated Azure Storage driver for Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md)」 (Azure BLOB ファイルシステム ドライバー (ABFS): Hadoop 専用の Azure Storage ドライバー) を参照してください。

#### <a name="azure-data-lake-storage-gen-2-uri-scheme"></a>Azure Data Lake Storage Gen2 の URI スキーム

Azure Data Lake Storage Gen2 は、HDInsight から Azure ストレージ内のファイルにアクセスするための新しい URI スキームを使用します。

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

この URI スキームは、SSL で暗号化されたアクセス (`abfss://` プレフィックス) と暗号化なしのアクセス (`abfs://` プレフィックス) に対応しています。 同じ Azure リージョン内のデータにアクセスする場合でも、できる限り `abfss` を使用することをお勧めします。

`<FILE_SYSTEM_NAME>` には、Data Lake Storage Gen2 ファイル システムのパスを指定します。

`<ACCOUNT_NAME>` には、Azure Storage アカウント名を指定します。 完全修飾ドメイン名 (FQDN) を指定する必要があります。

`<PATH>` は、ファイルまたはディレクトリの HDFS パス名です。

`<FILE_SYSTEM_NAME>` と `<ACCOUNT_NAME>` の値が指定されていない場合、既定のファイルシステムが使用されます。 既定のファイル システム上にあるファイルに関しては、相対パスか絶対パスを使用できます。 たとえば、HDInsight クラスターに付属している `hadoop-mapreduce-examples.jar` ファイルは、次のいずれかのパスを使用して参照できます。

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> HDInsight バージョン 2.1 クラスターと 1.6 クラスターでは、ファイル名は `hadoop-examples.jar` です。 HDInsight の外部からファイルを操作する場合、ほとんどのユーティリティで ABFS 形式が認識されず、代わりに `example/jars/hadoop-mapreduce-examples.jar` などの基本的なパス形式が要求されます。

詳細については、「[Use the Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)」 (Azure Data Lake Storage Gen2 の URI を使用する) を参照してください。

## <a name="use-azure-storage"></a>Azure ストレージの使用

Azure Storage は、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。 HDInsight は、クラスターの既定のファイル システムとして Azure Storage 内の BLOB コンテナーを使用できます。 HDInsight のすべてのコンポーネントは、Hadoop 分散ファイル システム (HDFS) のインターフェイスを利用して、BLOB として格納された構造化データまたは非構造化データを直接操作できます。

> [!WARNING]  
> Azure ストレージ アカウントを作成する場合、いくつかの選択肢があります。 以下の表は、HDInsight に関して利用できる選択肢についての情報です。

| ストレージ アカウントの種類 | サポートされているサービス | サポートされているパフォーマンス レベル | サポートされているアクセス層 |
|----------------------|--------------------|-----------------------------|------------------------|
| 汎用 v2   | BLOB               | 標準                    | ホット、クール、アーカイブ *    |
| 汎用 v1   | BLOB               | 標準                    | 該当なし                    |
| BLOB ストレージ         | BLOB               | 標準                    | ホット、クール、アーカイブ *    |

ビジネス データを格納するために既定の BLOB コンテナーを使用することはお勧めできません。 ストレージ コストを削減するために、既定の BLOB コンテナーの使用後、コンテナーを毎回削除することをお勧めします。 既定のコンテナーには、アプリケーション ログとシステム ログが格納されます。 コンテナーを削除する前に、ログを取り出してください。

1 つの BLOB コンテナーを複数のクラスターの既定のファイル システムとして使用することはサポートされていません。
 
 > [!NOTE]  
 > アーカイブ アクセス層はオフライン層であり、取得の際に数時間の待ち時間があるので、HDInsight での使用は推奨されません。 詳しくは、「[アーカイブ アクセス層](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)」をご覧ください。

### <a name="hdinsight-storage-architecture"></a>HDInsight のストレージ アーキテクチャ
次の図は、Azure Storage を使う HDInsight のストレージ アーキテクチャを示しています。

![Hadoop クラスターは、HDFS API を使用して、BLOB ストレージの構造化データおよび非構造化データへのアクセスと保存を実行します。](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "HDInsight のストレージ アーキテクチャ")

HDInsight では、それぞれのコンピューティング ノードにローカルに割り当てられている分散ファイル システムにアクセスします。 このファイル システムには、完全修飾 URI を使用してアクセスできます。次に例を示します。

    hdfs://<namenodehost>/<path>

さらに、HDInsight では、Azure Storage に格納されたデータにアクセスすることもできます。 の構文は次のとおりです。

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

HDInsight クラスターで Azure ストレージ アカウントを使用するときの考慮事項を次に示します。

* **クラスターに接続されているストレージ アカウント内のコンテナー:** アカウントの名前とキーは作成中にクラスターと関連付けられるため、これらのコンテナー内の BLOB に対するフル アクセス許可が与えられます。

* **クラスターに接続されていないストレージ アカウント内のパブリック コンテナーまたはパブリック BLOB:** コンテナー内の BLOB に対する読み取り専用のアクセス許可が与えられます。
  
  > [!NOTE]  
  > パブリック コンテナーの場合、そのコンテナー内に配置されているすべての BLOB のリストとコンテナー メタデータを取得できます。 パブリック BLOB の場合、正確な URL がわかっているときのみ、その BLOB にアクセスできます。 詳細については、[コンテナーと BLOB に対するアクセスの管理](../storage/blobs/storage-manage-access-to-resources.md)に関するページを参照してください。

* **クラスターに接続されていないストレージ アカウント内のプライベート コンテナー:** WebHCat ジョブを送信するときにストレージ アカウントを定義しない限り、コンテナー内の BLOB にはアクセスできません。 詳しくは、この記事の後半で説明します。

作成プロセスで定義されたストレージ アカウントとそのキーは、クラスター ノードの %HADOOP_HOME%/conf/core-site.xml に格納されます。 HDInsight の既定の動作では、core-site.xml ファイルに定義されたストレージ アカウントが使用されます。 この設定は、[Apache Ambari](./hdinsight-hadoop-manage-ambari.md) を使用して変更できます。

Apache Hive、MapReduce、Apache Hadoop ストリーミング、Apache Pig など、複数の WebHCat ジョブを利用して、ストレージ アカウントの説明とそのメタデータを伝達できます。 (現在、ストレージ アカウントについては Pig が対応していますが、メタデータについては対応していません)。詳細については、「 [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores (代替のストレージ アカウントおよびメタストアでの HDInsight クラスターの使用)](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)」を参照してください。

BLOB は、構造化データと非構造化データに使用できます。 BLOB コンテナーには、"キーと値のペア" としてデータが格納されます。ディレクトリ階層はありません。 ただし、キー名でスラッシュ (/) を使用すれば、ファイルがディレクトリ階層に保存されているように見せかけることができます。 たとえば、BLOB のキーが `input/log1.txt` であるとします。 実際の `input` ディレクトリは存在しませんが、キー名にスラッシュ文字が存在するため、ファイル パスの外観をしています。

### <a id="benefits"></a>Azure Storage の利点
コンピューティング クラスターとストレージ リソースを同じ場所で併置しないとなるとパフォーマンスの低下が懸念されますが、これは、Azure リージョン内のストレージ アカウント リソースの近くにコンピューティング クラスターを作成することで軽減されます。高速ネットワークが整備されているので、コンピューティング ノードは Azure Storage 内のデータに効率的にアクセスできます。

HDFS ではなく、Azure Storage にデータを格納することにはいくつかの利点があります。

* **データの再使用と共有:** HDFS のデータはコンピューティング クラスター内に配置されます。 HDFS API を使用してデータを操作できるのは、コンピューティング クラスターへのアクセスが許可されているアプリケーションだけです。 Azure ストレージ内のデータには、HDFS API または Blob Storage REST API のどちらかを使用してアクセスできます。 したがって、さまざまなアプリケーション (その他の HDInsight クラスターを含む) やツールを使用してデータの生成と利用ができます。
* **データのアーカイブ:** Azure Storage にデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。
* **データ ストレージ コスト:** コンピューティング クラスターは Azure Storage よりコストがかかるため、DFS に長期間データを格納すると、Azure Storage にデータを格納した場合よりコストが高くなります。 さらに、コンピューティング クラスターを生成するたびにデータを再読み込みする必要がないため、データの読み込みコストも節約されます。
* **柔軟なスケールアウト:** HDFS は大規模なファイル システムを提供しますが、規模を拡張するにはクラスターに対して作成するノードの数を増やさなければならないので、作業が複雑になります。 一方、Azure Storage には柔軟なスケール機能がもともと備わっています。
* **geo レプリケーション:** Azure Storage は、別の拠点に geo レプリケートできます。 災害発生時には別の拠点でデータを回復でき、データの冗長性が高まりますが、geo レプリケートした別拠点へのフェールオーバーはパフォーマンスに大きな影響を与え、追加コストが発生する可能性もあります。 geo レプリケーションを利用するときは、追加コストがかかっても保護する価値のあるデータかどうかを十分に考慮してください。

MapReduce の一部のジョブやパッケージでは中間結果が生成されますが、Azure Storage には保存したくない場合もあります。 このような場合、中間結果データをローカルの HDFS に保存できます。 実際、HDInsight では、Hive ジョブやその他のプロセスで生成される中間結果の一部が DFS に格納されます。

> [!NOTE]  
> ほとんどの HDFS コマンド (`ls`、`copyFromLocal`、`mkdir` など) は通常と同じように機能します。 ただし、`fschk` や `dfsadmin` など、HDFS ネイティブ実装 (DFS) に固有のコマンドについては、Azure Storage 上で実行した場合に動作が異なります。

## <a name="use-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 を使用する

Azure Data Lake Storage Gen1 の詳細については、「[Azure Data Lake Storage Gen1 の概要](../data-lake-store/data-lake-store-overview.md)」を参照してください。

Azure Data Lake Storage Gen1 は、ビッグ データの分析ワークロードに対応するエンタープライズ レベルのハイパースケール リポジトリです。 Azure Data Lake を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、および取り込み速度のデータを 1 か所でキャプチャすることができます。

Data Lake Storage Gen1 には、WebHDFS と互換性のある REST API を使用して Hadoop (HDInsight クラスターで使用可能) からアクセスできます。 これは、格納されたデータに対する分析を可能にするように設計されており、データ分析シナリオ用にパフォーマンスがチューニングされます。 これには、企業における実際のユース ケースで不可欠なエンタープライズ レベルのすべての機能 (セキュリティ、管理の容易性、スケーラビリティ、信頼性、および可用性) が既定で組み込まれています。

![Azure Data Lake Storage](./media/hdinsight-hadoop-compare-storage-options/data-lake-store-concept.png "HDInsight のストレージ アーキテクチャ")

Data Lake Storage Gen1 の主要な機能のいくつかを以下に示します。

### <a name="built-for-hadoop"></a>Hadoop 用に構築

Data Lake Storage Gen1 は、Hadoop 分散ファイル システム (HDFS) と互換性のある Apache Hadoop ファイル システムであり、Hadoop エコシステムと連動します。  WebHDFS API を使用する既存の HDInsight アプリケーションまたはサービスは、Data Lake Storage Gen1 と簡単に統合することができます。 Data Lake Storage Gen1 では、アプリケーション向けの WebHDFS と互換性のある REST インターフェイスも公開されています。

Data Lake Storage Gen1 に格納されたデータは、MapReduce または Hive などの Hadoop 分析フレームワークを使用して簡単に分析することができます。 Data Lake Storage Gen1 に格納されたデータに直接アクセスするように Microsoft Azure HDInsight クラスターをプロビジョニングおよび構成することができます。

### <a name="unlimited-storage-petabyte-files"></a>無制限のストレージ、ペタバイト ファイル

Data Lake Storage Gen1 では無制限のストレージが提供されます。そのため、分析を目的としてさまざまなデータを格納するのに適しています。 Data Lake に格納できるアカウント サイズ、ファイル サイズ、またはデータ量に関する制限は設定されていません。 対応可能な個々のファイルのサイズはキロバイトからペタバイトの範囲にわたり、任意の種類のデータを自由に格納することができます。 データは複数のコピーを作成して格納されるため障害が発生しても保護されます。Data Lake でのデータの格納期間に制限はありません。

### <a name="performance-tuned-for-big-data-analytics"></a>ビッグ データを分析するためのパフォーマンス チューニング

Data Lake Storage Gen1 は、大量のデータのクエリと分析のために非常に高いスループットを必要とする、大規模な分析システムを実行するために構築されています。 Data Lake では、ファイル内のデータを複数の異なる記憶域サーバーに分散します。 これにより、ファイルを並列に読み取ってデータ分析を実行する場合の読み取りスループットが向上します。

### <a name="enterprise-ready-highly-available-and-secure"></a>エンタープライズ対応: 高可用性およびセキュリティ

Data Lake Storage Gen1 では、業界標準の可用性と信頼性が提供されます。 データ資産は、冗長なコピーを作成して格納されるので、予期せぬ障害が発生しても保護されます。 企業では、実際のソリューションで既存のデータ プラットフォームの重要な部分として Data Lake Storage Gen1 を使用できます。

Data Lake Storage Gen1 では、格納されたデータに対してエンタープライズ レベルのセキュリティも提供されます。 詳細については、[Azure Data Lake Storage Gen1 内のデータのセキュリティ保護](#DataLakeStoreSecurity)に関するページを参照してください。

### <a name="all-data"></a>すべてのデータ

Azure Data Lake Storage Gen1 では、任意のデータをネイティブ形式でそのまま格納することができ、事前の変換は必要ありません。 Azure Data Lake Storage Gen1 では、データが読み込まれる前にスキーマを定義する必要はなく、分析時にデータを解釈してスキーマを定義するかどうかは個々の分析フレームワークに任されます。 任意のサイズおよび形式のファイルを格納できるようにすると、Data Lake Storage Gen1 で構造化データ、半構造化データ、および非構造化データを処理できるようになります。

Data Lake Storage Gen1 のデータ コンテナーは、本質的にはフォルダーとファイルです。 格納されたデータを SDK、Azure Portal、および Azure Powershell を使用して操作します。 これらのインターフェイスと適切なコンテナーを使用してストアにデータを配置する限り、あらゆる種類のデータを格納できます。 Data Lake Storage Gen1 では、格納されているデータの種類に基づくデータの特別な処理は行われません。

## <a name="DataLakeStoreSecurity">Data Lake Storage Gen1 でのデータのセキュリティ保護</a>
Data Lake Storage Gen1 では、Azure Active Directory を使用し、認証およびアクセス制御リスト (ACL) によってデータへのアクセスを管理します。

| 機能 | 説明 |
| --- | --- |
| Authentication |Data Lake Storage Gen1 では、Azure Active Directory (AAD) と統合することで、Data Lake Storage Gen1 に格納されたすべてのデータの ID およびアクセスの管理を行います。 この統合により、Data Lake Storage Gen1 では、AAD のすべての機能 (多要素認証、条件付きアクセス、ロール ベースのアクセス制御、アプリケーション使用状況の監視、セキュリティの監視とアラート通知など) の利点が得られます。Data Lake Storage Gen1 では、REST インターフェイスでの認証に対応する OAuth 2.0 プロトコルがサポートされます。 [Data Lake Storage Gen1 の認証](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)に関するページを参照してください。|
| アクセス制御 |Data Lake Storage Gen1 では、WebHDFS プロトコルで公開された POSIX 形式のアクセス許可をサポートすることにより、アクセス制御が提供されます。 ルート フォルダー、サブフォルダー、個々のファイルで ACL を有効にすることができます。 Data Lake Storage Gen1 のコンテキストにおける ACL のしくみの詳細については、[Data Lake Storage Gen1 でのアクセス制御](../data-lake-store/data-lake-store-access-control.md)に関するページを参照してください。 |
| 暗号化 |Data Lake Storage Gen1 では、アカウントに格納されているデータを暗号化することもできます。 暗号化設定は、Data Lake Storage Gen1 アカウントの作成時に指定します。 データを暗号化するかどうかを選択できます。 詳細については、[Data Lake Storage Gen1 での暗号化](../data-lake-store/data-lake-store-encryption.md)に関するページを参照してください。 暗号化関連の構成を提供する方法については、「[Azure Portal で Azure Data Lake Storage Gen1 の使用を開始する](../data-lake-store/data-lake-store-get-started-portal.md)」を参照してください。 |

Data Lake Storage Gen1 に格納されているデータのセキュリティ保護について詳しくお知りになりたいですか?  以下のページを参照してください。

* Data Lake Storage Gen1 内のデータをセキュリティで保護する方法については、[Azure Data Lake Storage Gen1 内のデータのセキュリティ保護](../data-lake-store/data-lake-store-secure-data.md)に関するページを参照してください。

## <a name="applications-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1 と互換性のあるアプリケーション
Data Lake Storage Gen1 は、Hadoop エコシステム内のほとんどのオープンソース コンポーネントと互換性があります。 他の Azure サービスとの連携性にも優れています。  Data Lake Storage Gen1 をオープンソース コンポーネントとその他の Azure サービスの両方と共に使用する方法の詳細については、下のリンクに従ってください。

* Data Lake Storage Gen1 と相互運用可能なオープンソース アプリケーションの一覧については、[Azure Data Lake Storage Gen1 と互換性のあるアプリケーションおよびサービス](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)に関するページを参照してください。
* Data Lake Storage Gen1 を他の Azure サービスと共に使用して広範なシナリオを有効にする方法については、[他の Azure サービスとの統合](../data-lake-store/data-lake-store-integrate-with-other-services.md)に関するページを参照してください。
* データの取り込み、処理、ダウンロード、視覚化などのシナリオにおける Data Lake Storage Gen1 の使い方については、[Data Lake Storage Gen1 の使用に関するシナリオ](../data-lake-store/data-lake-store-data-scenarios.md)についてのページを参照してください。

## <a name="what-is-data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 ファイル システム (adl://) とは
Hadoop 環境 (HDInsight クラスターで使用可能) では、新しいファイル システムである AzureDataLakeFilesystem (adl://) 経由で Data Lake Storage Gen1 にアクセスできます。 adl:// を使用するアプリケーションとサービスでは、WebHDFS で現在まだサポートされていない、より有効なパフォーマンスの最適化を利用できるようになります。 結果として、Data Lake Storage Gen1 では、adl:// を使用する推奨オプションで最適なパフォーマンスを利用することも、引き続き WebHDFS API を直接使用することにより既存のコードを維持することもできるという柔軟性が得られます。 Azure HDInsight は、AzureDataLakeFilesystem をフルに活用して Data Lake Storage Gen1 で最適なパフォーマンスを実現します。

Data Lake Storage Gen1 内のデータには、`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` を使用してアクセスすることができます。 Data Lake Storage Gen1 内のデータへのアクセス方法の詳細については、[格納データのプロパティの表示](../data-lake-store/data-lake-store-get-started-portal.md#properties)に関する記述を参照してください。



## <a name="next-steps"></a>次の手順

* [Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)。
* [Azure ストレージの概要](../storage/common/storage-introduction.md)