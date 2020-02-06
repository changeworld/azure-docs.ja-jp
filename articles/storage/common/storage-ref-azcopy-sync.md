---
title: azcopy sync | Microsoft Docs
description: この記事では、azcopy sync コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 1bff46c8584934ab8bcffce74763edc8363533d6
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988245"
---
# <a name="azcopy-sync"></a>azcopy sync

元の場所を同期先の場所にレプリケートします。

## <a name="synopsis"></a>概要

最後に変更された時刻が比較に使用されます。 同期先の最終更新時刻の方が新しい場合、ファイルはスキップされます。

サポートされているペアは次のとおりです。

- ローカル <-> Azure BLOB (SAS または OAuth 認証のいずれかを使用できます)
- Azure BLOB <-> Azure BLOB (ソースには SAS が含まれているか、パブリックにアクセス可能である必要があります。宛先には SAS または OAuth 認証のいずれかを使用できます)
- Azure ファイル <-> Azure ファイル (ソースには SAS が含まれているか、パブリックにアクセス可能である必要があります。宛先には SAS 認証が使用されている必要があります)

sync コマンドは、いくつかの点で copy コマンドと異なります。

1. 既定では、再帰フラグは true で、sync によってすべてのサブディレクトリがコピーされます。 再帰フラグが false の場合、sync はディレクトリ内の最上位レベルのファイルのみがコピーされます。
2. 仮想ディレクトリ間で同期する際に仮想ディレクトリの 1 つと同じ名前の BLOB がある場合は、パスの末尾にスラッシュ (例を参照) を追加します。
3. 'DeleteDestination' フラグが true または prompt に設定されている場合、sync を実行するとソースに存在しないファイルと BLOB を削除します。

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](storage-use-azcopy-blobs.md)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)

### <a name="advanced"></a>詳細設定

ファイル拡張子を指定しない場合、AzCopy は、ファイルの拡張子またはコンテンツ (拡張子が指定されていない場合) に基づいて、ローカル ディスクからアップロードするときにファイルのコンテンツの種類を自動的に検出します。

組み込みのルックアップ テーブルは小さいですが、Unix では、次の 1 つ以上の名前において使用可能な場合は、ローカル システムの mime.types ファイルによって拡張されます。

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows では、MIME の種類はレジストリから抽出されます。

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>例

1 つのファイルを同期する

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

上記と同じですが、今度はファイル コンテンツの MD5 ハッシュも計算し、BLOB の Content-MD5 プロパティとして保存します。

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

サブディレクトリを含むディレクトリ全体を同期します (再帰は既定でオンになっていることに注意してください)。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

or

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

ディレクトリ内の上位のファイルのみを同期しますが、サブディレクトリは同期しません。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

ディレクトリ内のファイルのサブセットを削除します (例: jpg ファイルおよび pdf ファイルのみ、またはファイル名が "exactName" の場合)。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include="*.jpg;*.pdf;exactName"
```

ディレクトリ全体を同期しますが、スコープから特定のファイルを除外します (たとえば、foo で始まるすべてのファイルや、bar で終わるすべてのファイル)。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude="foo*;*bar"
```

1 つの BLOB を同期する:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

1 つの仮想ディレクトリを同期する:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

BLOB と同じ名前の仮想ディレクトリを同期します (あいまいさを排除するため、パスの末尾にスラッシュを追加します)。

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Azure ファイル ディレクトリを同期する (BLOB と同じ構文):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> include/exclude フラグが一緒に使用されている場合は、include パターンに一致するファイルだけが検索されますが、exclude パターンに一致するファイルは常に無視されます。

## <a name="options"></a>オプション

**--block-size-mb** float         Azure Storage にアップロードする場合、または Azure Storage からダウンロードする場合に、このブロック サイズ (MiB で指定) を使用します。 既定値は、ファイル サイズに基づいて自動的に計算されます。 小数を使用できます (例:0.25)。

**--check-md5** string            ダウンロード時に MD5 ハッシュを検証する厳密さを指定します。 このオプションはダウンロード時にのみ使用できます。 指定できる値は、次のとおりです。NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing です。 (既定値は "FailIfDifferent")。 (既定値は "FailIfDifferent")

**--delete-destination** string   同期元に存在しない余分なファイルを同期先から削除するかどうかを定義します。 true、false、または prompt に設定できます。 prompt に設定すると、ファイルと BLOB の削除をスケジュールする前に、ユーザーに質問が表示されます。 (既定値は 'false')。 (既定値は "false")

**--exclude-attributes**  string   (Windows のみ) 属性が属性の一覧と一致するファイルを除外します。 次に例を示します。A;S;R

**--exclude-path** string コピーするときにこれらのパスを除外します。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを確認します (例: myFolder;myFolder/subDirName/file.pdf)。 アカウント トラバーサルと組み合わせて使用する場合、パスにはコンテナー名は含まれません。

**--exclude-pattern** string      名前がパターンの一覧と一致するファイルを除外します。 例: \*.jpg;\*.pdf;exactName

**-h, --help**                sync のヘルプ コンテンツを表示します

**--include-attributes**  string   (Windows のみ) 属性が属性の一覧と一致するファイルのみを含めます。 次に例を示します。A;S;R

**--include-pattern** string      名前がパターンの一覧と一致するファイルをのみを含めます。 例: \*.jpg;\*.pdf;exactName

**--log-level** string            ログ ファイルのログの詳細度を定義します。使用できるレベルは次のとおりです。INFO(すべての要求と応答)、WARNING(遅い応答)、ERROR(失敗した要求のみ)、および NONE(出力ログなし)。 (既定値は INFO)。 (既定値は "INFO")

**--put-md5**                     各ファイルの MD5 ハッシュを作成し、ハッシュを同期先の BLOB またはファイルの Content-MD5 プロパティとして保存します。 (既定では、ハッシュは作成されません)。アップロード時にのみ使用できます。

**--recursive**                   既定では true で、ディレクトリ間で同期するときにサブディレクトリを再帰的に検索します。 (既定値は true)。 (既定値は true)

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|

## <a name="see-also"></a>参照

- [azcopy](storage-ref-azcopy.md)
