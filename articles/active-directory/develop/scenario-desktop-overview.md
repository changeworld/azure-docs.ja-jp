---
title: Web API を呼び出すデスクトップ アプリ (概要) - Microsoft ID プラットフォーム
description: Web API を呼び出すデスクトップ アプリを構築する方法について説明します (概要)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 204e230f7ff0897d2ba97213ebc836aff9d0324b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204647"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>シナリオ: Web API を呼び出すデスクトップ アプリ

Web API を呼び出すデスクトップ アプリを構築するために必要なすべてのことについて説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>使用の開始

最初のアプリケーションをまだ作成していない場合は、.NET デスクトップのクイック スタートまたは UWP のクイック スタートに従って作成してください。

> [!div class="nextstepaction"]
> [クイック スタート:Windows デスクトップ アプリからトークンを取得し、Microsoft Graph API を呼び出す](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [クイック スタート:UWP アプリからトークンを取得し、Microsoft Graph API を呼び出す](./quickstart-v2-uwp.md)

## <a name="overview"></a>概要

デスクトップ アプリケーションを作成して、ユーザーをアプリケーションにサインインさせ、Microsoft Graph、他の Microsoft API、または独自の Web API などの Web API を呼び出します。 次のようにいくつかの可能性があります。

- デスクトップ アプリケーションでグラフィカル コントロールがサポートされている場合、たとえば、Windows.Form アプリケーションや WPF アプリケーションの場合は、対話式のトークン取得を使用することができます。
- Windows でホストされているアプリケーションの場合、Windows ドメインに参加しているコンピューター、または AAD に参加しているコンピューターで実行されているアプリケーションが、統合 Windows 認証を使用して自動的にトークンを取得することも可能です。
- 最後に、推奨されていませんが、パブリック クライアント アプリケーションではユーザー名/パスワードを使用することができます。 これは一部のシナリオ (DevOps など) ではまだ必要ですが、それを使用するとアプリケーションに制約が課せされることに注意してください。 たとえば、多要素認証 (条件付きアクセス) を実行する必要があるユーザーはサインインさせることができません。 また、アプリケーションはシングル サインオン (SSO) のメリットを受けられません。

  さらに、先進認証の原則に反しており、これはレガシの理由のためにのみ提供されています。

  ![デスクトップ アプリケーション](media/scenarios/desktop-app.svg)

- 移植可能なコマンド ライン ツール (おそらく、Linux または Mac で実行される .NET Core アプリケーション) を作成している場合は、(.NET Core では [Web ブラウザー](https://aka.ms/msal-net-uses-web-browser)が提供されていないため) 対話式認証を使用できず、また統合 Windows 認証も使用できません。 その場合の最善の選択肢は、デバイス コード フローを使用することです。 このフローは、IoT アプリケーションなどのブラウザーがないアプリケーションにも使用されます。

  ![ブラウザーレス アプリケーション](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>詳細

デスクトップ アプリケーションには多くの特異性があります。それらは主に、アプリケーションが対話式認証を使用しているかどうかによって決まります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デスクトップ アプリ - アプリの登録](scenario-desktop-app-registration.md)
