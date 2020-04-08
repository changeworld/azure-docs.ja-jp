---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: a3bae0ce3e6ebcf64936d0ca4af4fb702e5ea404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137026"
---
[プライベート エンドポイント](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)を使用して、仮想ネットワーク内のサーバーから Recovery Services コンテナーにデータを安全にバックアップできるようになりました。 プライベート エンドポイントでは、お使いのコンテナーの VNET アドレス空間からの IP アドレスが使用されます。 仮想ネットワーク内のリソースとコンテナー間のネットワーク トラフィックは、仮想ネットワークと Microsoft のバックボーン ネットワーク上のプライベート リンクを経由します。 これにより、パブリック インターネットへの露出が排除されます。 プライベート エンドポイントを使用して、お使いの Azure VM 内で実行される SQL データベースと SAP HANA データベースのバックアップと復元を行うことができます。 また、MARS エージェントを使用して、オンプレミス サーバーでも使用できます。

Azure VM のバックアップにはインターネット接続は不要であるため、ネットワークの分離を可能にするためのプライベート エンドポイントは必要ありません。

>[!NOTE]
> この機能は、現在先行使用できます。 Azure Backup でのプライベート エンドポイントの使用に関心がおありの場合は、[こちらのアンケート](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)に記入して azbackupnetsec@microsoft.com に電子メールで送信してください。 この機能を使用する能力は、Azure Backup サービスによる承認の対象です。
