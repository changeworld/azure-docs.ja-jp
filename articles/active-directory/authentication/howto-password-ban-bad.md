---
title: Azure AD でパスワードを禁止する方法
description: Azure AD でのパスワードの動的禁止を使用して環境の脆弱なパスワードを禁止する
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: 30d8260d78b3b46a9f4caea63f6bed818935a9a1
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158745"
---
# <a name="configuring-the-custom-banned-password-list"></a>カスタムの禁止パスワード リストを構成する

|     |
| --- |
| Azure AD パスワード保護と禁止パスワードのカスタム リストは、Azure Active Directory のパブリック プレビュー機能です。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。|
|     |

多くの組織では、ユーザーが学校、スポーツ チーム、有名人などの一般的な地域の用語を使用して推測しやすいパスワードを作成していることがわかっています。 Microsoft のカスタム禁止パスワード リストでは、ユーザーと管理者がパスワードを変更またはリセットしようとしたときに、グローバル禁止パスワード リストに加えて、評価およびブロックする文字列を追加することができます。

## <a name="add-to-the-custom-list"></a>カスタム リストに追加する

カスタム禁止パスワード リストを構成するには、Azure Active Directory Premium P1 または P2 ライセンスが必要です。 Azure Active Directory のライセンスの詳細については、[Azure Active Directory の価格ページ](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

1. [Azure portal](https://portal.azure.com) にサインインし、**[Azure Active Directory]**、**[認証方法]**、**[Password protection (Preview)]\(パスワード保護 (プレビュー)\)** の順に参照します。
1. オプション **[Enforce custom list]\(カスタム リストを適用する\)** を **[はい]** に設定します。
1. **[Custom banned password list]\(カスタム禁止パスワード リスト\)** に文字列 (1 行に 1 文字列) を追加します。
   * カスタム禁止パスワード リストには、最大 1,000 語を含めることができます。
   * カスタム禁止パスワード リストでは、大文字と小文字は区別されません。
   * カスタム禁止パスワード リストでは、一般的な文字の代替が考慮されています。
      * 例: "o" と "0"、"a" と "\@\"
   * 最短文字数は 4 文字で、最長文字数は 16 文字です。
1. すべての文字列を追加したら、**[保存]** をクリックします。

> [!NOTE]
> カスタム禁止パスワード リストの更新が適用されるまでに数時間かかることがあります。

![Azure portal の [認証方法] でカスタムの禁止パスワード リストを変更する](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>動作のしくみ

ユーザーまたは管理者が Azure AD のパスワードをリセットまたは変更するたびに、禁止パスワード リストを介してリストに存在しないことが確認されます。 このチェックは、Azure AD を使用して設定または変更されたパスワードに含まれています。

## <a name="what-do-users-see"></a>ユーザーに表示される画面

ユーザーがパスワードを禁止されるパスワードにリセットしようとすると、次のエラー メッセージが表示されます。

残念ながら、パスワードを簡単に推測できる単語、語句、またはパターンが含まれています。 別のパスワードで再実行してください。

## <a name="next-steps"></a>次の手順

[禁止パスワード リストの概念の概要](concept-password-ban-bad.md)

[Azure AD パスワード保護の概念の概要](concept-password-ban-bad-on-premises.md)

[禁止パスワード リストとのオンプレミス統合を有効にする](howto-password-ban-bad-on-premises.md)
