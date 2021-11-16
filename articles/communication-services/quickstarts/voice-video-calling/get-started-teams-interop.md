---
title: クイック スタート - Azure Communication Services での Teams の相互運用
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Azure Communication Calling SDK を使用して、Teams の会議に参加する方法について説明します。
author: chpalm
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 4ab25d1105f8dcad40599a8096ea114c31dfa61a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488333"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>クイックスタート: 通話アプリを Teams の会議に参加させる

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Calling with Windows](./includes/teams-interop/teams-interop-windows.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop/teams-interop-ios.md)]
::: zone-end

このドキュメントで説明されている機能では、Communication Services SDK の一般提供版を使用しています。 Teams の相互運用性には、Communication Services SDK のベータ版が必要です。 ベータ版 SDK は、[リリース ノートのページ](https://github.com/Azure/Communication/tree/master/releasenotes)で見つかります。

ベータ版の SDK で "パッケージのインストール" 手順を実行する場合は、パッケージのバージョンを最新のベータ リリースに変更してください。それには、`communication-calling` パッケージ名にバージョン `@1.0.0-beta.10` (この記事の執筆時点でのバージョン) を指定します。 `communication-common` パッケージ コマンドを変更する必要はありません。 次に例を示します。

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

- [通話のヒーロー サンプル](../../samples/calling-hero-sample.md)を確認する
- [Calling SDK の機能](./getting-started-with-calling.md)について確認する
- [通話のしくみ](../../concepts/voice-video-calling/about-call-types.md)の詳細について確認する