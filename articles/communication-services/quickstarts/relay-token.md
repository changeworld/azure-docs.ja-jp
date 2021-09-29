---
title: クイックスタート - TURN リレーにアクセスする
description: Azure Communication Services を使用して STUN/TURN トークンを取得する方法について説明します
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 7e449fa88a080fd9568436c7806767bac52f1861
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677152"
---
# <a name="quickstart-access-turn-relays"></a>クイックスタート: TURN リレーにアクセスする

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

このクイックスタートでは、ネットワーク リレー トークンを取得して Azure Communication Services TURN サーバーにアクセスする方法を示します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成する](https://azure.microsoft.com/free)
- アクティブな Azure Communication Services リソース。ない場合は、「[Communication Services リソースを作成する](./create-communication-resource.md)」を参照してください。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get a network relay token with .NET](./includes/relay-token-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get a network relay token with JavaScript](./includes/relay-token-js.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services リソースをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](./create-communication-resource.md#clean-up-resources)に関する記事を参照してください。
