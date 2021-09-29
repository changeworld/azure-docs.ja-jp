---
title: Azure Files、Blob Storage、Azure NetApp Files への NFS のアクセスを比較する
description: Azure Files、Azure Blob Storage、Azure NetApp Files への NFS のアクセスを比較します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: rogarana
ms.openlocfilehash: 3dcc0a93473e5f0b14f8921114172dbf6506d8aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636745"
---
# <a name="compare-access-to-azure-files-blob-storage-and-azure-netapp-files-with-nfs"></a>NFS を使用した Azure Files、Blob Storage、および Azure NetApp Files へのアクセスを比較する

この記事では、[ネットワーク ファイル システム (NFS)](https://en.wikipedia.org/wiki/Network_File_System) プロトコルを介してアクセスした場合の、各オファリングの比較について説明します。 他の方法でアクセスする場合、この比較は適用されません。

より全般的な比較については、Azure Blob Storage と Azure Files の比較の場合は[この記事](storage-introduction.md)を、Azure Files と Azure NetApp Files の比較の場合は[この記事](../files/storage-files-netapp-comparison.md)を参照してください。

## <a name="comparison"></a>比較

|カテゴリ  |Azure Blob Storage  |Azure Files  |Azure NetApp Files  |
|---------|---------|---------|---------|
|ユース ケース     |Blob Storage が最適なのは、データが一度取り込まれ、その後の変更に加えられる変更が最小限である、読み取りの多い大規模なシーケンシャル アクセスのワークロードです。<br></br>メンテナンスがほとんど必要ない場合、Blob Storage は総保有コストを最も低くなります。<br></br>シナリオ例として、大規模な分析データ、スループットを重視したハイパフォーマンス コンピューティング、バックアップとアーカイブ、自動運転、メディア レンダリング、ゲノム シーケンスなどがあります。         |Azure Files は、ランダム アクセス ワークロードに最適な高可用性サービスです。<br></br>NFS 共有の場合、Azure Files は POSIX ファイル システムを完全にサポートしているので、VM ベースのプラットフォームに加えて、CSI ドライバーが組み込まれた Azure Container Instance (ACI) や Azure Kubernetes Service (AKS) などのコンテナー プラットフォームからも簡単に使用できます。<br></br>シナリオ例として、共有ファイル、データベース、ホーム ディレクトリ、従来のアプリケーション、ERP、CMS、高度な管理を必要としない NAS 移行、スケールアウト ファイル ストレージを必要とするカスタム アプリケーションなどがあります。         |高度な管理機能を備えた、NetApp を搭載したクラウド内のフル マネージド ファイル サービスです。<br></br>NetApp Files は、ランダム アクセスを必要とするワークロードに適しており、幅広いプロトコルのサポートとデータ保護機能を備えています。<br></br>シナリオ例として、豊富な管理機能を必要とするオンプレミスのエンタープライズ NAS 移行、SAP HANA のような待機時間の影響を受けやすいワークロード、待機時間の影響を受けやすいまたは IOPS を多用するハイ パフォーマンス コンピューティング、同時マルチ プロトコル アクセスを必要とするワークロードなどがあります。         |
|使用可能なプロトコル     |NFS 3.0<br></br>REST<br></br>Data Lake Storage Gen2         |SMB<br><br>NFS 4.1 (プレビュー)<br></br> (いずれのプロトコル間にも相互運用性はありません)         |NFS 3.0 および 4.1<br></br>SMB         |
|主要な機能     | 低待機時間のワークロード向けに HPC キャッシュと統合。 <br> </br> ライフサイクル、不変の BLOB、データ フェールオーバー、メタデータ インデックスなどの統合管理。         | 高可用性のためのゾーン冗長性。 <br></br> 一貫した 1 桁のミリ秒の待機時間。 <br></br>容量に応じてスケーリングできる予測可能なパフォーマンスとコスト。         |極めて短い待機時間 (ミリ秒未満)。<br></br>SnapMirror など、クラウドの豊富な NetApp ONTAP 管理機能。<br></br>一貫したハイブリッド クラウド エクスペリエンス。         |
|パフォーマンス (ボリュームごと)     |最大 20,000 IOPS、最大 100 GiB/s のスループット。         |最大 100,000 IOPS、最大 80 GiB/s のスループット。         |最大 460,000 IOPS、最大 36 GiB/s のスループット。         |
|スケール     | 1 つのボリュームに対して最大 2 PiB。 <br></br> 1 つのファイルに対して最大 4.75 TiB。<br></br>最小容量の要件はありません。         |1 つのファイル共有に対して最大 100 TiB。<br></br>1 つのファイルに対して最大 4 TiB。<br></br>100 GiB の最小容量。         |1 つのボリュームに対して最大 100 TiB。<br></br>1 つのファイルに対して最大 16 TiB。<br></br>一貫したハイブリッド クラウド エクスペリエンス。         |
|価格     |[Azure Blob Storage の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)         |[Azure Files の料金](https://azure.microsoft.com/pricing/details/storage/files/)         |[Azure NetApp Files の価格](https://azure.microsoft.com/pricing/details/netapp/)         |

## <a name="next-steps"></a>次のステップ

- NFS を使用して BLOB ストレージにアクセスするには、「[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート (プレビュー)](../blobs/network-file-system-protocol-support.md)」を参照してください。
- NFS を使用して Azure Files にアクセスするには、「[Azure Files での NFS ファイル共有](../files/files-nfs-protocol.md)」を参照してください。
- NFS を使用して Azure NetApp Files にアクセスするには、「[クイックスタート: Azure NetApp Files を設定し、NFS ボリュームを作成する](../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)」を参照してください。
