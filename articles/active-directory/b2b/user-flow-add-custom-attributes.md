---
title: Azure AD ユーザー フローのカスタム属性を追加する
description: セルフサービス サインアップのユーザー フローの属性をカスタマイズする方法について説明します。
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594711"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>ユーザー フローのカスタム属性を定義する (プレビュー)
|     |
| --- |
| カスタム ユーザー属性の機能は、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

アプリケーションごとに、サインアップ中に収集する情報の要件が異なる場合があります。 Azure AD には、名、姓、市区町村、郵便番号など、属性に格納された一連の情報が組み込みで用意されています。 Azure AD では、外部ユーザーがユーザー フローを通じてサインアップするときにゲスト アカウントに格納される一連の属性を拡張できます。

Azure portal でカスタム属性を作成し、セルフサービス サインアップのユーザー フローでそれらを使用できます。 また、[Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api) を使用してこれらの属性を読み書きすることもできます。 Microsoft Graph API では、拡張属性を使用したユーザーの作成と更新がサポートされています。 Graph API の拡張属性には、`extension_<Application-client-id>_attributename` という規則を使って名前が付けられます。 次に例を示します。

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

`<Application-client-id>` は、 **[アプリの登録]** ページの **[アプリケーション (クライアント) ID]** の横で確認できます。 この ID は、ご自身のテナントに固有のものです。

## <a name="create-a-custom-attribute"></a>カスタム属性を作成する

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[Custom user attributes (Preview)]\(カスタムのユーザー属性 (プレビュー)\)** を選択します。 使用可能なユーザー属性が一覧表示されます。

   ![サインアップ用のユーザー属性の選択](media/user-flow-add-custom-attributes/user-attributes.png)

5. 属性を追加するには、 **[追加]** を選択します。
6. **[属性の追加]** ペインで、次の値を入力します。

   - **[名前]** - カスタム属性の名前を指定します ("ShoeSize" など)。
   - **[データ型]** - データ型を選択します ( **[String]** 、 **[Boolean]** 、または **[Int]** )。
   - **[説明]** - 必要に応じて、内部使用のためにカスタム属性の説明を入力します。 この説明は、ユーザーには表示されません。

   ![属性の追加](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. **［作成］** を選択します

これで、このカスタム属性がユーザー属性の一覧やユーザー フローで使用できるようになります。 カスタム属性が作成されるのは、いずれかのユーザー フローで初めて使用されるときだけであり、ユーザー属性の一覧に追加するときではありません。

新しく作成したカスタム属性を使用するユーザー フローを使用して新しいユーザーを作成したら、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) でそのオブジェクトを照会できます。 これで、**ShoeSize** がサインアップ中に収集される属性の一覧や、アプリケーションに送り返されるトークンに表示されるようになります。 アプリケーションに返されるトークンにこれらの要求を含める方法の詳細については、「[ディレクトリ拡張機能の省略可能な要求の構成](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims)」をご覧ください

## <a name="next-steps"></a>次のステップ

[セルフサービス サインアップのユーザー フローをアプリに追加する](self-service-sign-up-user-flow.md)