---
title: Web API アプリケーションを追加する - Azure Active Directory B2C
description: お使いの Active Directory B2C テナントに Web API アプリケーションを追加する方法について説明します。
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.author: kengaderdus
ms.date: 08/24/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a8a7c298c30f91d3eb2ad0f95998d9237777688e
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038123"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C テナントに Web API アプリケーションを追加する

 アクセス トークンを提示するクライアント アプリケーションからの要求を Web API リソースが受け付けて応答できるように、ご利用のテナントに Web API リソースを登録します。 この記事では、Azure Active Directory B2C (Azure AD B2C) に Web API を登録する方法について説明します。

アプリケーションを Azure AD B2C テナントに登録するには、Microsoft の新しい統合 **アプリの登録** エクスペリエンスか以前の **アプリケーション (レガシ)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](./app-registrations-training-guide.md)。

#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*webapi1* とします。
1. **[リダイレクト URI]** で、 **[Web]** を選択し、お使いのアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 実稼働アプリケーションでは、リダイレクト URI とエンドポイントを `https://localhost:5000` のように設定します。 開発時またはテスト時には、トークンのデコードされたコンテンツを表示する Microsoft 所有の Web アプリケーションである `https://jwt.ms` にこれを設定できます (トークンのコンテンツがお使いのブラウザー外に出ることはありません)。 お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。
1. **[登録]** を選択します。
1. ご自分の Web API のコードで使用するために、**アプリケーション (クライアント) ID** を記録しておきます。

[JavaScript ベースのシングルページ アプリケーション (SPA)](tutorial-register-spa.md) など、暗黙的な許可フローを実装するアプリケーションがある場合は、次の手順に従ってフローを有効にすることができます。

1. **[管理]** で、 **[認証]** を選択します。
1. **[暗黙的な許可]** で、 **[アクセス トークン]** と **[ID トークン]** の両方のチェック ボックスをオンにします。
1. **[保存]** を選択します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション (レガシ)]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*webapi1* とします。
1. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、 **[はい]** を選択します。
1. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 運用環境のアプリケーションでは、応答 URL を `https://localhost:44332` などの値に設定することになるでしょう。 テスト目的の場合は、応答 URL を `https://jwt.ms` に設定します。
1. **[アプリケーション ID URI]** には、ご使用の Web API で使用される ID を入力します。 ドメインを含んだ完全な識別子 URI が自動的に生成されます。 たとえば、「 `https://contosotenant.onmicrosoft.com/api` 」のように入力します。
1. **Create** をクリックしてください。
1. プロパティ ページで、アプリケーション ID を記録しておきます。これは、Web アプリケーションを構成するときに使用します。

* * *

## <a name="configure-scopes"></a>スコープを構成する

スコープを使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば Web API のユーザーが、読み取りと書き込みの両方のアクセス権限を持つ場合もあれば、読み取りアクセス権限しか持たない場合もあります。 このチュートリアルでは、スコープを使用して、Web API の読み取りアクセス許可と書き込みアクセス許可を定義します。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>[アクセス許可の付与]

保護された Web API をアプリケーションから呼び出すには、その API へのアクセス許可をアプリケーションに付与する必要があります。 たとえば「[チュートリアル: Azure Active Directory B2C にアプリケーションを登録する](tutorial-register-applications.md)」では、Azure AD B2C に *webapp1* という Web アプリケーションが登録されています。 このアプリケーションを使用して、Web API を呼び出すことができます。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

アプリケーションが登録されて、保護された Web API を呼び出すことができるようになります。 アプリケーションを使用するには、ユーザーは Azure AD B2C での認証を行います。 アプリケーションで、保護された Web API にアクセスするための認可の付与が、Azure AD B2C から取得されます。