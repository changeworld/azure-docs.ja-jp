---
title: azcopy copy| Microsoft Docs
description: この記事では、azcopy copy コマンドに関する参照情報を提供します。
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 0c1b09fbc425a80fe1f8d075c5a83455167073c3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74029996"
---
# <a name="azcopy-copy"></a>azcopy copy

ソース データをコピー先の場所にコピーします。

## <a name="synopsis"></a>概要

ソース データをコピー先の場所にコピーします。 サポートされている方向は次のとおりです。

  - ローカル <-> Azure BLOB (SAS または OAuth 認証)
  - ローカル <-> Azure Files (共有/ディレクトリ SAS 認証)
  - ローカル <-> ADLS Gen 2 (SAS、OAuth、または SharedKey 認証)
  - Azure BLOB (SAS またはパブリック) -> Azure BLOB (SAS または OAuth 認証)
  - Azure BLOB (SAS またはパブリック) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure BLOB (SAS または OAuth 認証)
  - AWS S3 (アクセス キー) <-> Azure ブロック BLOB (SAS または OAuth 認証)

詳細については、例を参照してください。

## <a name="related-conceptual-articles"></a>関連する概念に関する記事

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
- [AzCopy と Blob Storage でデータを転送する](storage-use-azcopy-blobs.md)
- [AzCopy とファイル ストレージでデータを転送する](storage-use-azcopy-files.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)

## <a name="advanced"></a>詳細

AzCopy は、ファイルの拡張子またはコンテンツ (拡張子が指定されていない場合) に基づいて、ローカル ディスクからアップロードするときにファイルのコンテンツの種類を自動的に検出します。

組み込みのルックアップ テーブルは小さいですが、Unix では、次の 1 つ以上の名前において使用可能な場合は、ローカル システムの mime.types ファイルによって拡張されます。

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

Windows では、MIME の種類はレジストリから抽出されます。 この機能は、フラグを使用して無効にできます。 フラグに関するセクションを参照してください。

コマンド ラインを使用して環境変数を設定した場合、その変数はコマンド ラインの履歴で読み取ることができます。 資格情報を含む変数をコマンド ラインの履歴から消去することを検討してください。 変数が履歴に表示されないようにするために、ユーザーに資格情報の入力を要求し、環境変数を設定するためのスクリプトを使用できます。

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>例

OAuth 認証を使用して 1 つのファイルをアップロードします。 AzCopy にまだログインしていない場合は、次のコマンドを実行する前に azcopy login コマンドを実行してください。

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"

上記と同じですが、今度はファイル コンテンツの MD5 ハッシュも計算し、BLOB の Content-MD5 プロパティとして保存します。

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5

SAS トークンを使用して 1 つのファイルをアップロードします。

- azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"

SAS トークンとパイプを使用して 1 つのファイルをアップロードします (ブロック BLOB のみ)。
  
- cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"

SAS トークンを使用してディレクトリ全体をアップロードします。
  
- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true

or

- azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true --put-md5

SAS トークンとワイルドカード文字 (*) を使用して、一連のファイルをアップロードします。

- azcopy cp "/path/*foo/* bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"

SAS トークンとワイルドカード文字 (*) を使用して、ファイルとディレクトリをアップロードします。

- azcopy cp "/path/*foo/* bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true

OAuth 認証を使用して 1 つのファイルをダウンロードします。 AzCopy にまだログインしていない場合は、次のコマンドを実行する前に azcopy login コマンドを実行してください。

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"

SAS トークンを使用して 1 つのファイルをダウンロードします。

- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"

SAS トークンを使用し、次に出力をファイルにパイプして、1 つのファイルをダウンロードします (ブロック BLOB のみ)。
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"

SAS トークンを使用してディレクトリ全体をダウンロードします。
  
- azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive=true

URL でのワイルドカード文字 (*) の使用に関する注意事項:

URL でワイルドカード文字を使用するには、2 つの方法のみがサポートされます。 

- URL の最後のスラッシュ (/) の直後に 1 つ使用できます。 これにより、ディレクトリ内のすべてのファイルが、サブディレクトリに配置されることなく、宛先に直接コピーされます。

- URL が、BLOB ではなく、コンテナーのみを参照している場合は、コンテナーの名前で 1 つ使用することもできます。 この方法を使用して、コンテナーのサブセットからファイルを取得できます。

格納しているディレクトリ自体をコピーせずに、ディレクトリの内容をダウンロードします。

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"

ストレージ アカウント全体をダウンロードします。

- azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive

コンテナー名にワイルドカード記号 (*) を使用して、ストレージ アカウント内のコンテナーのサブセットをダウンロードします。

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive

SAS トークンを使用して、1 つの BLOB を別の BLOB にコピーします。

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"

SAS トークンと OAuth トークンを使用して、1 つの BLOB を別の BLOB にコピーします。 ソース アカウント URL の末尾に SAS トークンを使用する必要がありますが、azcopy login コマンドを使用して AzCopy にログインする場合、コピー先アカウントでは不要です。 

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"

