---
title: Azure HPC Cache のシナリオ
description: コンピューティング ジョブが Azure HPC Cache で適切に動作するかどうかを確認する方法について説明します
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/29/2021
ms.author: v-erkel
ms.openlocfilehash: 36e0135102fbede5505e96fb1aa255588b2f2ae2
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259904"
---
# <a name="is-your-job-a-good-fit-for-azure-hpc-cache"></a>ジョブが Azure HPC Cache に適しているかどうか

Azure HPC Cache を使用すると、さまざまな分野で、ハイ パフォーマンス コンピューティング ジョブのデータへのアクセスを高速化できます。 ただし、一部の種類のワークフローには適していません。 この記事では、HPC Cache がニーズに適したオプションかどうかを判断する方法のガイドラインを示します。

また、「[概要](hpc-cache-overview.md)」の記事では、Azure HPC Cache をいつ使用するかの概要とユース ケースのいくつかの例を示しています。

また、プレビュー段階にある、[NFS でマウントされた BLOB ストレージ](../storage/blobs/network-file-system-protocol-support.md)を効果的に使用する方法についても、[この記事](nfs-blob-considerations.md)をお読みください。

## <a name="nfs-version-30-applications"></a>NFS バージョン 3.0 アプリケーション

Azure HPC Cache では、NFS 3.0 クライアントのみがサポートされます。

## <a name="high-read-to-write-ratio"></a>高い読み取り/書き込みの比率

コンピューティング クライアントが書き込みよりも読み取りを行うワークロードは、通常、キャッシュの候補として適しています。 たとえば、読み取り/書き込みの比率が 80/20 または 70/30 の場合、Azure HPC Cache を使用すると、頻繁に要求されるファイルをリモート ストレージから何度も取り込むのではなく、キャッシュから提供できます。

ファイルを取り込んで初めてキャッシュに格納すると、ストレージに対して直接通常のクライアント要求を行うよりも待機時間がわずかに増加します。そのため、クライアントが次に同じファイルを要求したときに効率が向上します。 これは、特に大きなファイルの場合に当てはまります。 各クライアント要求が一意である場合、HPC Cache の影響は限定的です。 しかし、ファイルが大きいほど、最初のアクセス後に時間の経過と共にパフォーマンスが向上します。

## <a name="file-based-analytic-workload"></a>ファイルベースの分析ワークロード

Azure HPC Cache は、特にコンピューティング クライアントが Azure 仮想マシンである場合に、ファイルベースのデータを使用し、多数のコンピューティング クライアントで実行されるパイプラインに最適です。 長いファイル アクセス時間が原因で発生する低速または不整合なパフォーマンスを解決するのに役立ちます。

## <a name="remote-data-access"></a>リモート データ アクセス

Azure HPC Cache は、コンピューティング リソースに近い場所に移動できないリモート データにワークロードでアクセスする必要がある場合に、待機時間を短縮するのに役立ちます。 たとえば、レコードが WAN 環境の末端にある場合、別の Azure リージョンにある場合、または顧客データ センターにある場合です。 (これは "ファイル バースト" とも呼ばれます)。

## <a name="heavy-request-load"></a>高い要求負荷

多数のクライアントが同時にソースからデータを要求した場合、Azure HPC Cache を使用すると、ファイル アクセスを高速化できます。 たとえば、ハイ パフォーマンス コンピューティング クラスターで使用する場合、Azure HPC Cache を使用すると、キャッシュを介した多数の同時要求に対応するスケーラビリティを実現できます。

## <a name="compute-resources-are-located-in-azure"></a>コンピューティング リソースは Azure に配置されています

Azure 仮想マシンは、ハイ パフォーマンス コンピューティング ワークロードに対するスケーラブルでコスト効果の高い解決策です。 Azure HPC Cache を使用すると、特に元のデータがリモート システムに格納されている場合に、必要な情報を近くの場所に配置することができます。

顧客が現在のパイプラインを "そのまま" Azure 仮想マシンで実行することを望んでいる場合、Azure HPC Cache では、スケーラビリティを実現するために POSIX ベースの共有ストレージ (またはキャッシュ) ソリューションを提供できます。

Azure HPC Cache を使用すれば、Azure Blob Storage をネイティブに呼び出すために作業パイプラインを再設計する必要はありません。 元のシステム上のデータにアクセスするか、HPC Cache を使用して新しい BLOB コンテナーに移動することができます。

## <a name="next-steps"></a>次のステップ

* キャッシュを計画および構成する方法の詳細については、「[概要](hpc-cache-overview.md)」と「[前提条件](hpc-cache-prerequisites.md)」の記事を参照してください
* Azure HPC Cache で [NFS 対応の Blob ストレージ](nfs-blob-considerations.md) (プレビュー) を使用する場合の考慮事項を確認する
