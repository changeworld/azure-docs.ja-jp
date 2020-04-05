---
title: Azure ポータルを使用して Relay 名前空間を作成する | Microsoft Docs
description: この記事では、Azure portal を使用して Relay 名前空間を作成する方法を段階的に説明します。
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 78ab6753-877a-4426-92ec-a81675d62a57
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: b9811ef92aba4891627c20e0269be136582a3304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514665"
---
# <a name="create-a-relay-namespace-using-the-azure-portal"></a>Azure Portal を使用した Relay 名前空間の作成

名前空間は、すべての Azure Relay コンポーネントを範囲とするコンテナーです。 複数のリレーを 1 つの名前空間に格納できます。多くの場合、名前空間はアプリケーション コンテナーとして機能します。 現在、リレー名前空間の作成には 2 つの方法があります。

1. Azure Portal (この記事)。
2. [Azure Resource Manager](../azure-resource-manager/management/overview.md) のテンプレート。

## <a name="create-a-namespace-in-the-azure-portal"></a>Azure Portal での名前空間の作成

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

お疲れさまでした。 これで、リレー名前空間を作成できました。

## <a name="next-steps"></a>次のステップ

* [Relay に関する FAQ](relay-faq.md)
* [.NET を使って作業を開始する](relay-hybrid-connections-dotnet-get-started.md)
* [Node を使って作業を開始する](relay-hybrid-connections-node-get-started.md)

