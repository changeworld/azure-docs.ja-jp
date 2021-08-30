---
title: Azure Key Vault Managed HSM - サードパーティのソリューション | Microsoft Docs
description: Managed HSM と統合されたサードパーティのソリューションについて説明します。
services: key-vault
author: mbaldwin
editor: ''
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: mbaldwin
ms.openlocfilehash: e70afc4a89fc0c9ce5c6ec59cd795b83b5e078b0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443471"
---
# <a name="third-party-solutions"></a>サードパーティ ソリューション

Azure Key Vault Managed HSM は、フル マネージド、高可用性、シングル テナント、標準準拠を特徴とするクラウド サービスで、**FIPS 140-2 レベル 3** 適合の HSM を使用してクラウド アプリケーションの暗号化キーを保護することができます。 [詳細については、こちらを参照してください](overview.md)。

複数のベンダーが Microsoft と密接に連携して、ソリューションを Managed HSM と統合しています。 次の表に、これらのソリューションの簡単な説明 (ベンダーが提供) を示します。 ベンダーの Azure Marketplace オファリングおよびドキュメントへのリンクも提供されます。


## <a name="third-party-solutions-integrated-with-managed-hsm"></a>Managed HSM と統合されたサードパーティのソリューション

| ベンダー名 | ソリューションの説明 |
|-------------|-------------------------------------------------|
|[Cloudflare](https://cloudflare.com)|Cloudflare の Keyless SSL を使用すると、Web サイトで Cloudflare の SSL サービスを使用できる一方、Managed HSM の秘密キーの保全状態を保つことができます。 このサービスを Managed HSM と組み合わせると、秘密キーを保護し、署名操作および暗号化操作を内部で実行し、アクセス制御を提供し、改ざんされにくい FIPS 140-2 レベル 3 HSM にキーを格納することによって、高レベルの保護を実現できます。 <br>[ドキュメント](https://developers.cloudflare.com/ssl/keyless-ssl/hardware-security-modules/azure-managed-hsm)
|[NewNet 通信テクノロジ](https://newnet.com/)|NewNet の Secure Transaction Cloud (STC) は、業界初のクラウド ベースの安全な支払いルーティング、切り替え、クラウド ベースの仮想化 HSM で拡張されたトランスポート ソリューションで、モバイル、Web、ストア内の支払いを処理します。 STC を使用すると、支払いエンティティのクラウド変換が可能になり、新規支払いプロバイダーが迅速にデプロイできます。<br/>[Azure Marketplace オファリング](https://azuremarketplace.microsoft.com/marketplace/apps/newnetcommunicationtechnologies1589991852134.secure_transaction_cloud?tab=overview)<br/>[ドキュメント](https://newnet.com/business-units/secure-transactions/products/secure-transaction-cloud-stc/)|
|[PrimeKey](https://www.primekey.com)|世界で最も使用されている PKI (公開キー基盤) である EJBCA Enterprise は、任意のユース ケースのための、信頼された ID および安全な通信のための基本セキュリティ サービスを提供します。 EJBCA Enterprise の 1 つのインスタンスは、複数の CA およびレベルをサポートし、複数のユース ケースに対して完全なインフラストラクチャの構築を可能にします。<br>[Azure Marketplace オファリング](https://azuremarketplace.microsoft.com/marketplace/apps/primekey.ejbca_enterprise_cloud_2)<br/>[ドキュメント](https://doc.primekey.com/x/a4z_/)|



## <a name="next-steps"></a>次のステップ
* [Managed HSM の概要](overview.md)
* [Managed HSM のベスト プラクティス](best-practices.md)

