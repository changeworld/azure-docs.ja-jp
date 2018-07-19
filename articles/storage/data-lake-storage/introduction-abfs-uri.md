---
title: Azure Data Lake Storage Gen2 プレビューの URI を使用する
description: Azure Data Lake Storage Gen2 プレビューの URI を使用する
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
manager: jahogg
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: a6130d8440b16e5a72c939fc07f6bf32c0946418
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114294"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Azure Data Lake Storage Gen2 の URI を使用する

Azure Data Lake Storage Gen2 プレビューと互換性のある [Hadoop ファイル システム](http://www.aosabook.org/en/hdfs.html) ドライバーは、そのスキーム識別子 `abfs` (Azure Blob ファイル システム) によって認識されます。 他の Hadoop ファイル システム ドライバーと整合性のある ABFS ドライバーでは、Data Lake Storage Gen2 対応アカウント内のファイルとディレクトリをアドレス指定するために URI 形式が使用されています。

## <a name="uri-syntax"></a>URI 構文

Data Lake Storage Gen2 の URI 構文は、Data Lake Storage Gen2 を既定のファイル システムとして使用するようにストレージ アカウントが設定されているかどうかによって異なります。

アカウント作成時、アドレス指定する Data Lake Storage Gen2 対応アカウントが既定のファイル システムとして**設定されていない**場合、簡潔な URI 構文は次のようになります。

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.widows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **スキーム識別子**: `abfs` プロトコルがスキーム識別子として使用されます。 接続時に Secure Socket Layer (SSL) を使用するかどうかを選択できます。 Secure Socket Layer を介して接続する場合は、`abfss` を使用します。

2. **ファイル システム**: ファイルとフォルダーを保持する親ロケーションです。 これは、Azure Storage Blob サービスのコンテナーと同じです。

3. **アカウント名**: 作成時にストレージ アカウントに付与された名前です。

4. **パス**: ディレクトリ構造をスラッシュ (`/`) で区切って表現したものです。

5. **ファイル名**: 個々のファイルの名前です。 このパラメーターは、ディレクトリをアドレス指定する場合は省略可能です。

ただし、アカウント作成時、アドレス指定するアカウントが既定のファイル システムとして設定された場合、簡潔な URI 構文は次のようになります。

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **パス**: ディレクトリ構造をスラッシュ (`/`) で区切って表現したものです。

2. **ファイル名**: 個々のファイルの名前です。


## <a name="next-steps"></a>次の手順

- [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](use-hdi-cluster.md)