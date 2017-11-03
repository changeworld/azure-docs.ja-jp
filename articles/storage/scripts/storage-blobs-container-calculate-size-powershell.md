---
title: "Azure PowerShell のサンプル スクリプト - BLOB コンテナーのサイズを計算する | Microsoft Docs"
description: "コンテナー内の BLOB のサイズを合計して、Azure Blob ストレージ内のコンテナーのサイズを計算します。"
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Blob ストレージ コンテナーのサイズを計算する

このスクリプトでは、コンテナー内のブロブのサイズを合計して、Azure Blob ストレージ内のコンテナーのサイズを計算します。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>Blob ストレージ コンテナーのサイズを理解する

Blob ストレージ コンテナーの合計サイズには、コンテナー自体のサイズとコンテナーの下のすべての BLOB のサイズが含まれます。

ここでは、Blob コンテナーと BLOB のストレージ容量を計算する方法について説明します。 以下では Len(X) は文字列の文字数を表します。

### <a name="blob-containers"></a>Blob コンテナー

次のようにして Blob コンテナーごとに使用されるストレージの量を推定します。

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

内訳は次のようになります。
* コンテナーごとの 48 バイトのオーバーヘッドには、最終更新時刻、アクセス許可、パブリック設定、およびシステムのメタデータが含まれています。
* コンテナー名は Unicode で格納されているので、文字数を取得して 2 を掛けます。
* Blob コンテナーに格納されているメタデータごとに、メタデータの名前 (ASCII 形式) の長さと文字列値の長さを合計します。
* Signed Identifier ごとの 512 バイトには、Signed Identifier の名前、開始時刻、有効期限、アクセス許可が含まれています。

### <a name="blobs"></a>BLOB

次のようにして BLOB ごとに使用されるストレージの量を推定します。

* ブロック BLOB (ベース BLOB またはスナップショット)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* ページ BLOB (ベース BLOB またはスナップショット)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

内訳は次のようになります。

* BLOB ごとの 124 バイトのオーバーヘッドには、最終変更時刻、サイズ、Cache-Control、Content-Type、Content-Language、Content-Encoding、Content-MD5、アクセス許可、スナップショット情報、リース、およびシステムのメタデータが含まれます。
* BLOB 名は Unicode で格納されるため、文字数を取得して 2 を掛けます。
* 次に格納されているメタデータごとに、名前 (ASCII 形式) の長さと文字列値の長さを足します。
* 次にブロック BLOB の場合
    * ブロック一覧の 8 バイト
    * ブロックの数にブロック ID のサイズ (バイト単位) を掛けたもの
    * コミットされたブロックとコミットされていないブロックすべてのデータのサイズを足します。 スナップショットが使用されている場合、このサイズは、ベース BLOB またはスナップショット BLOB の一意のデータだけを含むことに注意してください。 コミットされていないブロックが 1 週間後に使用されていない場合、それらはガベージ コレクションで回収され、その時点で以降の課金には加算されなくなります。
* 次にページ BLOB の場合
    * 連続しないデータのあるページ範囲の数に 12 バイトを掛けます。 これは、GetPageRanges API を呼び出すときに表示される一意のページ範囲の数です。
    * 保存されているすべてのページのデータのサイズ (バイト単位) を足します。 スナップショットを使用すると、計算中の ベース BLOB またはスナップショット BLOB の一意のページだけがこのサイズに含まれることに注意してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>次のステップ

Azure ストレージの課金の詳細については、「[Windows Azure Storage の課金について](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)」を参照してください。

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他のストレージ PowerShell サンプル スクリプトは、[Azure Storage 用 PowerShell サンプル](../blobs/storage-samples-blobs-powershell.md)のページにあります。
