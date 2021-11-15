---
title: azcopy sync | Microsoft Docs
description: この記事では、azcopy sync コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 09/01/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: f6d54941d01bf6588a330dcf822b38c46ca8eed6
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846541"
---
# <a name="azcopy-sync"></a>azcopy sync

元の場所を同期先の場所にレプリケートします。 この記事では、azcopy sync コマンドの詳細なリファレンスを提供します。 元の場所と宛先の間で BLOB を同期する方法の詳細については、「[AzCopy v10 を使用して Azure Blob Storage と同期する](storage-use-azcopy-blobs-synchronize.md)」を参照してください。 Azure Files については、「[ファイルを同期する](storage-use-azcopy-files.md#synchronize-files)」を参照してください。

## <a name="synopsis"></a>概要

最後に変更された時刻が比較に使用されます。 同期先の最終更新時刻の方が新しい場合、ファイルはスキップされます。

サポートされているペアは次のとおりです。

- ローカル <-> Azure BLOB (SAS または OAuth 認証のいずれかを使用できます)
- Azure BLOB <-> Azure BLOB (ソースには SAS が含まれているか、パブリックにアクセス可能である必要があります。宛先には SAS または OAuth 認証のいずれかを使用できます)
- Azure ファイル <-> Azure ファイル (ソースには SAS が含まれているか、パブリックにアクセス可能である必要があります。宛先には SAS 認証が使用されている必要があります)

sync コマンドは、いくつかの点で copy コマンドと異なります。

1. 既定では、再帰フラグは true で、sync によってすべてのサブディレクトリがコピーされます。 再帰フラグが false の場合、sync はディレクトリ内の最上位レベルのファイルのみがコピーされます。
2. 仮想ディレクトリ間で同期する際に仮想ディレクトリの 1 つと同じ名前の BLOB がある場合は、パスの末尾にスラッシュ (例を参照) を追加します。
3. `--delete-destination` フラグが true または prompt に設定されている場合、sync を実行すると、同期元に存在しないファイルと BLOB が同期先で削除されます。

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [チュートリアル:AzCopy を使用してオンプレミスのデータをクラウド ストレージに移行する](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy と Blob Storage でデータを転送する](./storage-use-azcopy-v10.md#transfer-data)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)

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

上記と同じですが、ファイル コンテンツの MD5 ハッシュも計算し、その MD5 ハッシュを BLOB の Content-MD5 プロパティとして保存します。

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

サブディレクトリを含むディレクトリ全体を同期します (再帰は既定でオンであることに注意してください)。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

or

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

ディレクトリ内のファイルだけを同期し、サブディレクトリやサブディレクトリ内のファイルは同期しません。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

ディレクトリ内のファイルのサブセットを同期します (例: jpg ファイルと pdf ファイルのみ、またはファイル名が `exactName` の場合)。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

ディレクトリ全体を同期しますが、スコープから特定のファイルを除外します (例: foo で始まるすべてのファイルや bar で終わるすべてのファイル)。

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
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

Azure File Directory を同期する:

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" --recursive=true
```

> [!NOTE]
> include/exclude フラグが一緒に使用されている場合は、include パターンに一致するファイルだけが検索されますが、exclude パターンに一致するファイルは常に無視されます。

## <a name="options"></a>Options

**--block-size-mb** float    Azure Storage へのアップロード時または Azure Storage からのダウンロード時に、このブロック サイズ (MiB で指定) を使用します。 既定値は、ファイル サイズに基づいて自動的に計算されます。 小数を使用できます (例: `0.25`)。

**--check-md5** string   ダウンロード時に MD5 ハッシュをどれだけ厳密に検証する必要があるかを指定します。 このオプションはダウンロード時にのみ使用できます。 指定できる値には、`NoCheck`、`LogOnly`、`FailIfDifferent`、`FailIfDifferentOrMissing` が含まれます。 (既定値は `FailIfDifferent` です。) (既定値は `FailIfDifferent`)

**--cpk-by-name** string          この名前によるクライアント指定キーを含めると、Azure Blob Storage に対して要求を行うクライアントは、要求ごとに暗号化キーを指定できます。 指定されたキー名は Azure Key Vault からフェッチされ、データの暗号化に使用されます

**--cpk-by-value**                この名前によるクライアント指定キーを含めると、Azure Blob Storage に対して要求を行うクライアントは、要求ごとに暗号化キーを指定できます。 指定されたキーとそのハッシュは環境変数からフェッチされます

**--delete-destination** string   同期元に存在しない余分なファイルを同期先から削除するかどうかを定義します。 `true`、`false`、または `prompt` に設定できます。 `prompt` に設定した場合は、ファイルと BLOB の削除をスケジュールする前に、ユーザーに質問が表示されます。 (既定値は `false` です。) (既定値は `false`)

**--dry-run**                     sync コマンドによってコピーまたは削除されるはずのファイルのパスを出力します。 このフラグによって、実際のファイルがコピーされたり削除されたりすることはありません。

**--exclude-attributes**  string   (Windows のみ) 属性が属性の一覧と一致するファイルを除外します。 例: `A;S;R`

**--exclude-path** string    同期元と同期先を比較するときに、これらのパスを除外します。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを調べます (例: `myFolder;myFolder/subDirName/file.pdf`)。

**--exclude-pattern** string   名前がパターンの一覧と一致するファイルを除外します。 例: `*.jpg;*.pdf;exactName`

**--exclude-regex** string        正規表現と一致するファイルの相対パスを除外します。 複数の正規表現は ';' で区切ります。

**--help**    sync のヘルプ。

**--include-attributes** string   (Windows のみ) 属性が属性の一覧と一致するファイルのみを含めます。 例: `A;S;R`

**--include-pattern** string   名前がパターンの一覧と一致するファイルをのみを含めます。 例: `*.jpg;*.pdf;exactName`

**--log-level** string     ログ ファイルのログ詳細度を定義します。指定可能なレベルは、`INFO` (すべての要求と応答)、`WARNING` (低速な応答)、`ERROR` (失敗した要求のみ)、`NONE` (出力ログなし) です。 (既定値は `INFO` です。)

**--mirror-mode**          このフラグが `true` に設定されている場合、最終変更時刻に基づく比較が無効になり、転送先で競合するファイルと BLOB が上書きされます。 既定値は `false` です。

**--preserve-smb-info**   既定では true です。 SMB 対応リソース (Windows と Azure Files) 間の SMB プロパティ情報 (最終書き込み時刻、作成時刻、属性ビット) を保持します。 ファイルのみのフィルターが指定されている場合を除き、このフラグはファイルとフォルダーの両方に適用されます (例: include-pattern)。  フォルダーの場合に保持されない最終書き込み時刻を除き、フォルダーについて転送される情報はファイルの情報と同じです。

**--preserve-permissions**        既定では false です。 対応リソース (Windows と Azure Files、または Data Lake Storage Gen 2 から Data Lake Storage Gen 2) 間の ACL を保持します。 階層型名前空間のあるアカウントの場合は、所有権変更およびアクセス許可変更のアクセス許可とともにコンテナーの SAS または OAuth トークンが必要です。 ダウンロードする場合は、--backup フラグを使用して、新しい所有者が AzCopy を実行しているユーザーにならないアクセス許可を復元する必要もあります。 ファイルのみのフィルターが指定されているのでない限り、このフラグは両方のファイルとフォルダーに適用されます (例: include-pattern)。

**--put-md5**     各ファイルの MD5 ハッシュを作成し、ハッシュを同期先の BLOB またはファイルの Content-MD5 プロパティとして保存します。 (既定では、ハッシュは作成されません)。アップロード時にのみ使用できます。

**--recursive**    既定では `True` で、ディレクトリ間で同期するときに、サブディレクトリ内を再帰的に調べます。 (既定値は `True` です。)

**--s2s-preserve-access-tier**  サービス間のコピー中にアクセス層を保持します。 宛先ストレージ アカウントでアクセス層の設定が確実にサポートされるようにするには、「[BLOB データのホット、クール、アーカイブのアクセス層](../blobs/access-tiers-overview.md)」を参照してください。 アクセス層の設定がサポートされていない場合は、`--s2s-preserve-access-tier=false` を使用してアクセス層のコピーをバイパスしてください。 (既定値は `true` です。)

**--s2s-preserve-blob-tags**      BLOB ストレージ間でサービス間の同期中にインデックス タグを保持します。

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

|オプション|説明|
|---|---|
|--cap-mbps uint32|転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。|
|--output-type string|コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。|
|--trusted-microsoft-suffixes string   |Azure Active Directory ログイン トークンを送信できる追加のドメイン サフィックスを指定します。  既定値は " *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net" です。 ここに記載されているすべてが既定値に追加されます。 セキュリティのために、Microsoft Azure のドメインのみをここに入力してください。 複数のエンティティは、セミコロンで区切ります。|

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
