---
title: Azure BareMetal インフラストラクチャの用語を把握する
description: Azure BareMetal インフラストラクチャの用語を把握します。
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725460"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>BareMetal インフラストラクチャの用語を把握する

この記事では、BareMetal インフラストラクチャに関連するいくつかの重要な用語について説明します。

- **リビジョン**:BareMetal インフラストラクチャ (HANA L インスタンス) スタンプには、2 つの異なるスタンプ リビジョンがあります。 これらは、アーキテクチャと Azure 仮想マシン ホストとの近接性の点で異なります。
    - "リビジョン 3" (Rev 3): 2016 年の中頃にデプロイされた元のデザインです。
    - "リビジョン 4.2" (Rev 4.2): Azure VM と HANA L インスタンスの間のネットワーク待機時間が非常に短い、Azure 仮想マシンのホストにより近い近接性を提供する新しい設計です。 Azure portal 内のリソースは "BareMetal インフラストラクチャ" と呼ばれ、お客様は、Azure portal から BareMetal インスタンスのリソースにアクセスできます。

- **スタンプ**: BareMetal インスタンスの Microsoft 内部デプロイ サイズを定義します。 インスタンスをデプロイするには、コンピューティング、ネットワーク、およびストレージ ラックで構成されている BareMetal インスタンス スタンプをデータセンターの場所に事前にデプロイする必要があります。 このようなデプロイは BareMetal インスタンス スタンプと呼ばれます。

- **テナント**: BareMetal インスタンス スタンプをデプロイしている顧客は、"*テナント*" として分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 別のテナントに割り当てられているストレージ ユニットやコンピューティング ユニットは、BareMetal インスタンス スタンプ レベルで相互に認識したり通信したりすることはできません。 同じお客様が別々のテナントにデプロイを置くこともありますが、 その場合でも BareMetal インスタンス スタンプ レベルでテナント間が通信することはありません。

## <a name="next-steps"></a>次のステップ

これで、BareMetal インフラストラクチャの重要な用語を知ったので、次の事項の学習に進むことができます。
- [Baremetal インフラストラクチャ](concepts-baremetal-infrastructure-overview.md)の詳細。
- [Azure で BareMetal インフラストラクチャ インスタンスを接続する](connect-baremetal-infrastructure.md)方法。

