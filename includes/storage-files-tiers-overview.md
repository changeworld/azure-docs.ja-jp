---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4e5c59a5ce7c0d743a574309bb1b9276ce80bea
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597830"
---
Azure Files では、Premium と Standard の 2 つの異なるストレージのサービス レベルが提供されており、お客様のシナリオのパフォーマンスと価格の要件に合わせて共有を調整できます。

- **Premium ファイル共有**: Premium ファイル共有は、ソリッドステート ドライブ (SSD) が基になっており、**FileStorage ストレージ アカウント**の種類にデプロイされます。 Premium ファイル共有は、IO 集中型ワークロードの場合、ほとんどの IO 操作に対して 1 桁台のミリ秒以内で一貫したハイ パフォーマンスと低待機時間を提供しています。 そのため、Premium ファイル共有は、データベース、Web サイトのホスティング、開発環境など、幅広い種類のワークロードに適しています。 Premium ファイル共有は、プロビジョニングされる課金モデルでのみ使用できます。 Premium ファイル共有のプロビジョニング済み課金モデルについて詳しくは、「[Premium ファイル共有のプロビジョニングについて](../articles/storage/files/storage-files-planning.md#understanding-provisioning-for-premium-file-shares)」をご覧ください。
- **Standard ファイル共有**: Standard ファイル共有は、ハード ディスク ドライブ (HDD) が基になっており、**汎用バージョン 2 (GPv2) ストレージ アカウント**の種類にデプロイされます。 Standard ファイル共有は、汎用のファイル共有や開発/テスト環境などのパフォーマンスの変動の影響を受けにくい IO ワークロードに対して信頼性の高いパフォーマンスを提供します。 Standard ファイル共有は、従量課金制の課金モデルでのみ利用できます。