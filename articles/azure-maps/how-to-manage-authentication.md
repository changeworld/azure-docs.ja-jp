---
title: 認証を管理する
titleSuffix: Azure Maps
description: Azure Maps 認証について理解を深めます。 どの方法がどのシナリオで最適に機能するかを確認します。 ポータルを使用して認証設定を表示する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 955b541bdb4ae38066f1eb4d2f09363ec51be1d2
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864076"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps での認証の管理

Azure Maps アカウントを作成した後、Azure Active Directory (Azure AD) 認証および共有キー認証をサポートするためにクライアント ID とキーが作成されます。

## <a name="view-authentication-details"></a>認証の詳細を表示する

Azure Maps アカウントの作成後、主キーと 2 次キーが生成されます。 [共有キー認証を使用して](./azure-maps-authentication.md#shared-key-authentication) Azure Maps を呼び出す場合は、サブスクリプション キーとして主キーを使用することをお勧めします。 2 次キーは、キー変更のローリングなどのシナリオで使用できます。 詳細については、「[Azure Maps による認証](./azure-maps-authentication.md)」をご覧ください。

認証の詳細は、Azure portal で確認できます。 Azure portal の自分のアカウントで、 **[設定]** メニューの **[認証]** を選択します。

> [!div class="mx-imgBorder"]
> ![認証の詳細](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>カテゴリとシナリオを調べる

アプリケーションのニーズに応じて、アプリケーションをセキュリティで保護するための特定の方法があります。 Azure AD では、さまざまな認証フローをサポートするためのカテゴリが定義されています。 アプリケーションがどのカテゴリに適合するかについては、「[アプリケーションのカテゴリ](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories)」を参照してください。

> [!NOTE]
> 共有キー認証を使用する場合でも、カテゴリとシナリオを理解することは、アプリケーションをセキュリティで保護するのに役立ちます。

## <a name="determine-authentication-and-authorization"></a>認証と認可を判断する

次の表に、Azure Maps での一般的な認証と認可のシナリオの概要を示します。 表では、それぞれのシナリオによって提供される保護の種類を比較しています。

> [!IMPORTANT]
> Microsoft では、運用アプリケーション用に Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Active Directory (Azure AD) を実装することをお勧めします。

| シナリオ                                                                                    | 認証 | 承認 | 開発の作業量 | 運用の作業量 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [信頼済みデーモンまたは非対話型クライアント アプリケーション](./how-to-secure-daemon-app.md)        | 共有キー     | 該当なし           | Medium             | 高               |
| [信頼済みデーモンまたは非対話型クライアント アプリケーション](./how-to-secure-daemon-app.md)        | Azure AD       | 高          | 低                | Medium             |
| [対話型のシングル サインオンを使用する Web シングル ページ アプリケーション](./how-to-secure-spa-users.md) | Azure AD       | 高          | 中             | Medium             |
| [非対話型サインオンを使用する Web シングル ページ アプリケーション](./how-to-secure-spa-app.md)      | Azure AD       | 高          | 中             | Medium             |
| [対話型シングル サインオンを使用する Web アプリケーション](./how-to-secure-webapp-users.md)          | Azure AD       | 高          | 高               | Medium             |
| [IoT デバイスまたは入力の制約付きデバイス](./how-to-secure-device-code.md)                     | Azure AD       | 高          | 中             | Medium             |

各シナリオの詳細な構成情報については、表のリンクを参照してください。

## <a name="view-role-definitions"></a>ロール定義を表示する

Azure Maps に使用できる Azure ロールを表示するには、 **[アクセス制御 (IAM)]** に移動します。 **[ロール]** を選択してから、「*Azure Maps*」で始まるロールを検索します。 これらの Azure Maps ロールが、アクセス権を付与できるロールです。

> [!div class="mx-imgBorder"]
> ![使用可能なロールの表示](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>ロールの割り当てを表示する

Azure Maps に対するアクセス権が付与されているユーザーやアプリを表示するには、 **[アクセス制御 (IAM)]** に移動します。 そこで、 **[ロールの割り当て]** を選択してから、「**Azure Maps**」でフィルター処理します。

> [!div class="mx-imgBorder"]
> ![アクセス権が付与されているユーザーやアプリの表示](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Azure Maps のトークンを要求する

Azure AD トークン エンドポイントからトークンを要求します。 Azure AD 要求で、次の詳細を使用します。

| Azure 環境      | Azure AD トークン エンドポイント             | Azure リソース ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure パブリック クラウド     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government クラウド | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Azure AD からユーザーやサービス プリンシパルのアクセス トークンを要求する方法の詳細については、[Azure AD の認証シナリオ](../active-directory/develop/authentication-vs-authorization.md)に関するページを参照し、[シナリオ](./how-to-manage-authentication.md#determine-authentication-and-authorization)の表から特定のシナリオを確認してください。

## <a name="manage-and-rotate-shared-keys"></a>共有キーの管理とローテーション

Azure Maps サブスクリプション キーは、Azure Maps アカウントのルート パスワードに似ています。 サブスクリプション キーは常に慎重に保護してください。 キーを安全に管理およびローテーションするには、Azure Key Vault を使用します。 アクセス キーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーンテキストで保存したりしないでください。 キーが侵害された可能性があると思われる場合は、キーをローテーションしてください。

> [!NOTE]
> 要求を承認するには、可能であれば、共有キーの代わりに、Azure Active Directory (Azure AD) を使用することをお勧めします。 Azure AD では、共有キーよりも優れたセキュリティと使いやすさが実現されます。

### <a name="manually-rotate-subscription-keys"></a>サブスクリプション キーを手動でローテーションする

Azure Maps アカウントのセキュリティを確保するために、サブスクリプション キーを定期的にローテーションすることをお勧めします。 可能であれば、Azure Key Vault を使用してアクセスキーを管理してください。 Key Vault を使用していない場合は、キーを手動でローテーションする必要があります。

キーをローテーションさせることができるように、2 つのサブスクリプション キーが割り当てられます。 2 つのキーにより、アプリケーションがプロセス全体を通じて Azure Maps へのアクセスを確実に維持できるようにします。

Azure portal で Azure Maps サブスクリプション キーをローテーションするには、次の手順に従います。

1. アプリケーション コードを、Azure Maps アカウントの 2 次キーを参照するように更新してデプロイします。
2. [Azure portal](https://portal.azure.com/) で Azure Maps アカウントに移動します。
3. **[設定]** で **[認証]** を選択します。
4. Azure Maps アカウントの主キーを再生成するには、主キーの隣にある **[再生成]** ボタンを選択します。
5. 新しい主キーを参照するようにアプリケーション コードを更新してデプロイします。
6. 同様に、 2 次キーを再生成します。

> [!WARNING]
> Microsoft では、すべてのアプリケーションで、同時にいずれかのキーのみを使用することをお勧めします。 キー 1 を使用する場所とキー 2 を使用する場所がある場合、キーを循環させると、一部のアプリケーションがアクセス権を失います。

## <a name="next-steps"></a>次のステップ

詳細については、[Azure AD と Azure Maps Web SDK](./how-to-use-map-control.md) に関する記事をご覧ください。

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示すサンプルを確認します。

> [!div class="nextstepaction"]
> [Azure AD 認証のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)