SAS トークンを使用して、1 つの BLOB 仮想ディレクトリを別の BLOB 仮想ディレクトリにコピーします。

- azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true

SAS トークンを使用して、すべての BLOB コンテナー、ディレクトリ、BLOB をストレージ アカウントから別のストレージ アカウントにコピーします。

- azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true

アクセス キーと SAS トークンを使用して、アマゾン ウェブ サービス (AWS) S3 から Blob Storage に 1 つのオブジェクトをコピーします。 最初に、AWS S3 ソースの環境変数 AWS_ACCESS_KEY_ID と AWS_SECRET_ACCESS_KEY を設定します。
  
- azcopy cp "https://s3.amazonaws.com/ [bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"

アクセス キーと SAS トークンを使用して、AWS S3 から Blob Storage にディレクトリ全体をコピーします。 最初に、AWS S3 ソースの環境変数 AWS_ACCESS_KEY_ID と AWS_SECRET_ACCESS_KEY を設定します。

- azcopy cp "https://s3.amazonaws.com/ [bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true

[folder] プレースホルダーをさらによく理解するには、 https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html を参照してください。

アクセス キーと SAS トークンを使用して、アマゾン ウェブ サービス (AWS) から Blob Storage にすべてのバケットをコピーします。 最初に、AWS S3 ソースの環境変数 AWS_ACCESS_KEY_ID と AWS_SECRET_ACCESS_KEY を設定します。

- azcopy cp "https://s3.amazonaws.com/ " "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true

アクセス キーと SAS トークンを使用して、アマゾン ウェブ サービス (AWS) のリージョンから Blob Storage にすべてのバケットをコピーします。 最初に、AWS S3 ソースの環境変数 AWS_ACCESS_KEY_ID と AWS_SECRET_ACCESS_KEY を設定します。

- azcopy cp "https://s3- [region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true

バケット名にワイルドカード記号 (*) を使用して、バケットのサブセットをコピーします。 前の例と同様に、アクセス キーと SAS トークンが必要になります。 AWS S3 ソースの環境変数 AWS_ACCESS_KEY_ID と AWS_SECRET_ACCESS_KEY を設定していることを確認します。

- azcopy cp "https://s3.amazonaws.com/ [bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive=true

## <a name="options"></a>オプション

**--blob-type** string                     コピー先の BLOB の種類を定義します。 これは、BLOB をアップロードする場合と、アカウント間でコピーする場合に使用されます (既定値は "Detect")。 有効な値としては、"Detect"、"BlockBlob"、"PageBlob"、および "AppendBlob" があります。 アカウント間でコピーする場合、値 "Detect" を使用すると、AzCopy はソース BLOB の種類を使用して、コピー先 BLOB の種類を判断します。 ファイルをアップロードするとき、"Detect" は、ファイル拡張子に基づいて、ファイルが VHD ファイルまたは VHDX ファイルであるかを判断します。 ファイルが VHD ファイルまたは VHDX ファイルの場合、AzCopy はそのファイルをページ BLOB として扱います。 (既定値は "Detect")

**--block-blob-tier** string              この BLOB 層を使用してブロック BLOB を Azure Storage にアップロードします。 (既定値は "None")

**--block-size-mb** float                  Azure Storage にアップロードするとき、および Azure Storage からダウンロードするときに、このブロック サイズ (MiB で指定) を使用します。 既定値は、ファイル サイズに基づいて自動的に計算されます。 小数を使用できます (例:0.25)。

**--cache-control** string                 cache-control ヘッダーを設定します。 ダウンロード時に返されます。

**--check-length**                         転送後にコピー先のファイルの長さを確認します。 コピー元とコピー先の間に不一致がある場合は、転送に失敗のマークが付けられます。 (既定値は true)

**--check-md5** string                     ダウンロード時に MD5 ハッシュをどのくらい厳密に検証する必要があるかを指定します。 ダウンロード時にのみ使用できます。 使用できるオプションは NoCheck、LogOnly、FailIfDifferent、FailIfDifferentOrMissing です。 (既定値は "FailIfDifferent")

**--content-disposition** string           content-disposition ヘッダーを設定します。 ダウンロード時に返されます。

**--content-encoding** string              content-encoding ヘッダーを設定します。 ダウンロード時に返されます。

**--content-encoding** string              content-language ヘッダーを設定します。 ダウンロード時に返されます。

**--content-type** string                  ファイルのコンテンツの種類を指定します。 no-guess-mime-type を暗黙的に示します。 ダウンロード時に返されます。

**--decompress**                           content-encoding が、ファイルが圧縮されていることを示している場合、ダウンロードするときに自動的に圧縮解除します。 サポートされている content-encoding の値は、"gzip" と "deflate" です。 ファイル拡張子 ".gz"/".gzip" または ".zz" は不要です。ある場合は削除されます。

