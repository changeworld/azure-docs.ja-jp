---
title: Azure Communication Services Call Recording API クイックスタート
titleSuffix: An Azure Communication Services quickstart document
description: Call Recording API のクイックスタート サンプルを提供します。
author: ravithanneeru
manager: GrantMeStrength
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
zone_pivot_groups: acs-csharp-java
ms.openlocfilehash: 397423b0bc86833b9a029c84c0d3e785ffc35049
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671870"
---
# <a name="call-recording-api-quickstart"></a>Call Recording API クイックスタート

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

このクイックスタートで音声通話とビデオ通話の録音を始めることができます。 このクイックスタートは、[呼び出し元のクライアント SDK](get-started-with-video-calling.md) を使用してエンドユーザーの通話エクスペリエンスを既に構築していることを前提としています。 **呼び出し元のサーバーの API と SDK** を使用すると、録音を有効にしたり、管理したりできます。 

::: zone pivot="programming-language-csharp"
[!INCLUDE [Build Call Recording server sample with C#](./includes/call-recording-samples/recording-server-csharp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Build Call Recording server sample with Java](./includes/call-recording-samples/recording-server-java.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [通話のヒーロー サンプル](../../samples/calling-hero-sample.md)を確認する
- [Calling SDK の機能](./calling-client-samples.md)について確認する
- [通話のしくみ](../../concepts/voice-video-calling/about-call-types.md)の詳細について確認する
