---
author: dcurwin
ms.service: backup
ms.topic: include
ms.date: 03/12/2020
ms.author: dacurwin
ms.openlocfilehash: 024f70a21b36bffa662ba6f568adc8efbd0a909d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81539323"
---
[プライベート エンドポイント](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)を使用して、仮想ネットワーク内のサーバーから Recovery Services コンテナーにデータを安全にバックアップできるようになりました。 プライベート エンドポイントでは、お使いのコンテナーの VNET アドレス空間からの IP アドレスが使用されます。 仮想ネットワーク内のリソースとコンテナー間のネットワーク トラフィックは、仮想ネットワークと Microsoft のバックボーン ネットワーク上のプライベート リンクを経由します。 これにより、パブリック インターネットへの露出が排除されます。 プライベート エンドポイントを使用して、お使いの Azure VM 内で実行される SQL データベースと SAP HANA データベースのバックアップと復元を行うことができます。 また、MARS エージェントを使用して、オンプレミス サーバーでも使用できます。

Azure VM のバックアップにはインターネット接続は不要であるため、ネットワークの分離を可能にするためのプライベート エンドポイントは必要ありません。

>[!NOTE]
> この機能の利用は、現在、制限されており、米国中西部、米国中南部、米国西部2、米国東部で利用できます (他の Azure リージョンでも今後、利用できるようになります)。 Azure Backup でのプライベート エンドポイントの使用に関心がおありの場合は、[こちらのアンケート](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUQk5EQ1QxRzVOWDNDS1Y1Q0xLTkdLQ0U0RC4u)に記入して azbackupnetsec@microsoft.com に電子メールで送信してください。 この機能を使用する能力は、Azure Backup サービスによる承認の対象です。
