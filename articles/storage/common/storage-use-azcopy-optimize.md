---
title: Azure Storage で AzCopy v10 のパフォーマンスを最適化する | Microsoft Docs
description: この記事は、Azure Storage で AzCopy v10 のパフォーマンスを最適化する際に役立ちます。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: f421e2aee0dc6a056cd28cf66a66dda41ce3f9e9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615531"
---
# <a name="optimize-the-performance-of-azcopy-with-azure-storage"></a>Azure Storage で AzCopy のパフォーマンスを最適化する

AzCopy は、ストレージ アカウント間の BLOB またはファイル コピーに利用できるコマンドライン ユーティリティです。 この記事は、パフォーマンスの最適化に役立ちます。

> [!NOTE]
> AzCopy の使用の開始に役立つコンテンツを探している場合は、「[AzCopy を使ってみる](storage-use-azcopy-v10.md)」を参照してください

パフォーマンスのベンチマークを実行し、コマンドと環境変数を使用して、パフォーマンスとリソース消費の最適なトレードオフを見つけることができます。

## <a name="run-benchmark-tests"></a>ベンチマーク テストを実行する

特定の BLOB コンテナーまたはファイル共有に対してパフォーマンス ベンチマーク テストを実行して、全般的なパフォーマンスの統計情報を表示し、パフォーマンスのボトルネックを特定できます。 生成されたテスト データをアップロードまたはダウンロードして、テストを実行できます。

パフォーマンス ベンチマーク テストを実行するには、次のコマンドを使用します。

**構文**

`azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'`

**例**

```azcopy
azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'
```

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

このコマンドは、指定したコピー先にテスト データをアップロードすることで、パフォーマンス ベンチマークを実行します。 テスト データはメモリ内に生成され、コピー先にアップロードされた後、テストの完了後にコピー先から削除されます。 オプションのコマンド パラメーターを使用して、生成するファイルの数と、必要なサイズを指定できます。

データをダウンロードしてこのテストを実行する場合は、`mode` パラメーターを `download` に設定します。 詳細なリファレンス ドキュメントについては、「[azcopy benchmark](storage-ref-azcopy-bench.md)」を参照してください。

## <a name="optimize-for-large-numbers-of-small-files"></a>多数の小さいファイル向けに最適化する

小さいファイルの転送時、特に多数を転送する場合に、スループットが低下することがあります。 パフォーマンスを最大化するには、各ジョブのサイズを小さくします。 ダウンロードとアップロードの操作については、コンカレンシーを向上させ、ログ アクティビティを減らし、ハイ パフォーマンスのコストを発生させる機能を無効にします。

#### <a name="reduce-the-size-of-each-job"></a>各ジョブのサイズを小さくする

最適なパフォーマンスを実現するには、各ジョブで転送するファイルを 1,000 万件未満にします。 5,000 万件を超えるファイルを転送するジョブは、AzCopy ジョブ追跡メカニズムによって大量のオーバーヘッドが発生するため、パフォーマンスが低下する可能性があります。 オーバーヘッドを削減するには、大きいジョブをより小さいジョブに分割することを検討してください。

ジョブのサイズを小さくする方法の 1 つは、ジョブによって影響を受けるファイルの数を制限することです。 これを行うには、コマンド パラメーターを使用します。 たとえば、ジョブは、[azcopy copy](storage-ref-azcopy-copy.md) コマンドの一部として `include path` パラメーターを使用して、ディレクトリのサブセットのみをコピーできます。

