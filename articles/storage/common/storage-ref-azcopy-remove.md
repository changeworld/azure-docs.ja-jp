---
title: azcopy remove | Microsoft Docs
description: この記事では、azcopy remove コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: abce1acb88e920c0de7bbb6447ec9d838f10486c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033990"
---
# <a name="azcopy-remove"></a>azcopy remove

Azure ストレージ アカウントから BLOB またはファイルを削除します。

## <a name="synopsis"></a>概要

```azcopy
azcopy remove [resourceURL] [flags]
```

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](storage-use-azcopy-blobs.md)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)

## <a name="examples"></a>例

SAS を使用して 1 つの BLOB を削除します。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

SAS を使用して仮想ディレクトリ全体を削除します。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

仮想ディレクトリ内の上位の BLOB のみを削除し、サブディレクトリは削除しません。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

仮想ディレクトリ内の BLOB のサブセットを削除します (例: jpg ファイルおよび pdf ファイルのみ、または BLOB 名が "exactName" の場合)。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --include="*.jpg;*.pdf;exactName"
```

仮想ディレクトリ全体を削除しますが、スコープから特定の BLOB を除外します (たとえば、foo で始まるすべての BLOB や、bar で終わるすべての BLOB)。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --exclude="foo*;*bar"
```

URL エンコードされていない相対パスをファイルに配置して、特定の BLOB と仮想ディレクトリを削除します。

```azcopy
azcopy rm "https://[account].blob.core.windows.net/[container]/[path/to/parent/dir]" --recursive=true --list-of-files=/usr/bar/list.txt
file content:
  dir1/dir2
  blob1
  blob2

```

階層型名前空間がある BLOB ストレージ アカウントからファイルを 1 つ削除します (include/exclude はサポートされていません)。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/file]?[SAS]"
```

階層型名前空間がある BLOB ストレージ アカウントからディレクトリを 1 つ削除します (include/exclude はサポートされていません)。

```azcopy
azcopy rm "https://[account].dfs.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

## <a name="options"></a>Options

**--exclude-path string**      削除する際にこれらのパスを除外します。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを確認します。 例: myFolder;myFolder/subDirName/file.pdf。

**--exclude-pattern** string   名前がパターンの一覧と一致するファイルを除外します。 例: *.jpg;* .pdf;exactName

**-h, --help**                remove のヘルプ コンテンツを表示します

**--include-path**string      削除する際にこれらのパスのみを含めます。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを確認します。 例: myFolder;myFolder/subDirName/file.pdf

**--include-pattern** string   名前がパターンの一覧と一致するファイルをのみを含めます。 例: *.jpg;* .pdf;exactName

**--list-of-files** string     削除するファイルとディレクトリの一覧を含むファイルの場所を定義します。 相対パスは改行で区切る必要があり、パスを URL エンコードすることはできません。

**--log-level** string         ログ ファイルのログの詳細度を定義します。 利用可能なレベルは次のとおりです。INFO (すべての要求/応答)、WARNING (遅い応答)、ERROR (失敗した要求のみ)、NONE (出力ログなし)。 (規定値は 'INFO') (規定値は "INFO")

**--recursive**                ディレクトリ間で同期するときにサブディレクトリを再帰的に検索します。

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
