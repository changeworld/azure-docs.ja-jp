---
title: "セルフサービスによるパスワードのリセットのデプロイ ガイド - Azure Active Directory"
description: "Azure AD のセルフ サービスによるパスワードのリセットを正常に展開するためのヒント"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 063c92a006696afb998799c312515c79c19a48f4
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>セルフサービスによるパスワードのリセットを適切にロールアウトする方法

Azure Active Directory (Azure AD) のセルフサービスによるパスワード リセット (SSPR) 機能が円滑にロールアウトされるように、ほとんどのお客様は次の手順を実行します。

1. [ディレクトリでパスワードのリセットを有効にします](active-directory-passwords-getting-started.md)。
2. [パスワード ライトバック用にオンプレミスの Active Directory のアクセス許可を構成します](active-directory-passwords-writeback.md#active-directory-permissions)。
3. [パスワード ライトバックを構成](active-directory-passwords-writeback.md#configure-password-writeback)し、パスワードを Azure AD からオンプレミス ディレクトリに書き戻します。
4. [必要なライセンスを割り当てて、確認します](active-directory-passwords-licensing.md)。
5. 段階的なロールアウトを実行するかどうかを決定します。 SSPR を段階的にロールアウトする場合は、特定のグループでプログラムを試験運用できるように、アクセスをそのユーザー グループだけに制限できます。 特定のグループにロールアウトするには、**[セルフ サービスによるパスワードのリセットが有効]** スイッチを **[選択済み]** に設定し、パスワードのリセットを有効にするセキュリティ グループを選択します。 
6. 会社電話、携帯電話、連絡用メール アドレスなど、ユーザーの登録に必要な[認証データ](active-directory-passwords-data.md)を設定します。
7. [Azure AD サインイン エクスペリエンスをカスタマイズして、会社のブランドを含めます](active-directory-passwords-customize.md)。
8. SSPR の使い方をユーザーに教えます。 パスワードの登録方法とリセット方法が記載されている手順書をユーザーに送信します。
9. いつ登録を強制するかを決定します。 任意の時点で登録を強制することができます。 一定期間後に認証情報の再確認をユーザーに求めることもできます。
10. レポート機能を使用します。 時間の経過と共に、[Azure AD が提供するレポート機能](active-directory-passwords-reporting.md)を使用して、ユーザーの登録と使用の状況を確認することができます。
11. パスワードのリセットを有効にします。 準備ができたら、**[セルフ サービスによるパスワードのリセットが有効]** スイッチを **[すべて]** に設定することで、すべてのユーザーのパスワードのリセットを有効にします。 

   > [!NOTE]
   > このオプションを特定のグループから全員に変更しても、ユーザーがテスト グループの一部として登録した既存の認証データは無効になりません。 構成が済んでいて有効な認証データが登録されているユーザーは、引き続き正常に機能します。

12. [Windows 10 ユーザーがログイン画面でパスワードをリセットできるようにします](active-directory-passwords-login.md)。

   > [!IMPORTANT]
   > Microsoft は Azure 管理者アカウントに対して強力な認証要件を適用しているため、管理者ではなくユーザーで SSPR をテストします。 管理者のパスワード ポリシーの詳細については、[パスワード ポリシー](active-directory-passwords-policy.md#administrator-password-policy-differences)に関する記事を参照してください。

## <a name="email-based-rollout"></a>電子メール ベースの展開

多くのお客様は、ユーザーに SSPR の使用を促す最も簡単な方法は、電子メール キャンペーンで簡単な使用手順を配布することです。 [ロールアウトに役立つテンプレートとして使用できる 3 つの簡単な電子メールを作成しました。](https://onedrive.live.com/?authkey=%21AD5ZP%2D8RyJ2Cc6M&id=A0B59A91C740AB16%2125063&cid=A0B59A91C740AB16)

* **準備中**: ユーザーに必要な作業を通知するために、ロールアウトの数週間前または数日前に使用する電子メール テンプレートです。
* **利用開始**: ユーザーに認証データの登録と確認を促すために、プログラム開始当日に使用する電子メール テンプレートです。 ユーザーは、今登録すると、SSPR が必要になったときに利用できます。
* **サインアップ リマインダー**: ユーザーに認証データの登録と確認を行うよう念を押すために、デプロイの数日から数週間後に使用する電子メール テンプレートです。

![電子メール][Email]

## <a name="create-your-own-password-portal"></a>独自のパスワードのポータルを作成する

多くのお客様は、Web ページをホストし、ルート DNS エントリ (https://passwords.contoso.com など) を作成することを選択しています。このページには、以下の情報へのリンクを設定します。

* [Azure AD のパスワードのリセット ポータル - https://aka.ms/sspr](https://aka.ms/sspr)
* [Azure AD パスワード リセットの登録ポータル - https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Azure AD のパスワード変更ポータル - https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* その他の組織固有の情報

ユーザーがこのサービスの使用を必要とするときにアクセスできる、覚えやすいブランド化された URL を、送信する任意の電子メールまたは広告に含めることができます。 組織のニーズに応じて使用およびカスタマイズできる[パスワードのリセットのサンプル ページ](https://github.com/ajamess/password-reset-page)をご用意しました。

## <a name="use-enforced-registration"></a>強制登録を使用する

ユーザーにパスワードのリセットを登録してほしい場合は、ユーザーが Azure AD を通じてサインインしたときに登録を要求できます。 このオプションを有効にするには、ディレクトリの **[パスワードのリセット]** ウィンドウで **[登録]** タブの **[サインイン時にユーザーに登録を求めますか]** オプションを有効にします。

管理者は、特定の期間の経過後、ユーザーに再登録を要求することができます。 **[ユーザーが認証情報を再確認するように求められるまでの日数]** オプションを 0 ～ 730 日に設定できます。

このオプションを有効にすると、ユーザーがサインインしたときに、管理者がユーザーに認証情報を確認するよう求めていることを通知するメッセージが表示されます。

## <a name="populate-authentication-data"></a>認証データの設定

[ユーザーのために認証データを設定しておく](active-directory-passwords-data.md)必要があります。 そうすれば、ユーザーは、パスワードのリセットに登録しなくても SSPR を使用できます。 ユーザーは、定義されているパスワードのリセット ポリシーに準拠した認証データを指定していれば、自分のパスワードをリセットできます。

## <a name="disable-self-service-password-reset"></a>セルフサービスのパスワード リセットの無効化

セルフサービスのパスワード リセットは簡単に無効にできます。 Azure AD テナントを開いて **[パスワードのリセット]** > **[プロパティ]** の順に移動し、**[セルフ サービスによるパスワードのリセットが有効]** の **[なし]** を選択します。

## <a name="next-steps"></a>次の手順

* [パスワードのリセットまたは変更](active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](active-directory-passwords-licensing.md)
* [SSPR が使用するデータと、ユーザー用に設定するデータ。](active-directory-passwords-data.md)
* [SSPR のポリシー オプション。](active-directory-passwords-policy.md)
* [パスワード ライトバックと、それが必要な理由。](active-directory-passwords-writeback.md)
* [SSPR でアクティビティをレポートする方法。](active-directory-passwords-reporting.md)
* [SSPR のすべてのオプションとその意味。](active-directory-passwords-how-it-works.md)
* [エラーが発生していると思われる場合のSSPR のトラブルシューティング方法。](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

[Email]: ./media/active-directory-passwords-best-practices/sspr-emailtemplates.png "こうしたメール テンプレートを組織の要件に合わせてカスタマイズする"