`include-pattern` パラメーターを使用して、特定の拡張子 (例: `*.pdf`) を持つファイルをコピーします。 別のジョブでは、`exclude-pattern` パラメーターを使用して、`*.pdf` 拡張子を持たないすべてのファイルをコピーします。 例については、「[特定のファイルをアップロードする](storage-use-azcopy-blobs-upload.md#upload-specific-files)」と「[特定の BLOB をダウンロードする](storage-use-azcopy-blobs-download.md#download-specific-blobs)」を参照してください。

大きいジョブをより小さいジョブに分割する方法を決定したら、複数の仮想マシン (VM) でジョブを実行することを検討します。

#### <a name="increase-concurrency"></a>コンカレンシーを向上させる

ファイルをアップロードまたはダウンロードする場合は、`AZCOPY_CONCURRENCY_VALUE` 環境変数を使用して、マシンで実行可能な同時要求数を増やします。 この変数は、マシンのパフォーマンスを損なわずにできる限り高く設定します。 この変数の詳細については、この記事の「[同時要求数を増やす](#increase-the-number-of-concurrent-requests)」セクションを参照してください。

ストレージ アカウント間で BLOB をコピーする場合は、`AZCOPY_CONCURRENCY_VALUE` 環境変数の値を `1000` よりも大きい値に設定することを検討します。 AzCopy はサーバー間 API を使用するので、この変数を高く設定できます。これにより、データはストレージ サーバー間で直接コピーされ、マシンの処理能力を使用しません。

#### <a name="decrease-the-number-of-logs-generated"></a>生成されるログの数を減らす

AzCopy が操作を完了したときに作成するログ エントリの数を減らすことで、パフォーマンスを向上させることができます。 既定では、AzCopy は操作に関連するすべてのアクティビティをログします。 最適なパフォーマンスを実現するには、コピー、同期、または削除コマンドの `log-level` パラメーターを `ERROR` に設定することを検討してください。 これにより、AzCopy はエラーのみをログします。 既定では、ログ レベルの値は `INFO` に設定されています。

#### <a name="turn-off-length-checking"></a>長さのチェックを無効にする

ファイルをアップロードまたはダウンロードする場合は、コピーと同期のコマンドの `--check-length` を `false` に設定することを検討します。 これにより、転送後に AzCopy によるファイルの長さの検証が行われなくなります。 既定では、AzCopy は、ソースとターゲットのファイルが一致することを確認するために、転送が完了した後に長さを確認します。 AzCopy は、各ファイルの転送後にこのチェックを実行します。 ジョブが多数の小さなファイルを転送する場合は、このチェックによってパフォーマンスが低下する可能性があります。

#### <a name="turn-on-concurrent-local-scanning-linux"></a>同時ローカル スキャンを有効にする (Linux)

一部の Linux システムでのファイル スキャンは、すべての並列ネットワーク接続を飽和状態にするのに十分な速度で実行されません。 このような場合に、`AZCOPY_CONCURRENT_SCAN` を `true` に設定できます。

## <a name="increase-the-number-of-concurrent-requests"></a>同時要求数を増やす

`AZCOPY_CONCURRENCY_VALUE` 環境変数を設定することにより、スループットを向上させることができます。 この変数は、同時に発生することができる要求の数を指定します。

コンピューターの CPU が 5 個未満の場合、この変数の値は `32` に設定されます。 それ以外の場合、既定値は CPU の数に 16 を掛けた数です。 この変数の最大の既定値は `300` ですが、この値は手動で高い値または低い値に設定できます。

| オペレーティング システム | コマンド  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

この変数の現在の値を確認するには、`azcopy env` を使用します。 値が空白の場合は、AzCopy ログ ファイルの先頭を調べることで、使用されている値を読み取ることができます。 選択された値と選択された理由が報告されています。

この変数を設定する前に、ベンチマーク テストを実行することをお勧めします。 ベンチマーク テスト プロセスによって、推奨されるコンカレンシーの値が報告されます。 または、ネットワークの状態とペイロードが一様でない場合は、この変数を特定の数値ではなく `AUTO` という単語に設定します。 これにより、AzCopy はベンチマーク テストで使用するのと同じ自動チューニング プロセスを常に実行します。

## <a name="limit-the-throughput-data-rate"></a>スループット データ速度を制限する

コマンドで `cap-mbps` フラグを使用して、スループット データ速度の上限を設定できます。 たとえば、次のコマンドではジョブを再開し、スループットを 1 秒あたり `10` メガビット (Mb) に制限します。

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

## <a name="optimize-memory-use"></a>メモリ使用量を最適化する

`AZCOPY_BUFFER_GB` 環境変数を設定して、ファイルをダウンロードおよびアップロードするときに AzCopy でバッファリング用に使用するシステム メモリの最大量を指定します。 この値はギガバイト (GB) 単位で指定します。

| オペレーティング システム | コマンド  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

> [!NOTE]
> ジョブの追跡では、メモリ使用量で追加のオーバーヘッドが常に発生します。 この量は、ジョブでの転送数によって異なります。 バッファーは、メモリ使用量の最大のコンポーネントです。 `AZCOPY_BUFFER_GB` を使用してオーバーヘッドを制御することで、要件をほぼ満たすことができますが、全体的なメモリ使用量を厳密に制限するために使用できるフラグはありません。

## <a name="optimize-file-synchronization"></a>ファイル同期を最適化する

[sync](storage-ref-azcopy-sync.md) コマンドは、転送先のすべてのファイルを識別し、同期操作を開始する前に、ファイル名と最終変更されたタイムスタンプを比較します。 多数のファイルがある場合は、この前処理を排除することでパフォーマンスを向上させることができます。

これを実現するには、代わりに [azcopy copy](storage-ref-azcopy-copy.md) コマンドを使用して、`--overwrite` フラグを `ifSourceNewer`に設定します。 AzCopy では、アップフロントスキャンと比較を実行せずに、ファイルがコピーされると比較されます。 これにより、比較対象のファイルの数が多い場合にパフォーマンスが低下します。

[azcopy copy](storage-ref-azcopy-copy.md) コマンドは、転送先からファイルを削除しません。そのため、コピー先のファイルがコピー元に存在しなくなったときにファイルを削除する場合は、[azcopy sync](storage-ref-azcopy-sync.md) コマンドを使用し、`--delete-destination` フラグを `true` または `prompt`の値に設定します。

## <a name="see-also"></a>関連項目

- [AzCopy を使ってみる](storage-use-azcopy-v10.md)