**--exclude-attributes**  string            (Windows のみ) 属性が属性一覧と一致するファイルを除外します。 例: A;S;R

**--exclude-blob-type** string             必要に応じて、コンテナーまたはアカウントから BLOB をコピーするときに除外する BLOB の種類 (BlockBlob/PageBlob/AppendBlob) を指定します。 このフラグの使用は、Azure 以外のサービスからサービスにデータをコピーする場合には適用されません。 複数の BLOB は ";" で区切る必要があります。

**--exclude-path** string                  コピーするときにこれらのパスを除外します。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを確認します (例: myFolder;myFolder/subDirName/file.pdf)。 アカウント トラバーサルと組み合わせて使用する場合、パスにはコンテナー名は含まれません。

**--exclude-pattern** string               コピーするときにこれらのファイルを除外します。 このオプションでは、ワイルドカード文字 (*) がサポートされます。

**--follow-symlinks**                      ローカル ファイル システムからアップロードするときにシンボリック リンクに従います。

**--from-to** string                       必要に応じて、コピー元とコピー先の組み合わせを指定します。 次に例を示します。LocalBlob、BlobLocal、LocalBlobFS。

**-h, --help**                                 copy のヘルプ

**--include-attributes**  string            (Windows のみ) 属性が属性一覧と一致するファイルを含めます。 例: A;S;R

**--include-path** string                  コピーするときにこれらのパスのみを含めます。 このオプションでは、ワイルドカード文字 (*) はサポートされていません。 相対パスのプレフィックスを確認します (例: myFolder;myFolder/subDirName/file.pdf)。

**--include-pattern** string               コピーするときにこれらのファイルのみを含めます。 このオプションでは、ワイルドカード文字 (*) がサポートされます。 ";" を使用してファイルを区切ります。

**--log-level** string                     ログ ファイルのログの詳細度を定義します。使用できるレベルは次のとおりです。INFO (すべての要求/応答)、WARNING (遅い応答)、ERROR (失敗した要求のみ)、NONE (出力ログなし)。 (既定値は "INFO")

**--metadata** string                      これらのキーと値のペアをメタデータとして Azure Storage にアップロードします。

**--no-guess-mime-type**                   AzCopy がファイルの拡張子または内容に基づいてコンテンツ タイプを検出しないようにします。

**--overwrite** string                     このフラグが true に設定されている場合は、コピー先の競合するファイルおよび BLOB を上書きします。 指定できる値は、"true"、"false"、および "prompt" です。 (既定値は "true")

**--page-blob-tier** string                この BLOB 層を使用して Azure Storage にページ BLOB をアップロードします。 (既定値は "None")

**--preserve-last-modified-time**          コピー先がファイル システムの場合にのみ使用できます。

**--put-md5**                             各ファイルの MD5 ハッシュを作成し、ハッシュを宛先 BLOB またはファイルの Content-MD5 プロパティとして保存します。 (既定では、ハッシュは作成されません)。アップロード時にのみ使用できます。

**--recursive**                            ローカル ファイル システムからアップロードするときにサブディレクトリを再帰的に検索します。

**--s2s-detect-source-changed**           列挙後にソースが変更されたかどうかを確認します。

**--s2s-handle-invalid-metadata** string   無効なメタデータ キーの処理方法を指定します。 使用できるオプションは ExcludeIfInvalid、FailIfInvalid、RenameIfInvalid です。 (既定値は "ExcludeIfInvalid")

**--s2s-preserve-access-tier**             サービス間のコピー中にアクセス層を保持します。 「[Azure Blob storage: ホット、クール、およびアーカイブ アクセス層](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)」を参照して、コピー先ストレージ アカウントでアクセス層の設定がサポートされていることを確認してください。 アクセス層の設定がサポートされていない場合は、s2sPreserveAccessTier = false を使用してアクセス層のコピーをバイパスしてください。 (既定値は true)

**--s2s-preserve-properties**              サービス間のコピー中にすべてのプロパティを保持します。 AWS S3 と Azure File の非単一ファイル ソースの場合、リスト操作はオブジェクトとファイルの完全なプロパティを返しません。 完全なプロパティを保持するために、AzCopy では、オブジェクトまたはファイルごとに 1 つの追加の要求を送信する必要があります。 (既定値は true)

## <a name="options-inherited-from-parent-commands"></a>親コマンドから継承されるオプション

**--cap-mbps uint32**      転送速度の上限を設定します (メガビット/秒)。 瞬間的なスループットは、上限と若干異なる場合があります。 このオプションを 0 に設定した場合や省略した場合、スループットは制限されません。

**--output-type** string   コマンドの出力形式。 選択肢には、text、json などがあります。 既定値は "text" です。 (既定値は "text")

## <a name="see-also"></a>関連項目

- [azcopy](storage-ref-azcopy.md)
