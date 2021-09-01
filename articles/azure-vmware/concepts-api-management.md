---
title: 概念 - API Management
description: Azure VMware Solution 仮想マシン (VM) 上で実行されている API を API Management が保護する方法について説明します
ms.topic: conceptual
ms.date: 04/28/2021
ms.openlocfilehash: 14b2d81d0080ebf389c817d1c718d207aa922b0d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752109"
---
# <a name="publish-and-protect-apis-running-on-azure-vmware-solution-vms"></a>Azure VMware Solution VM 上で実行されている API を発行および保護する

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) を使用すると、外部または内部のコンシューマーに安全に発行できます。  Developer (開発) および Premium (運用) の SKU のみで、Azure Virtual Network 統合を使用して、Azure VMware Solution ワークロードで実行されている API を発行することができます。 加えて、どちらの SKU でも、API Management サービスとバックエンド間の接続を確立することができます。

API Management 構成は、Azure VMware Solution の仮想マシン (VM) およびオンプレミスで実行されるバックエンド サービスの場合と同じです。 加えて、Azure VMware Solution でバックエンド サーバーが NSX Load Balancer の後ろに配置されている場合、どちらのデプロイでも API Management によってロード バランサーの仮想 IP がバックエンド エンドポイントとして構成されます。

## <a name="external-deployment"></a>外部デプロイ

外部デプロイは、パブリック エンドポイントを使用する外部ユーザーによって使用される API を発行します。 開発者と DevOps エンジニアは、Azure portal または PowerShell と API Management 開発者ポータルを使用して API を管理できます。

外部配置ダイアグラムには、プロセス全体と関連するアクター (上部に表示) が表示されます。 アクターは次のとおりです。

- **Administrator(s):** 管理者または DevOps チームを表します。Azure portal および PowerShell や Azure DevOps などの自動化メカニズムを通じて、Azure VMware Solution を管理します。

- **Users:** 公開されている API のコンシューマーを表し、API を使用するユーザーとサービスの両方を表します。

トラフィック フローは API Management インスタンスを通過します。これにより、バックエンド サービスが抽象化され、ハブ仮想ネットワークに接続されます。 ExpressRoute ゲートウェイによってトラフィックが ExpressRoute Global Reach チャネルにルーティングされ、その着信トラフィックをさまざまなバックエンド サービス インスタンスに分散する NSX Load Balancer に到達します。

API Management には Azure パブリック API があり、Azure DDOS Protection サービスをアクティブ化することをお勧めします。 

:::image type="content" source="media/api-management/api-management-external-deployment.png" alt-text="Azure VMware Solution 向けの外部 API Management デプロイを示す図" border="false":::


## <a name="internal-deployment"></a>内部デプロイ

内部デプロイは、内部ユーザーまたはシステムによって使用される API を発行します。 DevOps チームおよび API 開発者は、外部デプロイと同じ管理ツールと開発者ポータルを使用します。

内部デプロイ用の [Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) を使用して、API 向けにパブリックのセキュリティ保護されたエンドポイントを作成します。  ゲートウェイの機能によって、さまざまなシナリオを可能にするハイブリッド デプロイを作成します。  

* 内部と外部のコンシューマーの両方における消費用に同一の API Management リソースを使用する。

* 単一の API Management リソースを持ち、定義した API の一部を外部コンシューマーが利用できるようにする。

* パブリック インターネットから API Management へのアクセスのオン/オフを切り替える簡単な方法を提供する。

次の配置ダイアグラムは、内部または外部のコンシューマーを示しています。それぞれが同じ API または異なる API にアクセスします。

内部デプロイでは、API は同じ API Management インスタンスに公開されます。 API Management の前に、Azure Web Application Firewall (WAF) 機能がアクティブになっている Application Gateway がデプロイされています。 トラフィックをフィルター処理するための一連の HTTP リスナーとルールを備えた Application Gateway もデプロイされており、これは Azure VMware Solution で実行されているバックエンド サービスのサブセットのみを公開します。


* 内部トラフィックは、直接またはトラフィック規則を介して、ExpressRoute ゲートウェイ経由で Azure Firewall の後に API Management にルーティングされます。   

* 外部トラフィックは、API Management の外部保護レイヤーを使用する Application Gateway を通じて Azure に入ります。


:::image type="content" source="media/api-management/api-management-internal-deployment.png" alt-text="Azure VMware Solution 向けの内部 API Management デプロイを示す図" lightbox="media/api-management/api-management-internal-deployment.png" border="false":::
