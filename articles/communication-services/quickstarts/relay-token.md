---
title: クイックスタート - TURN リレーにアクセスする
description: Azure Communication Services を使用して STUN/TURN トークンを取得する方法について説明します
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 09/28/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 0e813fe3098b71478a88f1a8cf52c523efaaa82f
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893226"
---
# <a name="quickstart-access-turn-relays"></a>クイックスタート: TURN リレーにアクセスする

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

::: zone pivot="programming-language-python"
[!INCLUDE [Get a network relay token with Python](./includes/relay-token-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get a network relay token with Java](./includes/relay-token-java.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services リソースをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](./create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

