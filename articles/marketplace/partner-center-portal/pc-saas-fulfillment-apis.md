---
title: Microsoft コマーシャル マーケットプレースの SaaS Fulfillment API
description: Microsoft AppSource および Azure Marketplace で SaaS オファーを統合できるようにする Fulfillment API について説明します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: ba1b158bc529b148a8e3138d122c13ead19e073e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858089"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>Microsoft コマーシャル マーケットプレースの SaaS Fulfillment API

SaaS Fulfillment API を使用すると、独立系ソフトウェア ベンダー (ISV) は SaaS アプリケーションを Microsoft AppSource および Azure Marketplace と統合できます。 これらの API により、ISV アプリケーションは、すべての商取引が有効になっているチャネル (直接、パートナー主導 (再販業者)、フィールド主導) に参加できるようになります。 これらは、Microsoft AppSource と Azure Marketplace で処理可能な SaaS オファーを一覧表示するために必要です。

> [!WARNING]
> この API の最新バージョンはバージョン 2 であり、すべての新しい SaaS オファーではこれを使用する必要があります。  この API のバージョン 1 は非推奨であり、既存のオファーをサポートするために保持されています。

## <a name="business-model-support"></a>ビジネス モデルのサポート

この API では、次のビジネス モデル機能がサポートされます。以下を実行できます。

* 1 つのオファーに対して複数のプランを指定する。 各プランに異なる機能を含め、異なる価格を設定できます。
* サイト単位またはユーザー単位の課金モデルに基づくオファーを提供する。
* 月単位と年単位 (前払い) の課金オプションを提供する。
* 交渉による取引上の合意に基づいて、顧客に対してプライベートな価格設定を提供する。


## <a name="next-steps"></a>次のステップ

SaaS アプリケーションをまだ登録していない場合は、「[Azure AD アプリケーションの登録](./pc-saas-registration.md)」で説明されているように、[Azure portal](https://ms.portal.azure.com) で登録を行います。  その後、このインターフェイスの最新バージョンを使用して開発を行います。[SaaS Fulfillment API バージョン 2](./pc-saas-fulfillment-api-v2.md)
