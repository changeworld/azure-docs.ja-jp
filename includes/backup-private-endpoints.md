---
author: v-amallick
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: v-amallick
ms.openlocfilehash: d20ed4d39921f8000f77f947c4372bd8b10ca642
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294168"
---
[プライベート エンドポイント](../articles/private-link/private-endpoint-overview.md)を使用して、仮想ネットワーク内のサーバーから Recovery Services コンテナーにデータを安全にバックアップできるようになりました。 プライベート エンドポイントでは、お使いのコンテナーの VNET アドレス空間からの IP アドレスが使用されます。 仮想ネットワーク内のリソースとコンテナー間のネットワーク トラフィックは、仮想ネットワークと Microsoft のバックボーン ネットワーク上のプライベート リンクを経由します。 これにより、パブリック インターネットへの露出が排除されます。 プライベート エンドポイントを使用して、お使いの Azure VM 内で実行される SQL データベースと SAP HANA データベースのバックアップと復元を行うことができます。 また、MARS エージェントを使用して、オンプレミス サーバーでも使用できます。

Azure VM のバックアップにはインターネット接続は不要であるため、ネットワークの分離を可能にするためのプライベート エンドポイントは必要ありません。

[こちら](../articles/backup/private-endpoints.md)で、Azure Backup のプライベート エンドポイントの詳細をご覧ください。