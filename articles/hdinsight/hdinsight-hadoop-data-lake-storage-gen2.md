---
title: Azure HDInsight で Azure Data Lake Storage (ADLS) Gen2 と Apache Hadoop を使用する
description: Azure Data Lake Storage Gen2 の概要を提供し、Azure HDInsight でどのように機能するかについて説明します。
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 2ae11afe1ecbe500a4851aab6d56e612fbe79ee6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976752"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Azure HDInsight で Azure Data Lake Storage Gen2 と Apache Hadoop を使用する

Azure Data Lake Storage (ADLS) Gen2 は、Hadoop 互換ファイル システム、Azure Active Directory、POSIX ベースの ACL などのコア機能を Azure Data Lake Storage Gen1 から引き継ぎ、それらを Azure Blob Storage に統合します。 この組み合わせにより、Azure Data Lake Storage Gen1 のパフォーマンスを利用しつつ、Blob Storage の階層化とデータのライフ サイクル管理も使用することができます。

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 のコア機能

- Hadoop と互換性のあるアクセス:Azure Data Lake Storage Gen2 では、Hadoop 分散ファイル システム (HDFS) を利用する場合と同様に、データの管理およびアクセスを可能にします。 ABFS ドライバーは、Data Lake Storage Gen2 に格納されたデータにアクセスするための Azure HDInsight および Azure Databricks など、すべての Apache Hadoop 環境内で利用できます。

- POSIX アクセス許可のスーパー セット:Data Lake Gen2 のセキュリティ モデルは、ACL および POSIX のアクセス許可に加え、Data Lake Storage Gen2 固有の追加設定をサポートしています。 設定は、管理ツールまたはフレームワーク (Apache Hive や Apache Spark など) を通じて構成できます。

- コスト効率:Data Lake Storage Gen2 は、低コストのストレージ容量とトランザクションを備えています。 Azure Blob Storage のライフサイクルなどの機能は、データのライフサイクルの進展に応じて請求金額を調整することで、コストの削減に役立ちます。

- Blob ストレージ ツール、フレームワーク、およびアプリの操作:Data Lake Storage Gen2 では、Blob ストレージ用に今日存在するさまざまなツール、フレームワーク、およびアプリケーションを継続的に利用できます。

- 最適化されたドライバー:ABFS ドライバーは、ビッグ データ分析のために特別に最適化されています。 該当する REST API は、dfs エンドポイント、dfs.core.windows.net を介して表示されます。

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2 の新機能

### <a name="managed-identities-for-secure-file-access"></a>セキュア ファイル アクセスのためのマネージド ID

Azure HDInsight は、マネージド ID を使用して、Azure Data Lake Storage Gen2 内のファイルへのクラスター アクセスをセキュリティで保護します。 マネージド ID は、Azure サービスに自動的に管理される一連の資格情報を提供する Azure Active Directory の機能です。 これらの資格情報は、AD 認証をサポートする任意のサービスを認証するために使用できます。 マネージド ID の使用には、コードまたは構成ファイルに資格情報を格納する必要はありません。

詳しくは、[Azure リソースのマネージド ID ](../active-directory/managed-identities-azure-resources/overview.md)を参照してください。

### <a name="azure-blob-filesystem-abfs-driver"></a>Azure BLOB ファイルシステム (ABFS) ドライバー

Apache Hadoop アプリケーションは、ローカル ディスク ストレージからネイティブにデータを読み書きすることを想定しています。 ABFS などの Hadoop ファイルシステム ドライバーは、通常の Hadoop ファイル システム操作をアプリケーション用にエミュレートすることにより、Hadoop アプリケーションでクラウド ストレージを使用できるようにします。 次に、ドライバーは、これらの操作を実際のクラウド ストレージ プラットフォームで認識される操作に変換します。

以前は、Hadoop ファイルシステム ドライバーは、すべてのファイルシステム操作をクライアント側での Azure Storage REST API 呼び出しに変換してから、REST API を呼び出していました。 しかし、このクライアント側の変換では、ファイルの名前変更のような 1 つのファイルシステム操作に対して複数の REST API が呼び出されていました。 ABFS では、一部の Hadoop ファイルシステム ロジックをクライアント側からサーバー側に移動し、ADLS Gen2 API が Blob API と並列で実行されるようになりました。 この移行により、Hadoop ファイルシステムの一般的な操作が 1 つの REST API 呼び出しで実行できるようになったため、パフォーマンスが向上します。

詳細については、「[The Azure Blob Filesystem driver (ABFS):A dedicated Azure Storage driver for Hadoop](../storage/data-lake-storage/abfs-driver.md)」 (Azure BLOB ファイルシステム ドライバー (ABFS): Hadoop 専用の Azure Storage ドライバー) を参照してください。

### <a name="adls-gen-2-uri-scheme"></a>ADLS Gen 2 の URI スキーム

ADLS Gen2 では、HDInsight から Azure Storage 内のファイルにアクセスするため、次の新しい URI スキームが使用されています。

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

詳細については、「[Use the Azure Data Lake Storage Gen2 URI](../storage/data-lake-storage/introduction-abfs-uri.md)」 (Azure Data Lake Storage Gen2 の URI を使用する) を参照してください。

## <a name="next-steps"></a>次の手順
- [Azure Data Lake Storage Gen2 の概要](../storage/data-lake-storage/introduction.md)
- [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 プレビューを使用する](../storage/data-lake-storage/use-hdi-cluster.md)