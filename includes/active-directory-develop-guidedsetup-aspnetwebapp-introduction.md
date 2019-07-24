---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 46247d42837f8ac181d33216d2b93d28e2533c09
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482311"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>ASP.NET Web アプリへの "Microsoft でサインイン" の追加

このガイドでは、OpenID Connect を使用する従来の Web ブラウザー ベースのアプリケーションで、ASP.NET MVC ソリューションを使用して "Microsoft でサインイン" を実装する方法を示します。

このガイドの終了時には、お使いのアプリケーションが outlook.com や live.com など、個人用アカウントのサインインを受け入れることができるようになります。 また、これらのアカウントには、Azure Active Directory と統合された会社や組織の職場または学校アカウントも含まれます。

> このガイドには Visual Studio 2019 が必要です。  お持ちでない場合は、  [Visual Studio 2019 を無料でダウンロードできます](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このチュートリアルで生成されたサンプル アプリの動作の紹介](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

作成するサンプル アプリケーションは、サインインボタンによる認証をユーザーに要求する ASP.NET Web サイトへのアクセスにはブラウザーを使用します。 このシナリオでは、Web ページを表示する処理の大半がサーバー側で発生します。

## <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|アプリケーションで認証に OpenIDConnect を使用できるようにするためのミドルウェア|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|アプリケーションで Cookie を使用してユーザー セッションを維持できるようにするためのミドルウェア|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|ASP.NET 要求パイプラインを使用した、IIS 上での OWIN ベース アプリケーションの実行が可能|
