---
title: Azure BareMetal インフラストラクチャの用語を把握する
description: Azure BareMetal インフラストラクチャの用語を把握します。
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: fd7a39854c86f728ef152f8e7d858157e1ad26f4
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861909"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>BareMetal インフラストラクチャの用語を把握する

この記事では、BareMetal に関する重要な用語をいくつか取り上げます。

- **リビジョン**:リビジョン 3 (Rev 3) と呼ばれる元のスタンプ リビジョンと、BareMetal インスタンス スタンプ用の 2 つの異なるスタンプ リビジョンがあります。 各スタンプは、アーキテクチャと Azure 仮想マシン ホストとの近接性の点で異なります。
    - **リビジョン 4** (Rev 4): Azure 仮想マシン (VM) のホストにより近い場所を提供し、Azure VM と BareMetal インスタンス ユニット間のネットワーク待機時間を短縮するより新しい設計です。 
    - **リビジョン 4.2** (Rev 4.2): 既存の Rev 4 アーキテクチャを使用した、最新のブランド変更された BareMetal インフラストラクチャです。 Rev 4 では、Azure 仮想マシン (VM) ホストにより近接します。 これにより、Azure VM と Rev 4 のスタンプまたは行にデプロイされた BareMetal インスタンス ユニットの間のネットワーク待ち時間が大幅に改善されます。 Azure portal を使用して BareMetal インスタンスにアクセスし、管理することができます。    

- **スタンプ**:BareMetal インスタンスの Microsoft 内部デプロイ サイズを定義します。 インスタンス ユニットをデプロイするには、コンピューティング、ネットワーク、およびストレージ ラックで構成されている BareMetal インスタンス スタンプをデータセンターの場所に事前にデプロイする必要があります。 このようなデプロイは BareMetal Instance スタンプと呼ばれるか、Revision 4.2 からになります。

- **Tenant**: BareMetal インスタンス スタンプにデプロイされたお客様は、"*テナント*" に分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 別のテナントに割り当てられているストレージ ユニットやコンピューティング ユニットは、BareMetal インスタンス スタンプ レベルで相互に認識したり通信したりすることはできません。 同じお客様が別々のテナントにデプロイを置くこともありますが、 その場合でも BareMetal インスタンス スタンプ レベルでテナント間が通信することはありません。

## <a name="next-steps"></a>次のステップ
[BareMetal インフラストラクチャ](workloads/sap/baremetal-overview-architecture.md)について、または [BareMetal インスタンス ユニットを識別して操作する](workloads/sap/baremetal-infrastructure-portal.md)方法についてさらに詳しく学習します。 