---
title: Safari ブラウザーに関する既知の問題 (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 用 Microsoft Authentication Library (MSAL.js) を Safari ブラウザーで使用するときの既知の問題について説明します。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: edb995e31c2872c1541e29fee09dd66aafc8f9e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696114"
---
# <a name="known-issues-on-safari-browser-with-msaljs"></a>Safari ブラウザーと MSAL.js に関する既知の問題 

## <a name="silent-token-renewal-on-safari-12-and-itp-20"></a>Safari 12 と ITP 2.0 のサイレント トークンの更新

Apple iOS 12 および MacOS 10.14 オペレーティング システムには [Safari 12 ブラウザー](https://developer.apple.com/safari/whats-new/)のリリースが含まれています。 セキュリティとプライバシー上の理由で、Safari 12 には [Intelligent Tracking Prevention 2.0](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) が含まれています。 これにより、基本的に、設定中のサード パーティの Cookie がブラウザーから削除されます。 さらに ITP 2.0 は、ID プロバイダーによって設定される Cookie をサード パーティの Cookie として扱います。

### <a name="impact-on-msaljs"></a>MSAL.js への影響

MSAL.js では非表示の Iframe を使用して、`acquireTokenSilent` 呼び出しの一部としてサイレント トークン取得と更新を実行します。 サイレント トークン要求は、Azure AD によって設定される Cookie によって表される認証済みのユーザー セッションにアクセスできる Iframe に依存します。 ITP 2.0 ではこれらの Cookie へのアクセスを防ぐため、MSAL.js はトークンのサイレント取得および更新に失敗し、この結果、`acquireTokenSilent` エラーが発生します。

現時点ではこの問題の解決策は存在せず、規格コミュニティと共にオプションを評価中です。

### <a name="work-around"></a>回避策

ITP 設定は Safari ブラウザーで既定で有効になっています。 **[環境設定]**  ->  **[プライバシー]** に移動し、 **[サイト越えトラッキングを防ぐ]** オプションのチェックをオフにすることで、この設定を無効化できます。

![Safari の設定](./media/msal-js-known-issue-safari-browser/safari.png)

ユーザーにサインインを求める対話型のトークン取得呼び出しでの `acquireTokenSilent` エラーを処理する必要があります。
サインインの繰り返しを避けるために実行できるアプローチは、`acquireTokenSilent` エラーを処理し、対話型の呼び出しに進む前に、Safari 内の ITP 設定を無効にするオプションをユーザーに提供することです。 設定を無効にすると、後続のサイレント トークン更新が成功します。
