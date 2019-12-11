---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 8106b1a77f11874b1b13f41bfc9e62ea800afc98
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361263"
---
### <a name="database-tier"></a>データベース層

データベース層には、アプリケーションのデータベース インスタンスが存在します。 データベースには、Oracle DB、Oracle RAC、Oracle Exadata Database システムのいずれかを選択できます。 

Oracle DB を選択した場合、Azure Marketplace で提供されている Oracle DB イメージを使用してデータベース インスタンスを Azure にデプロイできます。 または、Azure と OCI 間の相互接続を使用し、OCI 上に PaaS モデルで Oracle DB をデプロイすることもできます。

Oracle RAC の場合は、Azure CloudSimple に IaaS モデルで、または OCI に PaaS モデルで Oracle RAC をデプロイできます。 2 ノードの RAC システムを使用することをお勧めします。 

最後に、Exadata システムの場合は、OCI 相互接続を使用して、Exadata システムを OCI にデプロイします。 前出のアーキテクチャ図は、2 つのサブネットにまたがって OCI にデプロイされた Exadata システムを示しています。

運用のシナリオでは、2 つの可用性ゾーン (Azure にデプロイする場合) または 2 つの可用性ドメイン (OCI の場合) にまたがってデータベースの複数インスタンスをデプロイします。 プライマリ データベースとスタンバイ データベースの同期には、Oracle Active Data Guard を使用します。

データベース層は、中間層からの要求のみを受け取ります。 中間層からの要求と、管理を目的とした踏み台サーバーからの要求のみを、それぞれポート 1521 とポート 22 で許可するネットワーク セキュリティ グループ (OCI にデータベースをデプロイする場合はセキュリティ リスト) を設定するようお勧めします。

OCI にデプロイされたデータベースについては、FastConnect 回線に接続された動的ルーティング ゲートウェイ (DRG) を使用して、別個の仮想クラウド ネットワークを設定する必要があります。