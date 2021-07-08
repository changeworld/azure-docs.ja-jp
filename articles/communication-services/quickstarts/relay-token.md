---
title: クイックスタート - ネットワーク リレー トークンを取得する
description: Azure Communication Services を使用して STUN/TURN トークンを取得する方法について説明します
author: shahen
manager: anvalent
services: azure-communication-services
ms.author: shahen
ms.date: 05/21/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 2fc0bfeaef1d3e0e49d1597f41582b6d79d9a3db
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026929"
---
# <a name="quickstart-get-a-network-relay-token"></a>クイックスタート: ネットワーク リレー トークンを取得する

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

このクイックスタートでは、ネットワーク リレー トークンを取得して Azure Communication Services TURN サーバーにアクセスする方法を示します

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
