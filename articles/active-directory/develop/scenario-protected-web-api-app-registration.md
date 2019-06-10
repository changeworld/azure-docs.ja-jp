---
title: 保護されている Web API - アプリの登録 | Azure
description: 保護されている Web API をビルドする方法とアプリを登録するために必要な情報について学習します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: 59af4e20c7fe838f7c725b47e45968941fa85cb7
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254052"
---
# <a name="protected-web-api---app-registration"></a>保護されている Web API - アプリの登録

この記事では、保護されている Web API でのアプリの登録の詳細について説明します。

「[クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する (プレビュー)](quickstart-register-app.md)」で、アプリケーションを登録する方法の一般的な手順を参照してください。

## <a name="accepted-token-version"></a>承認済みトークンのバージョン

Microsoft ID プラットフォームのエンドポイントでは、2 種類のトークン (v1.0 トークンと v2.0 トークン) を発行できます。 [アクセス トークン](access-tokens.md)に関するページで、これらのトークンの詳細を学習できます。 承認済みトークンのバージョンは、アプリケーションの作成時に選択した **[サポートされているアカウントの種類]** によって異なります。

- **[サポートされているアカウントの種類]** の値が **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox、Outlook.com など)]** の場合、承認済みトークンのバージョンは v2.0 にあります。
- それ以外の場合、承認済みトークンのバージョンは v1.0 になります。

アプリケーションを作成したら、次の手順に従って、承認済みトークンのバージョンを変更できます。

1. Azure portal では、自分のアプリを選択して、アプリ用の**マニフェスト**を選択します。
2. マニフェストで、 **"accessTokenAcceptedVersion"** を検索して、その値が **2** であることを確認します。 このプロパティによって、Azure AD では Web API で v2.0 トークンを承認することが認識されます。 それが **null 値**の場合は、承認済みトークン バージョンは v1.0 になります。
3. **[保存]** を選択します。

> [!NOTE]
> Web API によって、承認するトークン バージョン (v1.0 または v2.0) を決定できます。 クライアントから Microsoft ID プラットフォーム v2.0 エンドポイントを使用して、Web API 用のトークンが要求されるときに、Web API で承認されるバージョンを識別するトークンを取得します。

## <a name="no-redirect-uri"></a>リダイレクト URI なし

ユーザーが対話形式でサインインすることがないため、Web API によってリダイレクト URI を登録する必要がありません。

## <a name="expose-an-api"></a>API の公開

Web API に固有の別の設定は、公開されている API と公開されているスコープです。

### <a name="resource-uri-and-scopes"></a>リソース URI とスコープ

通常、スコープはフォーム `resourceURI/scopeName` です。 Microsoft Graph の場合、スコープには `User.Read` などのショートカットがありますが、この文字列は単に `https://graph.microsoft.com/user.read` 用のショートカットです。

アプリの登録時に、次のパラメーターを定義する必要があります。

- 1 つのリソース URI - 既定では、アプリケーションの登録ポータルでは、`api://{clientId}` を使用することをお勧めします。 このリソース URI は一意ですが、人間が判読できるものではありません。 これを変更することはできませんが、一意であることを確認してください。
- 1 つまたは複数のスコープ

お客様のアプリケーションを使用するエンド ユーザーに表示される同意画面には、スコープも表示されます。 そのため、スコープが説明される、対応する文字列を指定する必要があります。

- エンド ユーザーによって表示
- 管理者の同意を許可できるテナント管理者によって表示

### <a name="how-to-expose-the-api"></a>API を公開する方法

1. アプリケーションの登録で **[API の公開]** を選択します。
   1. **[Scope の追加]** を選択します。
   1. **[保存して続行]** を選択して、提案されたアプリケーション ID URI (api://{clientId}) を受け入れます。
   1. 次のパラメーターを入力します。
      - **[スコープ名]** には、`access_as_user` を使用します。
      - **[同意できるユーザー]** に、 **[管理者とユーザー]** オプションが選択されていることを確認します。
      - **[管理者の同意の表示名]** には、「`Access TodoListService as a user`」と入力します。
      - **[管理者の同意の説明]** には、「`Accesses the TodoListService Web API as a user`」と入力します。
      - **[ユーザーの同意の表示名]** には、「`Access TodoListService as a user`」と入力します。
      - **[ユーザーの同意の説明]** には、「`Accesses the TodoListService Web API as a user`」と入力します。
      - **[状態]** は **[有効]** に設定されたままにします。
      - **[スコープの追加]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのコード構成](scenario-protected-web-api-app-configuration.md)
