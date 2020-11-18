---
title: チュートリアル - Azure App Service でセキュリティで保護された Web アプリを構築する | Azure
description: このチュートリアルでは、Azure App Service を使用して Web アプリを構築する方法、認証を有効にする方法、Azure ストレージを呼び出す方法、および Microsoft Graph を呼び出す方法について学習します。
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428327"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>チュートリアル: App Service で認証を有効にし、ストレージと Microsoft Graph にアクセスする

このチュートリアルでは、一般的なアプリケーション シナリオについて説明します。以下の内容を学習します。

- [(A) Web アプリの認証を構成](scenario-secure-app-authentication-app-service.md)し、アクセスを組織内のユーザーに制限します。
- (B) マネージド ID を使用して、Web アプリケーションに代わって [Azure ストレージに安全にアクセス](scenario-secure-app-access-storage.md)します。
- (C) マネージド ID を使用して、[サインインしているユーザーに代わって](scenario-secure-app-access-microsoft-graph-as-user.md)、または [Web アプリケーションに代わって](scenario-secure-app-access-microsoft-graph-as-app.md) Microsoft Graph のデータにアクセスします。
- このチュートリアル用に作成した[リソースをクリーンアップ](scenario-secure-app-clean-up-resources.md)します。

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Microsoft ID プラットフォームでのアプリケーション シナリオ" border="false":::

最初に、Web アプリの認証を有効にする方法について学習します。

> [!div class="nextstepaction"]
> [Web アプリの認証を構成する](scenario-secure-app-authentication-app-service.md)
