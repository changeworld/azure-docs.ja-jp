---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: fd7124ad83a446d7dde39a836c337a97b12a0a0a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563229"
---
[プライベート エンドポイント](../articles/private-link/private-endpoint-overview.md)を使用して、仮想ネットワーク内のサーバーから Recovery Services コンテナーにデータを安全にバックアップできるようになりました。 プライベート エンドポイントでは、お使いのコンテナーの VNET アドレス空間からの IP アドレスが使用されます。 仮想ネットワーク内のリソースとコンテナー間のネットワーク トラフィックは、仮想ネットワークと Microsoft のバックボーン ネットワーク上のプライベート リンクを経由します。 これにより、パブリック インターネットへの露出が排除されます。 プライベート エンドポイントを使用して、お使いの Azure VM 内で実行される SQL データベースと SAP HANA データベースのバックアップと復元を行うことができます。 また、MARS エージェントを使用して、オンプレミス サーバーでも使用できます。

Azure VM のバックアップにはインターネット接続は不要であるため、ネットワークの分離を可能にするためのプライベート エンドポイントは必要ありません。

[こちら](../articles/backup/private-endpoints.md)で、Azure Backup のプライベート エンドポイントの詳細をご覧ください。