---
title: Azure BareMetal インフラストラクチャの用語を把握する
description: Azure BareMetal インフラストラクチャの用語を把握します。
ms.topic: conceptual
ms.date: 12/31/2020
ms.openlocfilehash: f11bc4cfdd463623010ed7b6677235344ec7cd62
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2020
ms.locfileid: "97829060"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>BareMetal インフラストラクチャの用語を把握する

この記事では、BareMetal に関する重要な用語をいくつか取り上げます。

- **リビジョン**:BareMetal インスタンス スタンプには、2 つの異なるスタンプ リビジョンがあります。 各バージョンは、アーキテクチャと Azure 仮想マシン ホストとの近接性の点で異なります。
    - **リビジョン 3** (Rev 3): 元のデザインです。
    - **リビジョン 4** (Rev 4): Azure 仮想マシン (VM) のホストにより近い場所を提供し、Azure VM と BareMetal インスタンス ユニット間のネットワーク待機時間を短縮する新しい設計です。 
    - **リビジョン 4.2** (Rev 4.2): 既存の Rev 4 アーキテクチャを使用した、最新のブランド変更された BareMetal インフラストラクチャです。 Azure portal を使用して BareMetal インスタンスにアクセスし、管理できます。  

- **スタンプ**:BareMetal インスタンスの Microsoft 内部デプロイ サイズを定義します。 インスタンス ユニットをデプロイするは、コンピューティング、ネットワーク、およびストレージ ラックで構成されている BareMetal インスタンス スタンプをデータセンターの場所に事前にデプロイする必要があります。 このようなデプロイは BareMetal Instance スタンプと呼ばれるか、Revision 4.2 からになります。

- **Tenant**: BareMetal インスタンス スタンプにデプロイされたお客様は、"*テナント*" に分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 別のテナントに割り当てられているストレージ ユニットやコンピューティング ユニットは、BareMetal インスタンス スタンプ レベルで相互に認識したり通信したりすることはできません。 同じお客様が別々のテナントにデプロイを置くこともありますが、 その場合でも BareMetal インスタンス スタンプ レベルでテナント間が通信することはありません。

## <a name="next-steps"></a>次のステップ
[Azure portal](workloads/sap/baremetal-infrastructure-portal.md) を使用して BareMetal インスタンス ユニットを識別および操作する方法について説明します。


 