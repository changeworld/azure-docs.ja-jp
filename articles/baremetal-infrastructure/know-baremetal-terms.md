---
title: Azure BareMetal インフラストラクチャの用語を把握する
description: Azure BareMetal インフラストラクチャの用語を把握します。
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 53a601cc4556198479d8ca5d7495942d4dc2762c
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580142"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>BareMetal インフラストラクチャの用語を把握する

この記事では、BareMetal インフラストラクチャに関連するいくつかの重要な用語について説明します。

- **リビジョン**: リビジョン 3 (Rev 3) と呼ばれる元のスタンプ リビジョンと、BareMetal インスタンス スタンプ用の 2 つの追加のスタンプ リビジョンがあります。 各スタンプは、アーキテクチャと Azure 仮想マシン ホストとの近接性の点で異なります。
    - **リビジョン 4** (Rev 4): Azure 仮想マシン (VM) のホストにより近い場所を提供し、Azure VM と SAP HANA インスタンス間のネットワーク待機時間を短縮するより新しい設計です。 
    - **リビジョン 4.2** (Rev 4.2): 既存の Rev 4 アーキテクチャを使用した、最新のブランド変更された BareMetal インフラストラクチャです。 Rev 4 では、Azure 仮想マシン (VM) ホストにより近接します。 これにより、Azure VM と Rev 4 のスタンプまたは行にデプロイされた BareMetal インスタンスの間のネットワーク待ち時間が大幅に改善されます。 Azure portal を使用して BareMetal インスタンスにアクセスし、管理することができます。    

- **スタンプ**: BareMetal インスタンスの Microsoft 内部デプロイ サイズを定義します。 インスタンスをデプロイするには、コンピューティング、ネットワーク、およびストレージ ラックで構成されている BareMetal インスタンス スタンプをデータセンターの場所に事前にデプロイする必要があります。 このようなデプロイは BareMetal インスタンス スタンプと呼ばれます。

- **テナント**: BareMetal インスタンス スタンプをデプロイしている顧客は、"*テナント*" として分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 別のテナントに割り当てられているストレージ ユニットやコンピューティング ユニットは、BareMetal インスタンス スタンプ レベルで相互に認識したり通信したりすることはできません。 同じお客様が別々のテナントにデプロイを置くこともありますが、 その場合でも BareMetal インスタンス スタンプ レベルでテナント間が通信することはありません。

## <a name="next-steps"></a>次のステップ
これで、BareMetal インフラストラクチャの重要な用語を知ったので、次の事項の学習に進むことができます。
- [Baremetal インフラストラクチャ](concepts-baremetal-infrastructure-overview.md)の詳細。
- [Azure で BareMetal インフラストラクチャ インスタンスを接続する](connect-baremetal-infrastructure.md)方法。

