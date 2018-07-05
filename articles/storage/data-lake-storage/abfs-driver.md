---
title: Azure Data Lake Storage Gen2 プレビュー用の Azure BLOB ファイルシステム ドライバー
description: ABFS Hadoop ファイルシステム ドライバー
services: storage
keywords: ''
author: jamesbak
manager: jahogg
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e92c4efba29f1c40f6d4cb155974ca3a896796e5
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114335"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Azure BLOB ファイルシステム ドライバー (ABFS): Hadoop 専用の Azure Storage ドライバー

Azure Data Lake Storage Gen2 プレビュー内のデータの主要なアクセス方法の 1 つは、[Hadoop FileSystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html) です。 Azure Data Lake Storage Gen2 には、対応するドライバーである Azure BLOB ファイル システム ドライバー (`ABFS`) があります。 ABFS は、Apache Hadoop の一部であり、Hadoop の商用ディストリビューションの多くに含まれています。 このドライバーを使用すると、多くのアプリケーションとフレームワークは、Data Lake Storage Gen2 サービスを明示的に参照するコードがなくても Data Lake Storage Gen2 のデータにアクセスできます。

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>以前の機能: Windows Azure Storage Blob ドライバー

Windows Azure Storage Blob ドライバーまたは [WASB ドライバー](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)は、Azure Storage Blob のオリジナル サポートを提供しました。 このドライバーは、(Hadoop FileSystem インターフェイスでの必要に応じて) Azure Blob Storage によって公開されているオブジェクト ストア スタイルのインターフェイスのセマンティクスへのファイル システム セマンティクスのマッピングの複雑なタスクを実行しました。 このドライバーは引き続きこのモデルをサポートし、BLOB に格納されたデータへの高パフォーマンス アクセスを実現しますが、このマッピングを実行するコードが大量に含まれているため、メンテナンスが困難になります。 さらに、[FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) や [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) などの一部の操作がディレクトリに適用される場合、(オブジェクト ストアにディレクトリのサポートがないため) ドライバーが膨大な数の操作を実行する必要があり、多くの場合にパフォーマンスの低下につながります。

したがって、WASB の本質的な設計の欠陥を克服するために、新しい Azure Data Lake Storage サービスが新しい ABFS ドライバーからのサポートと共に実装されました。

## <a name="the-azure-blob-file-system-driver"></a>Azure BLOB ファイル システム ドライバー

[Azure Data Lake Storage REST インターフェイス](https://docs.microsoft.com/en-us/rest/api/storageservices/data-lake-storage-gen2)は Azure Blob Storage 上のファイル システムのセマンティクスをサポートするように設計されています。 Hadoop FileSystem も同じセマンティクスをサポートするように設計されている場合、ドライバー内での複雑なマッピングは必要ありません。 したがって、Azure BLOB ファイル システム ドライバー (または ABFS) は、REST API の単なるクライアント shim です。

ただし、ドライバーが実行する必要のある機能がいくつかあります。

### <a name="uri-scheme-to-reference-data"></a>参照データへの URI スキーム

Hadoop 内の他のファイルシステムの実装と一貫性のある ABFS ドライバーは、リソース (ディレクトリとファイル) を明確にアドレス指定できるように独自の URI スキームを定義します。 URI スキームは、「[Use the Azure Data Lake Storage Gen2 URI](./introduction-abfs-uri.md)」(Azure Data Lake Storage Gen2 URI の使用) に記載されています。 URI の構造は次のとおりです: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

上記の URI 形式を使用して、標準の Hadoop ツールとフレームワークを使用してこれらのリソースを参照できます。

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

内部的には、ABFS ドライバーは、URI で指定されたリソースをファイルとディレクトリに変換し、これらの参照で Azure Data Lake Storage REST API を呼び出します。

### <a name="authentication"></a>認証

Hadoop アプリケーションが Data Lake Storage Gen2 内に含まれるリソースに安全にアクセスできるように、ABFS ドライバーは現在共有キー認証をサポートしています。 キーが暗号化され、Hadoop 構成に格納されます。

### <a name="configuration"></a>構成

ABFS ドライバーのすべての構成は <code>core-site.xml</code> 構成ファイルに格納されます。 [Ambari](http://ambari.apache.org/) を備えた Hadoop ディストリビューションでは、構成は Web ポータルまたは Ambari REST API を使用して管理することもできます。

サポートされるすべての構成エントリの詳細は、[公式 Hadoop ドキュメント](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)で指定されています。

### <a name="hadoop-documentation"></a>Hadoop ドキュメント

ABFS ドライバーについては、[公式 Hadoop ドキュメント](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)に完全に記載されています

## <a name="next-steps"></a>次の手順

- [HDInsight クラスターの設定](./quickstart-create-connect-hdi-cluster.md)
- [Azure Databricks クラスターの作成](./quickstart-create-databricks-account.md)
- [Azure Data Lake Storage Gen2 URI の使用](./introduction-abfs-uri.md)
