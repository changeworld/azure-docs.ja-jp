---
title: Azure Data Lake Storage Gen2 の URI を使用する
description: abfs スキーム識別子の URI 構文について説明します。これは、Azure Blob ファイル システム ドライバー (Azure Data Lake Storage Gen2 用 Hadoop ファイル システム ドライバー) を表します。
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: af31e49228f655d5b16cc20fdc07cd9a7da597d6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620219"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Azure Data Lake Storage Gen2 の URI を使用する

Azure Data Lake Storage Gen2 と互換性のある [Hadoop ファイル システム](https://www.aosabook.org/en/hdfs.html) ドライバーは、そのスキーム識別子 `abfs` (Azure BLOB ファイル システム) によって認識されます。 他の Hadoop ファイル システム ドライバーと整合性のある ABFS ドライバーでは、Data Lake Storage Gen2 対応アカウント内のファイルとディレクトリをアドレス指定するために URI 形式が使用されています。

## <a name="uri-syntax"></a>URI 構文

Data Lake Storage Gen2 の URI 構文は、Data Lake Storage Gen2 を既定のファイル システムとして使用するようにストレージ アカウントが設定されているかどうかによって異なります。

アカウント作成時、アドレス指定する Data Lake Storage Gen2 対応アカウントが既定のファイル システムとして **設定されていない** 場合、簡潔な URI 構文は次のようになります。

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **スキーム識別子**:`abfs` プロトコルがスキーム識別子として使用されます。 末尾に「s」を追加すると (つまり、abfs<b><i>s</i></b>)、ABFS Hadoop クライアント ドライバーは、選択された認証方法に関係なく、"<i>常に</i>" トランスポート層セキュリティ (TLS) を使用します。 認証に OAuth を選択すると、OAuth は TLS 層にのみ依存するため、「abfss」ではなく「abfs」を指定した場合でも、クライアント ドライバーは常に TLS を使用します。 最後に、以前の方法であるストレージ アカウント キーの使用を選択すると、クライアント ドライバーは、「abfs」が TLS を使用しないことを意味すると解釈します。

2. **ファイル システム**:ファイルとフォルダーを保持する親ロケーションです。 これは、Azure Storage Blob サービスのコンテナーと同じです。

3. **アカウント名**:作成時にストレージ アカウントに付与された名前です。

4. **パス**:スラッシュ (`/`) で区切って表現したディレクトリ構造です。

5. **ファイル名**:個々のファイルの名前です。 このパラメーターは、ディレクトリをアドレス指定する場合は省略可能です。

ただし、アカウント作成時、アドレス指定するアカウントが既定のファイル システムとして設定された場合、簡潔な URI 構文は次のようになります。

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **パス**:スラッシュ (`/`) で区切って表現したディレクトリ構造です。

2. **ファイル名**:個々のファイルの名前です。

## <a name="next-steps"></a>次のステップ

- [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
