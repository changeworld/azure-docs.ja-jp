---
title: Azure AD .NET プロトコルの概要 | Microsoft Docs
description: Azure AD を利用してテナントの Web アプリケーションと Web API へのアクセスを承認するために HTTP メッセージを使用する方法。
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181348"
---
## <a name="register-your-application-with-your-ad-tenant"></a>AD テナントへのアプリケーションの登録
まず、Azure Active Directory (Azure AD) テナントにアプリケーションを登録する必要があります。 これにより、アプリケーションのアプリケーション ID を取得でき、トークンを受信できるようになります。

* [Azure Portal](https://portal.azure.com) にサインインします。
* ページ右上隅にあるアカウントをクリックして Azure AD テナントを選択し、 **[ディレクトリの切り替え]** ナビゲーションをクリックして、適切なテナントを選択します。 
  * アカウントの下の Azure AD テナントが 1 つのみの場合、または適切な Azure AD テナントを既に選択している場合は、この手順をスキップします。
* 左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** をクリックします。
* **[アプリの登録]** をクリックし、 **[新規登録]** をクリックします。
* 画面の指示に従い、新しいアプリケーションを作成します。 このチュートリアルでは、Web アプリケーションであるかパブリック クライアント (モバイルとデスクトップ) アプリケーションであるかは重要ではありませんが、Web アプリケーションまたはパブリック クライアント アプリケーションの具体的な例を確認するには、[クイック スタート](../articles/active-directory/develop/v1-overview.md)をご覧ください。
  * **[名前]** はアプリケーションの名前で、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
  * **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
  * **[リダイレクト URI]** を指定します。 Web アプリケーションの場合、これはユーザーのサインイン場所となるアプリのベース URL です。  たとえば、「 `http://localhost:12345` 」のように入力します。 パブリック クライアント (モバイルとデスクトップ) の場合、Azure AD はこれを使用してトークン応答を返します。 アプリケーション固有の値を入力します。  たとえば、「 `http://MyFirstAADApp` 」のように入力します。
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* 登録が完了すると、Azure AD により、アプリケーションに一意のクライアント ID (**アプリケーション ID**) が割り当てられます。 この値は次のセクションで必要になるので、アプリケーション ページからコピーします。
* Azure Portal でアプリケーションを見つけるには、 **[アプリの登録]** をクリックし、 **[アプリケーションをすべて表示]** をクリックします。
