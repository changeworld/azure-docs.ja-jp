---
title: Azure Defender for DNS - 利点と機能
description: Azure Defender for DNS の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 08/18/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: cffe4947e321cbd1f2cbb8a4d22141039129c1e1
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419597"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Azure Defender for DNS の概要

[Azure DNS](../dns/dns-overview.md) は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

Azure Defender for DNS により、次の方法によって Azure DNS に接続されているリソースの保護が強化されます。

- Azure リソースからのすべての DNS クエリを継続的に監視する
- 高度なセキュリティ分析を実行して不審なアクティビティについてアラートする

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|**Azure Defender for DNS** の課金については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Azure Defender for DNS の利点

Azure Defender for DNS により、Azure DNS に接続されているリソースは、次のような問題から保護されます。

- Azure リソースからのデータ流出 (DNS トンネリングを使用)
- C&C サーバーを使用したマルウェアの通信
- 悪意のあるドメインとの通信 (フィッシング、クリプト マイニングなど)
- DNS 攻撃 - 悪意のある DNS リゾルバとの通信 

Azure Defender for DNS によって提供されるアラートの全一覧は、[アラートのリファレンス ページ](alerts-reference.md#alerts-dns)に掲載されています。

## <a name="dependencies"></a>依存関係

Azure Defender for DNS では、エージェントは使用されません。 

DNS レイヤーを保護するためには、各サブスクリプションについて、「[Azure Defender を有効にする](enable-azure-defender.md)」の説明に従って Azure Defender for DNS を有効にします。


## <a name="next-steps"></a>次のステップ

この記事では、Azure Defender for DNS について説明しました。 

> [!div class="nextstepaction"]
> [Azure Defender を有効にする](enable-azure-defender.md)

関連資料については、次の記事をご覧ください。 

- セキュリティ アラートは、Security Center によって生成される場合もあれば、Security Center がさまざまなセキュリティ製品から受信する場合もあります。 それらすべてのアラートを Azure Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。