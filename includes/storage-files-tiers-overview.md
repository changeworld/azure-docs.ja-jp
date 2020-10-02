---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e75cb7d13fb74d32191ab7f076d73ad66976503d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90606369"
---
Azure Files では、Premium、トランザクション最適化、ホット、クールの 4 つの異なるストレージのサービス レベルが提供されており、お客様のシナリオのパフォーマンスと価格の要件に合わせて共有を調整できます。

- **Premium**:Premium ファイル共有は、ソリッドステート ドライブ (SSD) が基になっており、**FileStorage ストレージ アカウント**の種類にデプロイされます。 Premium ファイル共有は、IO 集中型ワークロードの場合、ほとんどの IO 操作に対して 1 桁台のミリ秒以内で一貫したハイ パフォーマンスと低待機時間を提供しています。 Premium ファイル共有は、データベース、Web サイトのホスティング、開発環境など、幅広い種類のワークロードに適しています。 Premium ファイル共有は、サーバー メッセージ ブロック (SMB) プロトコルとネットワーク ファイル システム (NFS) プロトコルの両方で使用できます。
- **トランザクション最適化**:トランザクション最適化ファイル共有は、Premium ファイル共有が提供する待機時間を必要としない、トランザクション負荷の高いワークロードを可能にします。 トランザクション最適化ファイル共有は、ハード ディスク ドライブ (HDD) によってサポートされた標準ストレージ ハードウェア上で提供され、**汎用バージョン 2 (GPv2) ストレージ アカウント**の種類にデプロイされます。 トランザクション最適化は、従来は "Standard" と呼ばれていましたが、これはサービス レベル自体ではなく、ストレージ メディアの種類を指しています (ホットおよびクールも、標準ストレージ ハードウェア上にあるため、"Standard" サービス レベルです)。
- **Hot**:ホット ファイル共有は、チーム共有や Azure File Sync などの汎用ファイル共有シナリオ用に最適化されたストレージを提供します。ホット ファイル共有は、HDD によってサポートされた標準ストレージ ハードウェア上で提供され、**汎用バージョン 2 (GPv2) ストレージ アカウント**の種類にデプロイされます。
- **Cool**:クール ファイル共有は、オンライン アーカイブ ストレージのシナリオ向けに最適化された、コスト効率に優れたストレージを提供します。 Azure File Sync は、より低いチャーンのワークロードに適している場合もあります。 クール ファイル共有は、HDD によってサポートされた標準ストレージ ハードウェア上で提供され、**汎用バージョン 2 (GPv2) ストレージ アカウント**の種類にデプロイされます。

Premium ファイル共有は、プロビジョニングされる課金モデルでのみ使用できます。 Premium ファイル共有のプロビジョニング済み課金モデルについて詳しくは、「[Premium ファイル共有のプロビジョニングについて](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)」をご覧ください。 トランザクション最適化、ホット、およびクール ファイル共有などの Standard ファイル共有は、従量課金制で利用できます。

ホットおよびクール ファイル共有は、すべての Azure パブリック リージョンと Azure Government リージョンで利用できます。 トランザクション最適化ファイル共有は、Azure China および Azure Germany リージョンを含むすべての Azure リージョンで利用できます。

ホットまたはクール ファイル共有をデプロイするには、[ホットまたはクール ファイル共有の作成](../articles/storage/files/storage-how-to-create-file-share.md#create-a-hot-or-cool-file-share)に関する記事を参照してください。 