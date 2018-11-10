---
title: Azure での自動スケールとゾーン冗長 Application Gateway (パブリック プレビュー) | Microsoft Docs
description: この記事では、Azure portal での Application Gateway における Web アプリケーション ファイアウォール要求サイズ制限と除外リストについて説明します。
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/26/2018
ms.author: victorh
ms.openlocfilehash: 8fb3dce108b59b8df0d330ec642365d2487eae35
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085463"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>自動スケールとゾーン冗長 Application Gateway (パブリック プレビュー)

Application Gateway と Web アプリケーション ファイアウォール (WAF) は、現在、新しい v2 SKU でパブリック プレビューとして使用できます。この SKU では、パフォーマンスの向上が提供され、自動スケール、ゾーン冗長性、静的 VIP のサポートなどの重要な新機能のサポートが追加されます。 一般公開された SKU の既存機能は、既知の制限のセクションで示されているいくつかの例外を除き、新しい v2 SKU でも引き続きサポートされます。 新しい v2 SKU には、次の拡張機能が含まれます。

- **自動スケール**: 自動スケール SKU の下での Application Gateway または WAF のデプロイは、トラフィック負荷パターンの変化に基づいてスケールアップまたはスケールダウンできます。 また、自動スケールにより、プロビジョニングの間にデプロイのサイズまたはインスタンスの数を選択する必要がなくなります。 このように、SKU では真の弾力性が提供されます。 新しい SKU では、Application Gateway は固定容量モード (自動スケール無効) と自動スケール有効モードの両方で動作できます。 固定容量モードは、ワークロードが一定で予測可能なシナリオに便利です。 自動スケール モードは、アプリケーション トラフィックの変動が大きいアプリケーションで役に立ちます。
   
   > [!NOTE]
   > 現在、自動スケールは WAF SKU では利用できません。 WAF は、自動スケール モードではなく固定容量モードで構成してください。
- **ゾーン冗長性**: Application Gateway または WAF のデプロイは複数の可用性ゾーンを対象にできるので、Traffic Manager を使ってゾーンごとに個別に Application Gateway のインスタンスをプロビジョニングして稼働させる必要はありません。 Application Gateway のインスタンスをデプロイする単一のゾーンまたは複数のゾーンを選択できるので、ゾーンの障害の回復性が保証されます。 アプリケーションのバックエンド プールも、複数の可用性ゾーンに同様に分散できます。
- **パフォーマンスの向上**: 自動スケール SKU では、一般に利用可能な SKU と比較して、SSL オフロードのパフォーマンスが最大で 5 倍になります。
- **デプロイと更新の時間の短縮**: 自動スケール SKU では、一般に使用可能な SKU と比較して、デプロイと更新の時間が短縮されます。
- **静的 VIP**: アプリケーション ゲートウェイの VIP は、静的な VIP の種類だけをサポートするようになります。 これにより、アプリケーション ゲートウェイに関連付けられた VIP は、再起動後でも変化しません。

> [!IMPORTANT]
> 自動スケールおよびゾーン冗長アプリケーション ゲートウェイの SKU は、現在、パブリック プレビュー段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>サポートされているリージョン
自動スケール SKU は、米国東部 2、米国中部、米国西部 2、フランス中部、西ヨーロッパ、および東南アジアで利用できます。

## <a name="pricing"></a>価格
プレビューの期間中は、料金はかかりません。 キー コンテナーや仮想マシンなど、アプリケーション ゲートウェイ以外のリソースに対しては課金されます。 

## <a name="known-issues-and-limitations"></a>既知の問題と制限

|問題|詳細|
|--|--|
|認証証明書|サポートされていません。<br>詳細については、「[Application Gateway でのエンド ツー エンド SSL の概要](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)」を参照してください。|
|同じサブネット上の Standard_v2 と Standard Application Gateway の混在|サポートされていません。<br>さらに、自動スケールを有効にすると、1 つのサブネットには 1 つアプリケーション ゲートウェイだけが存在できます。|
|Application Gateway サブネット上のユーザー定義ルート (UDR)|サポートされていません|
|受信ポート範囲の NSG| - Standard_v2 SKU では 65200 ～ 65535<br>- Standard SKU では 65503 ～ 65534<br>詳細については、[FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet) をご覧ください。|
|Azure 診断でのパフォーマンス ログ|サポートされていません。<br>Azure メトリックを使用する必要があります。|
|課金|現在は請求されません。|
|FIPS モード、WebSocket|現在はサポートされていません。|
|ILB のみモード|現在これはサポートされていません。 パブリック モードと ILB モードがまとめてサポートされます。|
|Web アプリケーション ファイアウォールの自動スケール|WAF は、自動スケール モードをサポートしていません。 固定容量モードはサポートされています。|

## <a name="next-steps"></a>次の手順
- [Azure PowerShell を使用して、自動スケーリングの予約済み IP アドレスを持つゾーン冗長アプリケーション ゲートウェイを作成します](tutorial-autoscale-ps.md)
- [Application Gateway](overview.md) の詳細を参照します。
- [Azure Firewall](../firewall/overview.md) の詳細を参照します。 

