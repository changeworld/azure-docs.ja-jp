---
title: Microsoft Defender for DNS - 利点と機能
description: Microsoft Defender for DNS の利点と機能について説明します。
author: memildin
ms.author: memildin
ms.date: 10/20/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 5e9c316576187db360c2db9d5c6f51929d7c7c3f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467646"
---
# <a name="introduction-to-microsoft-defender-for-dns"></a>Microsoft Defender for DNS の概要

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for DNS を使用すると、Azure DNS の [Azure で提供される名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)機能を使用するリソースの保護が強化されます。 

リソースにエージェントを追加することなく、Defender for DNS によって、Azure DNS 内からこれらのリソースからのクエリが監視され、疑わしいアクティビティが検出されます。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|**Microsoft Defender for DNS** の課金については、[価格に関するページ](https://azure.microsoft.com/pricing/details/security-center/)をご覧ください。|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure China 21Vianet<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-dns"></a>Microsoft Defender for DNS の利点

Microsoft Defender for DNS によって、次のような疑わしいアクティビティと異常なアクティビティが検出されます。

- DNS トンネリングを使用した Azure リソースからの **データ流出**
- コマンドおよびコントロール サーバーと通信する **マルウェア**
- **DNS 攻撃** - 悪意のある DNS リゾルバとの通信 
- フィッシングや暗号化マイニングなどの **悪意のあるアクティビティに使用されているドメインとの通信**

Microsoft Defender for DNS によって提供されるアラートの完全な一覧は、[アラートのリファレンス ページ](alerts-reference.md#alerts-dns)に掲載されています。

## <a name="dependencies"></a>依存関係

Microsoft Defender for DNS では、エージェントは使用されません。 

DNS レイヤーを保護するためには、各サブスクリプションについて、[強化された保護の有効化](enable-enhanced-security.md)に関するページの説明に従って Microsoft Defender for DNS を有効にします。


## <a name="respond-to-microsoft-defender-for-dns-alerts"></a>Microsoft Defender for DNS のアラートに対応する

Microsoft Defender for DNS からアラートを受け取った場合は、下の説明に従って調査し、アラートに対応することをお勧めします。 Microsoft Defender for DNS によってすべての接続されたリソースが保護されているため、アラートをトリガーしたアプリケーションまたはユーザーを熟知している場合でも、すべてのアラートを取り巻く状況を確認することが重要です。  


### <a name="step-1-contact"></a>手順 1. Contact

1. リソース所有者に問い合わせて、動作が想定されていたか、意図的であったかを判断します。
1. そのアクティビティが想定されている場合は、アラートを無視します。
1. そのアクティビティが想定されていない場合は、リソースをセキュリティ侵害された可能性があるものとして扱い、次の手順で説明するように軽減します。

### <a name="step-2-immediate-mitigation"></a>手順 2. 即時軽減策 

1. ネットワークからリソースを分離し、横移動を防止します。
1. リソースに対してマルウェア対策のフル スキャンを実行し、結果として得られる修復アドバイスに従います。
1. リソースにインストールされた実行中のソフトウェアを確認し、不明または不要なパッケージを削除します。
1. マシンを既知の正常な状態に戻し、必要に応じてオペレーティング システムを再インストールし、マルウェアのない検証済みソースからソフトウェアを復元します。
1. マシンに対する Microsoft Defender for DNS の推奨事項をすべて解決し、今後の侵害を防ぐために強調表示されたセキュリティ上の問題を修復します。


## <a name="next-steps"></a>次の手順

この記事では、Microsoft Defender for DNS について説明しました。 

> [!div class="nextstepaction"]
> [強化された保護を有効にする](enable-enhanced-security.md)

関連資料については、次の記事をご覧ください。 

- セキュリティ アラートは、Defender for Cloud によって生成されるか、他のセキュリティ製品から受信される可能性があります。 それらすべてのアラートを Microsoft Sentinel、サードパーティ製の SIEM、または他の外部ツールにエクスポートするには、[SIEM へのアラートのエクスポート](continuous-export.md)に関するページの手順に従ってください。