---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 2981a46502ad7a7ab673e00bd1e8d2033ef2213d
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84317774"
---
アプリケーションを Azure AD B2C テナントに登録するには、Microsoft の新しい統合**アプリの登録**エクスペリエンスか以前の**アプリケーション (レガシ)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの**名前**を入力します。 たとえば、*nativeapp1* とします。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory or any identity provider]\(任意の組織のディレクトリまたは任意の ID プロバイダー内のアカウント\)** を選択します。
1. **[リダイレクト URI]** で、ドロップダウンを使用して **[パブリック クライアント/ネイティブ (モバイルとデスクトップ)]** を選択します。
1. 一意のスキームを含めたリダイレクト URI を入力します。 たとえば、「 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 」のように入力します。 リダイレクト URI を選択する際には、2 つの重要な考慮事項があります。
    * **一意**:リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` の例では、`com.onmicrosoft.contosob2c.exampleapp` はスキームです。 このパターンに従う必要があります。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーにはアプリケーションを選択する選択肢が与えられます。 ユーザーが正しく選択しなかった場合、サインインは失敗します。
    * **完全**:リダイレクト URI には、スキームとパスの両方が必要です。 パスには、ドメインの後に少なくとも 1 つのスラッシュを含める必要があります。 たとえば、`//oauth/` は機能し、`//oauth` は失敗します。 アンダースコアなどの特殊文字は URI に含めないでください。
1. **[アクセス許可]** で、 *[openid と offline_access アクセス許可に対して管理者の同意を付与します]* チェック ボックスをオンにします。
1. **[登録]** を選択します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション (レガシ)]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*nativeapp1* とします。
1. **[ネイティブ クライアント]** の場合、 **[はい]** を選択します。
1. 一意のスキームを含めた**カスタム リダイレクト URI** を入力します。 たとえば、「 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` 」のように入力します。 リダイレクト URI を選択する際には、2 つの重要な考慮事項があります。
    * **一意**:リダイレクト URI のスキームは、すべてのアプリケーションで一意である必要があります。 `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` の例では、`com.onmicrosoft.contosob2c.exampleapp` はスキームです。 このパターンに従う必要があります。 2 つのアプリケーションで同じスキームを共有している場合、ユーザーにはアプリケーションを選択する選択肢が与えられます。 ユーザーが正しく選択しなかった場合、サインインは失敗します。
    * **完全**:リダイレクト URI には、スキームとパスの両方が必要です。 パスには、ドメインの後に少なくとも 1 つのスラッシュを含める必要があります。 たとえば、`//oauth/` は機能し、`//oauth` は失敗します。 アンダースコアなどの特殊文字は URI に含めないでください。
1. **［作成］** を選択します