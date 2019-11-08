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
ms.openlocfilehash: 7772b3ee5d0e27c09e83f7d118eb9f67f17e0d07
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523222"
---
## <a name="register-your-application-with-your-ad-tenant"></a>AD テナントへのアプリケーションの登録
まず、Azure Active Directory (Azure AD) テナントにアプリケーションを登録する必要があります。 これにより、アプリケーションのアプリケーション ID を取得でき、トークンを受信できるようになります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
   
1. ページ右上隅にあるアカウントを選択し、 **[ディレクトリの切り替え]** ナビゲーションを選択してから、適切なテナントを選択して、Azure AD テナントを選択します。 
   - アカウントの下の Azure AD テナントが 1 つのみの場合、または適切な Azure AD テナントを既に選択している場合は、この手順をスキップします。
   
1. Azure portal で、**Azure Active Directory** を検索して選択します。
   
1. **[Azure Active Directory]** の左側のメニューで、 **[アプリの登録]** を選択し、 **[新しい登録]** を選択します。
   
1. 画面の指示に従い、新しいアプリケーションを作成します。 このチュートリアルでは、Web アプリケーションであるかパブリック クライアント (モバイルとデスクトップ) アプリケーションであるかは重要ではありませんが、Web アプリケーションまたはパブリック クライアント アプリケーションの具体的な例を確認するには、[クイック スタート](../articles/active-directory/develop/v1-overview.md)をご覧ください。
   
   - **[名前]** はアプリケーションの名前で、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
   - **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
   - **[リダイレクト URI]** を指定します。 Web アプリケーションの場合、これはユーザーのサインイン場所となるアプリのベース URL です。  たとえば、「 `http://localhost:12345` 」のように入力します。 パブリック クライアント (モバイルとデスクトップ) の場合、Azure AD はこれを使用してトークン応答を返します。 アプリケーション固有の値を入力します。  たとえば、「 `http://MyFirstAADApp` 」のように入力します。
   <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
   
1. 登録が完了すると、Azure AD により、アプリケーションに一意のクライアント ID (**アプリケーション ID**) が割り当てられます。 この値は次のセクションで必要になるので、アプリケーション ページからコピーします。
   
1. Azure portal でアプリケーションを見つけるには、 **[アプリの登録]** を選択し、 **[アプリケーションをすべて表示]** を選択します。
