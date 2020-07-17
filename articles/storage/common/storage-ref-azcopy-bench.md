---
title: azcopy bench | Microsoft Docs
description: この記事では、azcopy bench コマンドの参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518127"
---
# <a name="azcopy-bench"></a>azcopy bench

指定した宛先にテスト データをアップロードすることで、パフォーマンス ベンチマークを実行します。 テスト データは自動的に生成されます。

このベンチマーク コマンドは、次の点を除いて、'copy' と同じアップロード プロセスを実行します。

  - ソース パラメーターはありません。  コマンドには、宛先 URL のみが必要です。 現在のリリースでは、この宛先 URL は BLOB コンテナーを参照する必要があります。
  
  - ペイロードは、自動生成されるファイルの数とサイズを制御するコマンド ライン パラメーターによって記述されます。 生成プロセスは、すべてメモリ内で行われます。 ディスクは使用されません。
  
  - copy コマンドで使用できるオプションのパラメーターの一部のみがサポートされています。
  
  - 追加の診断が測定および報告されます。
  
  - 既定では、転送されたデータはテスト実行の終了時に削除されます。

ベンチマーク モードは、最大スループットを提供する並列 TCP 接続の数に自動的に調整されます。 最後にその数値が表示されます。 自動チューニングを行わないようにするには、AZCOPY_CONCURRENCY_VALUE 環境変数を特定の数の接続に設定します。

一般的な認証の種類がすべてサポートされています。 ただし、ベンチマークを行うための最も便利な方法は、通常、SAS トークン付きの空のコンテナーを作成し、SAS 認証を使用することです。

## <a name="examples"></a>例

```azcopy
azcopy bench [destination] [flags]
```

既定のパラメーターを使用してベンチマーク テストを実行します (最大 1 Gbps のネットワークのベンチマークに適しています)。

- azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"

サイズがそれぞれ 2 GiB の 100 個のファイルをアップロードするベンチマーク テストを実行します (10 Gbps などの高速ネットワークでのベンチマークに適しています)。

- azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --size-per-file 2G

上記と同じですが、サイズがそれぞれ 8 MiB の 5 万個のファイルを使用し、それらの MD5 ハッシュを計算します (--put-md5 フラグが copy コマンドでこれを行うのと同じ方法を使用します)。 ベンチマーク時の --put-md5 の目的は、MD5 の計算が、選択したファイルの数とサイズのスループットに影響するかどうかをテストすることです。

- azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5

## <a name="options"></a>Options

**--blob-type** string  宛先の BLOB の種類を定義します。 さまざまな種類の BLOB のベンチマークを許可するために使用されます。 copy コマンドの同一の名前のパラメーターと同じです (既定値は "Detect")。

**--block-size-mb** float  このブロック サイズ (MiB で指定) を使用します。 既定値は、ファイル サイズに基づいて自動的に計算されます。 小数を使用できます (例: 0.25)。 copy コマンドの同一の名前のパラメーターと同じです。

**--delete-test-data**  true の場合、ベンチマーク実行の最後にベンチマーク データが削除されます。  たとえば、ベンチマーク モード以外で手動テストに使用するためなどに、宛先でデータを保持する場合は false に設定します (既定値は true)。

**--file-count** uint  使用するために自動生成されるデータ ファイルの数 (既定値は 100)。

**-h, --help**  bench のヘルプを表示します。

**--log-level** string ログ ファイルのログの詳細度を定義します。使用できるレベルは次のとおりです。INFO (すべての要求/応答)、WARNING (遅い応答)、ERROR (失敗した要求のみ)、NONE (出力ログなし)。 (既定値は "INFO")

**--put-md5**  各ファイルの MD5 ハッシュを作成し、そのハッシュを宛先の BLOB またはファイルの Content-MD5 プロパティとして保存します。 (既定では、ハッシュは作成されません)。copy コマンドの同一の名前のパラメーターと同じです。

**--size-per-file** string   自動生成される個々のデータ ファイルのサイズ。 直後に K、M、または G が続く数値を指定する必要があります。例: 12k または 200G (既定値は "250M")。

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

**--cap-mbps uint32**  転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。

**--output-type** string  コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。 (既定値 "text")。

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
