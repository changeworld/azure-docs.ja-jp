---
title: Azure Defender for DNS - 利点と機能
description: Azure Defender for DNS の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 10/11/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ad2b761a747401646c48dcbf32385b8e5ec153c1
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740229"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Azure Defender for DNS の概要

Azure Defender for DNS を使用すると、Azure DNS の [Azure で提供される名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)機能を使用するリソースの保護が強化されます。 

リソースにエージェントを追加することなく、Defender for DNS によって、Azure DNS 内からこれらのリソースからのクエリが監視され、疑わしいアクティビティが検出されます。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|**Azure Defender for DNS** の課金については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」をご覧ください|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Azure Defender for DNS の利点

Azure Defender for DNS によって、次のような疑わしいアクティビティと異常なアクティビティが検出されます。

- DNS トンネリングを使用した Azure リソースからの **データ流出**
- コマンドおよびコントロール サーバーと通信する **マルウェア**
- **DNS 攻撃** - 悪意のある DNS リゾルバとの通信 
- フィッシングや暗号化マイニングなどの **悪意のあるアクティビティに使用されているドメインとの通信**

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
