---
title: Web API を呼び出すモバイル アプリ - アプリのコード構成 | Microsoft ID プラットフォーム
description: Web API を呼び出すモバイル アプリを構築する方法 (アプリのコード構成) について説明します
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b6ebab0eeca6895e1c7a0f6008972030d81da42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65962396"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Web API を呼び出すモバイル アプリ - アプリの登録

この記事では、モバイル アプリケーションを作成するためのアプリの登録手順が記載されています。

## <a name="supported-account-types"></a>サポートされているアカウントの種類

モバイル アプリケーションでサポートされているアカウントの種類は、有効にするエクスペリエンスやアプリが対象とするユーザーによって異なります。

## <a name="platform-configuration-and-redirect-uris"></a>プラットフォーム構成とリダイレクト URI  

モバイル アプリを構築するときに最も重要な登録手順は、リダイレクト URI です。 これは、[認証ブレードのプラットフォーム構成](https://aka.ms/MobileAppReg)で設定できます。

このエクスペリエンスにより、お使いのアプリで Microsoft Authenticator (および Android の Intune ポータル サイト) を介してシングル サインオン (SSO) できるようになり、デバイス管理ポリシーもサポートされます。

リダイレクト URI を手動で構成する場合は、アプリケーション マニフェストを介して行えます。 推奨フォーマットは以下のとおりです。

- ***iOS***: `msauth.<BUNDLE_ID>://auth`
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Android の署名のハッシュは、KeyTool コマンドを通じて、リリース キーまたはデバッグ キーを使用して生成できます。

## <a name="api-permissions"></a>API のアクセス許可

モバイル アプリケーションでは、サインインしたユーザーの代わりに API を呼び出せます。 お使いのアプリでは、委任されたアクセス許可 (スコープとも呼ばれる) を要求する必要があります。 これは、目的のエクスペリエンスに応じて、Azure portal を介して静的に実行することも、実行時に動的に実行することもできます。 アクセス許可を静的に登録すれば、管理者は簡単にアプリを承認できるため、この方法をお勧めします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [トークンの取得](scenario-mobile-acquire-token.md)
