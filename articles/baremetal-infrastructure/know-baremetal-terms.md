---
title: Azure BareMetal インフラストラクチャの用語を把握する
description: Azure BareMetal インフラストラクチャの用語を把握します。
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: a4bf8b24bc412215f595e12128c0a63f40e8fbfd
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767107"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>BareMetal インフラストラクチャの用語を把握する

この記事では、BareMetal インフラストラクチャに関連するいくつかの重要な用語について説明します。

- **リビジョン**:BareMetal インフラストラクチャ (HANA L インスタンス) スタンプには、2 つの異なるスタンプ リビジョンがあります。 これらのリビジョンは、アーキテクチャと、Azure 仮想マシン ホストとの近接性の点で異なります。
    - "リビジョン 3" (Rev 3): 2016 年の中頃にデプロイされた元のデザインです。
    - "リビジョン 4.2" (Rev 4.2): Azure VM と HANA L インスタンスの間のネットワーク待機時間が非常に短い、Azure 仮想マシンのホストにより近い近接性を提供する新しい設計です。 Azure portal 内のリソースは "BareMetal インフラストラクチャ" と呼ばれ、お客様は、Azure portal から BareMetal インスタンスのリソースにアクセスできます。

- **スタンプ**: BareMetal インスタンスの Microsoft 内部デプロイ サイズを定義します。 インスタンスをデプロイするには、コンピューティング、ネットワーク、およびストレージ ラックで構成されている BareMetal インスタンス スタンプをデータセンターの場所に事前にデプロイする必要があります。 このようなデプロイは BareMetal インスタンス スタンプと呼ばれます。

- **テナント**: BareMetal インスタンス スタンプをデプロイしている顧客は、"*テナント*" として分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 別のテナントに割り当てられているストレージ ユニットやコンピューティング ユニットは、BareMetal インスタンス スタンプ レベルで相互に認識したり通信したりすることはできません。 同じお客様が別々のテナントにデプロイを置くこともありますが、 その場合でも BareMetal インスタンス スタンプ レベルでテナント間が通信することはありません。

## <a name="next-steps"></a>次のステップ

BareMetal インフラストラクチャの詳細を確認します。

> [!div class="nextstepaction"]
> [BareMetal インフラストラクチャ](concepts-baremetal-infrastructure-overview.md)
