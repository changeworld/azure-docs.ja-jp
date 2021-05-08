---
title: Azure HPC Cache で NFS BLOB ストレージを使用する
description: Azure HPC Cache で ADLS-NFS BLOB ストレージを使用する場合の手順と制限事項について説明します
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: d861a41d8cdeac548729ff341b3ffe27c0aa8152
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259925"
---
# <a name="use-nfs-mounted-blob-storage-preview-with-azure-hpc-cache"></a>NFS でマウントされた BLOB ストレージ (プレビュー) を Azure HPC Cache で使用する

2021 年 3 月から、NFS でマウントされた BLOB コンテナーを Azure HPC Cache で使用できるようになっています。 [Azure Blob Storage での NFS 3.0 プロトコルのサポート](../storage/blobs/network-file-system-protocol-support.md)の詳細については、Blob Storage に関するドキュメント サイトを参照してください。

> [!NOTE]
> Azure Blob Storage での NFS 3.0 プロトコルのサポートはプレビュー段階であり、運用環境では使用できません。 [NFS プロトコル サポートに関するドキュメント](../storage/blobs/network-file-system-protocol-support.md)で更新プログラムと詳細を確認してください。

Azure HPC Cache では、その ADLS-NFS ストレージ ターゲットの種類として NFS 対応 BLOB ストレージを使用します。 これらのストレージ ターゲットは、通常の NFS ストレージ ターゲットと似ているだけでなく、通常の Azure Blob ターゲットと重なる部分もいくつかあります。

この記事では、ADLS-NFS ストレージ ターゲットを使用する際に理解しておく必要がある戦略と制限事項について説明します。

NFS BLOB に関するドキュメント、特に互換性のあるシナリオとないシナリオについて説明するこれらのセクションも参照してください。

