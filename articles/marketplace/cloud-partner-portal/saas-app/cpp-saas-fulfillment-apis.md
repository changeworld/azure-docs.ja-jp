---
title: SaaS Fulfillment API - Azure Marketplace | Microsoft Docs
description: ご自身の SaaS オファーを Azure Marketplace に統合できるようにする Fulfillment API のバージョンについて説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 9c3fbe7cd7ebd41f59be360f40d66b8d38dbce5e
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2019
ms.locfileid: "57319277"
---
# <a name="saas-fulfillment-apis"></a>SaaS Fulfillment API

独立系ソフトウェア ベンダー (ISV) は、SaaS Fulfillment API を使用して、自社の SaaS アプリケーションを Azure Marketplace に 統合できます。 この API により、ISV アプリケーションは、すべての商取引が有効になっているチャネル (直接、パートナー主導 (再販業者)、フィールド主導) に参加できるようになります。  この API は、取引可能な SaaS オファーを Azure Marketplace 上に表示するための要件になります。

この API の最新バージョンはバージョン 2 であり、すべての新しい SaaS オファーではこれを使用する必要があります。  この API のバージョン 1 は非推奨であり、既存のオファーをサポートするために保持されています。


## <a name="business-model-support"></a>ビジネス モデルのサポート

この API では、次のビジネス モデル機能がサポートされます。以下を実行できます。

* 1 つのオファーに対して複数のプランを指定する。 各プランに異なる機能を含め、異なる価格を設定できます。
* サイト単位またはユーザー単位の課金モデルに基づくオファーを提供する。
* 月単位と年単位 (前払い) の課金オプションを提供する。
* 交渉による取引上の合意に基づいて、顧客に対してプライベートな価格設定を提供する。


## <a name="next-steps"></a>次の手順

このインターフェイスの最新バージョンを使用して開発を行います。[SaaS Fulfillment API バージョン 2](./cpp-saas-fulfillment-api-v2.md)
