---
title: Azure Active Directory を使用したヘッダーベースの認証
description: Azure Active Directory を使用したヘッダーベースの認証の取得に関するアーキテクチャ ガイダンス。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99cf28c88b3c94fad5e9abe7eaee1e11bcfacbe6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052372"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Azure Active Directory を使用したヘッダーベースの認証

一般に、レガシ アプリケーションではヘッダーベースの認証が使用されます。 このシナリオでは、ユーザー (またはメッセージの発信者) が中間 id ソリューションに対して認証を行います。 中間ソリューションでは、ユーザーが認証され、必要なハイパーテキスト転送プロトコル (HTTP) ヘッダーが宛先 Web サービスに伝達されます。 Azure Active Directory (AD) では、このパターンがアプリケーション プロキシ サービスを使用してサポートされ、他のネットワーク コントローラー ソリューションに統合されます。

このソリューションでは、アプリケーション プロキシからアプリケーションへのリモート アクセスが提供され、ユーザーが認証されると、アプリケーションに必要なヘッダーが渡されます。 

## <a name="use-when"></a>使用する状況

リモート ユーザーは、ヘッダーベースの認証が必要なオンプレミス アプリケーションへのシングル サインオン (SSO) を安全に行う必要があります。

![ヘッダーベースの認証アーキテクチャの図](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>システムのコンポーネント

* **[ユーザー]** :アプリケーション プロキシで提供されるレガシ アプリケーションにアクセスします。

* **Web ブラウザー**:アプリケーションの外部 URL にアクセスするためにユーザーが操作するコンポーネントです。

* **Azure AD**:ユーザーを認証します。 

* **アプリケーション プロキシ サービス**:ユーザーからオンプレミスのアプリケーションに要求を送信するためのリバース プロキシとして機能します。 Azure AD に格納され、条件付きアクセス ポリシーを適用することもできます。

* **アプリケーション プロキシ コネクタ**:アプリケーションへの接続を提供するために、Windows サーバーにオンプレミスでインストールされます。 送信接続のみが使用されます。 Azure AD への応答を返します。

* **レガシ アプリケーション**:アプリケーション プロキシからのユーザー要求を受信するアプリケーションです。 セッションを設定し、応答を返すために必要な HTTP ヘッダーがレガシ アプリケーションで受信されます。 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Azure AD を使用したヘッダーベースの認証を実装する

* [Azure AD でアプリケーション プロキシを使用したリモート アクセスを行うためにオンプレミス アプリケーションを追加する](../app-proxy/application-proxy-add-on-premises-application.md)  

* [アプリケーション プロキシと PingAccess を使用したシングル サインオン用のヘッダーベースの認証](../app-proxy/application-proxy-configure-single-sign-on-with-headers.md) 

* [アプリ デリバリー コントローラーとネットワークを使用してレガシ アプリをセキュリティで保護する](../manage-apps/secure-hybrid-access.md)
