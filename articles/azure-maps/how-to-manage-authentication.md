---
title: Microsoft Azure Maps 内で認証を管理する
titleSuffix: Azure Maps
description: Azure Maps 認証について理解を深めます。 どの方法がどのシナリオで最適に機能するかを確認します。 ポータルを使用して認証設定を表示する方法について説明します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/10/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
custom.ms: subject-rbac-steps
ms.openlocfilehash: d087daf38c455fd4d87ea571ff5f3a0ab9ad0527
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454779"
---
# <a name="manage-authentication-in-azure-maps"></a>Azure Maps での認証の管理

Azure Maps アカウントを作成するときに、キーとクライアント ID が生成されます。 キーとクライアント ID は、Azure Active Directory (Azure AD) 認証および共有キー認証をサポートするために使用されます。

## <a name="view-authentication-details"></a>認証の詳細を表示する

 >[!IMPORTANT]
 >[共有キー認証を使用して Azure Maps を呼び出す](./azure-maps-authentication.md#shared-key-authentication)場合は、サブスクリプション キーとして主キーを使用することをお勧めします。 セカンダリ キーは、キーのローリング変更などのシナリオで使用することをお勧めします。 詳細については、「[Azure Maps による認証](./azure-maps-authentication.md)」をご覧ください。

Azure Maps の認証の詳細を表示するには:

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal メニューに移動します。 **[すべてのリソース]** を選択し、Azure Maps アカウントを選択します。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/select-all-resources.png" alt-text="Azure Maps アカウントを選択する。":::

3. 左側のウィンドウの **[設定]** で、 **[認証]** を選択します。

      :::image type="content" border="true" source="./media/how-to-manage-authentication/view-authentication-keys.png" alt-text="認証の詳細。":::

## <a name="choose-an-authentication-category"></a>認証カテゴリを選択する

お使いのアプリケーションのニーズに応じて、アプリケーション セキュリティの実現に向けた特定の道筋があります。 Azure AD では、さまざまな認証フローをサポートするために、特定の認証カテゴリが定義されています。 アプリケーションに最適なカテゴリを選択するには、「[アプリケーションのカテゴリ](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories)」を参照してください。

> [!NOTE]
> 共有キー認証を使用する場合でも、カテゴリとシナリオを理解することは、アプリケーションをセキュリティで保護するのに役立ちます。

## <a name="choose-an-authentication-and-authorization-scenario"></a>認証と認可のシナリオを選択する

この表は、Azure Maps での一般的な認証と認可のシナリオの概要を示しています。 各シナリオの詳細な構成情報については、それぞれのリンクを使用してください。

> [!IMPORTANT]
> 運用アプリケーションの場合、Azure AD と Azure ロールベースのアクセス制御 (Azure RBAC) を実装することをお勧めします。

| シナリオ                                                                                    | 認証 | 承認 | 開発の作業量 | 運用の作業量 |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [信頼済みデーモンまたは非対話型クライアント アプリケーション](./how-to-secure-daemon-app.md)        | 共有キー     | 該当なし           | Medium             | 高               |
| [信頼済みデーモンまたは非対話型クライアント アプリケーション](./how-to-secure-daemon-app.md)        | Azure AD       | 高          | 低                | Medium             |
| [対話型のシングル サインオンを使用する Web シングル ページ アプリケーション](./how-to-secure-spa-users.md) | Azure AD       | 高          | Medium             | Medium             |
| [非対話型サインオンを使用する Web シングル ページ アプリケーション](./how-to-secure-spa-app.md)      | Azure AD       | 高          | Medium             | Medium             |
| [対話型シングル サインオンを使用する Web アプリケーション](./how-to-secure-webapp-users.md)          | Azure AD       | 高          | 高               | Medium             |
| [IoT デバイスまたは入力の制約付きデバイス](./how-to-secure-device-code.md)                     | Azure AD       | 高          | Medium             | Medium             |

## <a name="view-built-in-azure-maps-role-definitions"></a>組み込みの Azure Maps ロールの定義を表示する

組み込みの Azure Maps ロールの定義を表示するには:

1. 左側のウィンドウで **[アクセス制御 (IAM)]** を選択します。

2. **[ロール]** タブを選択します。

3. 検索ボックスに「**Azure Maps**」と入力します。

結果には、Azure Maps に使用できる組み込みロールの定義が表示されます。

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-role-definitions.png" alt-text="組み込みの Azure Maps ロールの定義を表示する。":::

## <a name="view-role-assignments"></a>ロールの割り当てを表示する

Azure Maps に対するアクセス権が付与されているユーザーやアプリを表示するには、 **[アクセス制御 (IAM)]** に移動します。 そこで、 **[ロールの割り当て]** を選択してから、「**Azure Maps**」でフィルター処理します。

1. 左側のウィンドウで **[アクセス制御 (IAM)]** を選択します。

2. **[ロールの割り当て]** タブを選択します。

3. 検索ボックスに「**Azure Maps**」と入力します。

結果には、現在の Azure Maps ロールの割り当てが表示されます。

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-amrbac.png" alt-text="アクセス権が付与されている組み込みビューのユーザーとアプリを表示する。":::

## <a name="request-tokens-for-azure-maps"></a>Azure Maps のトークンを要求する

Azure AD トークン エンドポイントからトークンを要求します。 Azure AD 要求で、次の詳細を使用します。

| Azure 環境      | Azure AD トークン エンドポイント             | Azure リソース ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Azure パブリック クラウド     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government クラウド | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Azure AD からユーザーやサービス プリンシパルのアクセス トークンを要求する方法の詳細については、「[Azure AD の認証シナリオ](../active-directory/develop/authentication-vs-authorization.md)」をご覧ください。  特定のシナリオを表示するには、[シナリオの表](./how-to-manage-authentication.md#choose-an-authentication-and-authorization-scenario)をご覧ください。

## <a name="manage-and-rotate-shared-keys"></a>共有キーの管理とローテーション

Azure Maps サブスクリプション キーは、Azure Maps アカウントのルート パスワードに似ています。 サブスクリプション キーは常に慎重に保護してください。 ご自身のキーを安全に管理およびローテーションするには、Azure Key Vault を使用します。 アクセス キーを他のユーザーに配布したり、ハードコーディングしたり、他のユーザーがアクセスできるプレーンテキストで保存したりしないでください。 ご自身のキーが侵害された可能性があると思われる場合は、そのキーをローテーションしてください。

> [!NOTE]
> 可能な場合は、共有キーではなく Azure AD を使用して、要求を承認することをお勧めします。 Azure AD は、共有キーよりも優れたセキュリティと使いやすさを備えています。

### <a name="manually-rotate-subscription-keys"></a>サブスクリプション キーを手動でローテーションする

Azure Maps アカウントのセキュリティを確保するために、お使いのサブスクリプション キーは定期的にローテーションすることをお勧めします。 可能であれば、Azure Key Vault を使用してアクセスキーを管理してください。 Key Vault を使用していない場合は、お使いのキーを手動でローテーションする必要があります。

キーをローテーションさせることができるように、2 つのサブスクリプション キーが割り当てられます。 2 つのキーにより、アプリケーションがプロセス全体を通じて Azure Maps へのアクセスを確実に維持できるようにします。

Azure portal で Azure Maps サブスクリプション キーをローテーションするには、次の手順に従います。

1. アプリケーション コードを、Azure Maps アカウントの 2 次キーを参照するように更新してデプロイします。
2. [Azure portal](https://portal.azure.com/) で、ご自身の Azure Maps アカウントに移動します。
3. **[設定]** で **[認証]** を選択します。
4. Azure Maps アカウントの主キーを再生成するには、主キーの隣にある **[再生成]** ボタンを選択します。
5. 新しい主キーを参照するようにアプリケーション コードを更新してデプロイします。
6. 同様に、 2 次キーを再生成します。

> [!WARNING]
> すべてのアプリケーションで、同時にいずれかのキーのみを使用することをお勧めします。 キー 1 を使用する場所とキー 2 を使用する場所がある場合、キーをローテーションすると、一部のアプリケーションがアクセス権を失います。

## <a name="next-steps"></a>次のステップ

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示すサンプルを確認します。

> [!div class="nextstepaction"]
> [Azure AD 認証のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