* [機能の概要](../storage/blobs/network-file-system-protocol-support.md#applications-and-workloads-suited-for-this-feature)
* [まだサポートされていない機能](../storage/blobs/network-file-system-protocol-support.md#azure-storage-features-not-yet-supported)
* [パフォーマンスに関する考慮事項](../storage/blobs/network-file-system-protocol-support-performance.md)

## <a name="understand-consistency-requirements"></a>整合性の要件について

HPC Cache では、ADLS-NFS ストレージ ターゲットの厳密な整合性が必要です。 既定では、NFS 対応の BLOB ストレージで厳密にはファイル メタデータが更新されません。これにより、HPC Cache でファイルのバージョンを正確に比較できなくなります。

この違いに対処するために、Azure HPC Cache では、ストレージ ターゲットとして使用される任意の NFS 対応 BLOB コンテナーでの NFS 属性キャッシュを自動的に無効にします。

この設定は、コンテナーをキャッシュから削除した場合でも、その有効期間にわたって保持されます。

## <a name="preload-data-with-nfs-protocol"></a>NFS プロトコルを使用してデータを事前に読み込む

NFS 対応 BLOB コンテナーで ''*ファイルを編集できるのは、作成時に使用したものと同じプロトコルでのみです*''。 つまり、Azure REST API を使用してコンテナーを設定した場合、NFS を使用してそれらのファイルを更新することはできません。 Azure HPC Cache では NFS のみを使用するため、Azure REST API で作成されたファイルを編集することはできません。

コンテナーが空の場合、またはファイルが NFS を使用して作成された場合、キャッシュに問題はありません。

コンテナー内のファイルが、NFS ではなく Azure Blob の REST API で作成された場合、Azure HPC Cache による元のファイルに対するアクションは以下のものに制限されます。

* ディレクトリ内のファイルを一覧表示する。
* ファイルを読み取る (およびそれ以降の読み取りのためにキャッシュに保持する)。
* ファイルを削除する。
* ファイルを空にする (0 に切り捨てる)。
* スクリプトのコピーを保存する。 コピーは NFS で作成されたファイルとしてマークされ、NFS を使用して編集できます。

Azure HPC Cache では、REST を使用して作成されたファイルの内容を編集することは **できません**。 これは、変更されたファイルをクライアントからストレージ ターゲットに保存し直すことができないことを意味します。

NFS で作成されていないファイルに対して読み取り/書き込みキャッシュ使用モデルを利用するとデータ整合性の問題が発生する可能性があるため、この制限を理解しておくことが重要です。

> [!TIP]
> 読み取りおよび書き込みキャッシュの詳細については、「[キャッシュ使用モデルについて](cache-usage-models.md)」を参照してください。

### <a name="write-caching-scenarios"></a>書き込みキャッシュのシナリオ

これらのキャッシュ使用モデルには、書き込みキャッシュが含まれます。

* **書き込みが 15% を超えています**
* **書き込みが 15% を超えています。30 秒ごとにバッキング サーバーの変更を確認します**
* **書き込みが 15% を超えています。60 秒ごとにバッキング サーバーの変更を確認します**
* **書き込みが 15% を超えると、30 秒ごとにサーバーに書き戻されます**

これらの使用モデルのみを使用して、NFS で作成されたファイルを編集します。

REST で作成されたファイルで書き込みキャッシュを使用しようとすると、ファイルの変更が失われる可能性があります。 これは、ファイルの編集内容をすぐにストレージ コンテナーに保存する作業がキャッシュで試行されないためです。

REST で作成されたファイルへの書き込みをキャッシュしようとすると、データがどのように危険にさらされるかを以下に示します。

1. キャッシュではクライアントからの編集を受け入れ、各変更について成功メッセージを返します。
1. キャッシュでは変更されたファイルをそのストレージに保持し、追加の変更を待機します。
1. しばらく時間が経つと、変更されたファイルのバックエンド コンテナーへの保存がキャッシュで試行されます。 この時点で、NFS を使用して、REST で作成されたファイルに書き込もうとしているため、エラー メッセージが表示されます。

   変更が受け入れられなかったことをクライアント コンピューターに通知するには遅すぎて、キャッシュでは元のファイルを更新することができません。 そのため、クライアントからの変更は失われます。

### <a name="read-caching-scenarios"></a>読み取りキャッシュのシナリオ

読み取りキャッシュのシナリオは、NFS または Azure Blob REST API で作成されたファイルに適しています。

これらの使用モデルでは読み取りキャッシュのみを使用します。

* **負荷が高く、頻度の低い書き込みを読み取ります**
* **クライアントでキャッシュをバイパスして、NFS ターゲットに書き込みます**
* **読み取りの負荷が高く、バッキング サーバーの確認が 3 時間ごとに行われます**

これらの使用モデルは、REST API または NFS で作成されたファイルで使用できます。 それでもクライアントからバックエンド コンテナーに送信された NFS 書き込みは失敗しますが、すぐに失敗してクライアントにエラー メッセージが返されます。

読み取りキャッシュ ワークフローには、キャッシュされない限り、ファイルの変更が引き続き含まれる場合があります。 たとえば、クライアントでコンテナーのファイルにアクセスしても、変更内容を新しいファイルとして書き戻す場合や、変更されたファイルを別の場所に保存する場合があります。

## <a name="recognize-network-lock-manager-nlm-limitations"></a>ネットワーク ロック マネージャー (NLM) の制限を認識する

NFS 対応 BLOB コンテナーでは、ネットワーク ロック マネージャー (NLM) はサポートされません。これは、ファイルが競合しないようにするために一般的に使用される NFS プロトコルです。

NFS ワークフローがもともとハードウェア ストレージ システム用に作成されている場合、クライアント アプリケーションに NLM 要求が含まれる可能性があります。 プロセスを NFS 対応 BLOB ストレージに移動するときにこの制限を回避するには、クライアントでキャッシュをマウントするときに NLM を必ず無効にするようにします。

NLM を無効にするには、クライアントの ``mount`` コマンドで ``-o nolock`` オプションを使用します。 このオプションを使用すると、クライアントで NLM ロックを要求し、応答でエラーを受信できなくなります。

いくつかの状況では、Azure HPC Cache 自体でクライアントからの NLM 要求を確認します。 **読み取り負荷が高く、書き込みの頻度が低い** というキャッシュ使用モデルで、バックエンド ストレージの代わりに NLM 要求を確認します。 このシステムにより、クライアントでのエラーを防ぐことはできますが、実際には ADLS-NFS コンテナーのロックは作成されません。

ADLS-NFS ストレージ ターゲットの使用モデルと **読み取り負荷が高く、書き込みの頻度が低い** を切り替える場合は、``nolock`` オプションを使用してすべてのクライアントを再マウントする必要があります。

NLM、HPC Cache、および使用モデルの詳細については、「[キャッシュ使用モデルについて](cache-usage-models.md#know-when-to-remount-clients-for-nlm)」を参照してください。

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>HPC Cache を使用して NFS 対応のコンテナーへの書き込みを効率化する

Azure HPC Cache は、ADLS-NFS ストレージ ターゲットへの変更の書き込みを含むワークロードのパフォーマンスを向上させるのに役立つ場合があります。

> [!NOTE]
> Azure HPC Cache を使ってファイルを変更する場合は、NFS を使用して ADLS-NFS ストレージ コンテナーを設定する必要があります。

NFS 対応 BLOB の「[パフォーマンスに関する考慮事項」の記事](../storage/blobs/network-file-system-protocol-support-performance.md)に概説されている制限事項の 1 つとして、ADLS-NFS ストレージが既存のファイルを上書きする場合にはあまり効率的ではないというものがあります。 NFS でマウントされた BLOB ストレージで Azure HPC Cache を使用する場合、クライアントでアクティブなファイルを変更するときに、キャッシュで間欠的な書き換えが処理されます。 バックエンド コンテナーへのファイル書き込みの待機時間は、クライアントには表示されません。

前述の「[NFS プロトコルを使用してデータを事前に読み込む](#preload-data-with-nfs-protocol)」で説明した制限事項に留意してください。

## <a name="next-steps"></a>次の手順

* [ADLS-NFS ストレージ ターゲットの前提条件](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements-preview)を確認する
* [NFS 対応の BLOB ストレージ アカウント](../storage/blobs/network-file-system-protocol-support-how-to.md)を作成する
