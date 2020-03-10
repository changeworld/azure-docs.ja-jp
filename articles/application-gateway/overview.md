---
title: Azure Application Gateway とは
description: Azure Application Gateway を使用してアプリケーションに対する Web トラフィックを管理する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4a4395801218409fe77d1081689ba80b495fcfad
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302578"
---
# <a name="what-is-azure-application-gateway"></a>Azure Application Gateway とは

Azure Application Gateway は、Web アプリケーションに対するトラフィックを管理できる Web トラフィック ロード バランサーです。 従来のロード バランサーはトランスポート レイヤー (OSI レイヤー 4 - TCP と UDP) で動作し、送信元 IP アドレスとポートに基づくトラフィックを送信先 IP アドレスとポートにルーティングします。

Application Gateway では、URI パスやホスト ヘッダーなど、HTTP 要求の追加属性に基づいてルーティングを決定できます。 たとえば、着信 URL に基づいてトラフィックをルーティングできます。 そのため、`/images` が着信 URL にある場合、画像用に構成された特定のサーバー セット (プールと呼ばれます) にトラフィックをルーティングできます。 `/video` が URL にある場合、そのトラフィックはビデオ用に最適化された別のプールにルーティングされます。

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

この種類のルーティングは、アプリケーション レイヤー (OSI レイヤー 7) の負荷分散と呼ばれます。 Azure Application Gateway は URL ベースのルーティングなどを行うことができます。

>[!NOTE]
> Azure では、ユーザーのシナリオのためにフル マネージドの負荷分散ソリューションのスイートが提供されます。 高パフォーマンス、低待ち時間のレイヤー 4 負荷分散機能が必要な場合は、「[Azure Load Balancer の概要](../load-balancer/load-balancer-overview.md)」をご覧ください グローバル DNS の負荷分散が必要な場合は、「[Traffic Manager とは](../traffic-manager/traffic-manager-overview.md)」を参照してください。 実際のエンド ツー エンドのシナリオでは、これらのソリューションを組み合わせると役に立つことがあります。
>
> Azure の負荷分散オプションの比較については、「[Azure の負荷分散オプションの概要](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview)」を参照してください。

## <a name="features"></a>機能

Application Gateway の機能の詳細については、「[Azure Application Gateway の機能](features.md)」を参照してください。

## <a name="pricing-and-sla"></a>料金と SLA

Application Gateway の価格情報については、「[Application Gateway の価格](https://azure.microsoft.com/pricing/details/application-gateway/)」を参照してください。

Application Gateway の SLA 情報については、「[Application Gateway の SLA](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/)」を参照してください。

## <a name="next-steps"></a>次のステップ

要件と環境に応じて、Azure portal、Azure PowerShell、または Azure CLI のいずれかを使用してテスト アプリケーション ゲートウェイを作成できます。

- [クイック スタート: Azure Application Gateway による Web トラフィックのルーティング - Azure portal](quick-create-portal.md)
- [クイック スタート: Azure Application Gateway による Web トラフィックのルーティング - Azure PowerShell](quick-create-powershell.md)
- [クイック スタート: Azure Application Gateway による Web トラフィックのルーティング - Azure CLI](quick-create-cli.md)
