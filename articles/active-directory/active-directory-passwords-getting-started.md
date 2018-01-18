---
title: "セルフサービスによるパスワードのリセットのクイック スタート - Azure Active Directory"
description: "Azure AD のセルフ サービスによるパスワードのリセットを迅速にデプロイする"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: 88123cead40968ebf7327b81f94233529f97aa2f
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2018
---
# <a name="azure-ad-self-service-password-reset-rapid-deployment"></a>Azure AD のセルフ サービスによるパスワード リセットの迅速なデプロイ

> [!IMPORTANT]
> **サインインに問題がありますか?** その場合、[Azure AD パスワードを忘れた場合](active-directory-passwords-update-your-own-password.md)に関するページを参照してください。

セルフ サービスによるパスワードのリセット (SSPR) は、ユーザーが自分のパスワードやアカウントのリセットまたはロック解除を行うことができるようにする簡単な方法を IT 管理者に提供します。 このシステムには、ユーザーがいつシステムにアクセスしたかを追跡する詳細なレポートと、誤用または悪用について警告する通知が用意されています。

このガイドでは、試用版またはライセンス版の Azure Active Directory (Azure AD) テナントが既に稼働していることを前提としています。 Azure AD の設定に関するヘルプが必要な場合は、[Azure AD の概要](get-started-azure-ad.md)に関する記事を参照してください。

## <a name="enable-sspr-for-your-azure-ad-tenant"></a>Azure AD テナントの SSPR の有効化

1. 既存の Azure AD テナントから **[パスワードのリセット]** を選択します。

2. **[プロパティ]** ページの **[セルフ サービスによるパスワードのリセットが有効]** オプションで、次のいずれかを選択します。
   * **[なし]**: どのユーザーも SSPR 機能を使用できません。
   * **[選択済み]**: 選択した特定の Azure AD グループのメンバーのみが SSPR 機能を使用できます。 この機能を概念実証のためにデプロイする場合、ユーザー グループを定義してこの設定を使用することをお勧めします。
   * **[すべて]**: Azure AD テナントにアカウントを持つすべてのユーザーが SSPR 機能を使用できます。 この設定は、概念実証の完了後、この機能をテナント全体にデプロイする準備ができたときに使用することをお勧めします。

   > [!IMPORTANT]
   > Azure Administrator アカウントには、このオプションの設定に関係なく、パスワードをリセットする機能が常にあります。 

3. **[認証方法]** ページで、以下を選択します。
   * **[リセットのために必要な方法の数]**: 最小で 1 つ、最大で 2 つサポートしています。
   * **[ユーザーが使用できる方法]**: 少なくとも 1 つ必要ですが、追加の方法があってもかまいません。
      * **[電子メール]**: ユーザーが構成した認証用メール アドレスにコードが送信されます。
      * **[携帯電話]**: ユーザーは、構成した携帯電話番号への通話またはテキスト メッセージのどちらでコードを受け取るかを選択できます。
      * **[会社電話]**: ユーザーは、構成した会社の電話番号への通話でコードを受け取ります。
      * **[セキュリティの質問]**: 管理者が選択する必要があります。
         * **[登録する必要がある質問の数]**: 登録を完了するための最小の数です。 ユーザーはより多くの質問に答えることを選んで、出問元となる一連の質問を作成できます。 このオプションでは 3 ～ 5 個の質問を設定できます。これは、パスワードのリセットに必要な質問の数以上にする必要があります。 セキュリティの質問を選択する際に **[カスタム]** ボタンを選択した場合、カスタムの質問を追加することができます。
         * **[リセットに必要な質問の数]**: ユーザーがパスワードをリセットまたはロック解除するために正解する必要がある質問の数を 3 ～ 5 の範囲で設定できます。
            
    ![認証][Authentication]

4. 推奨: **[カスタマイズ]** では、定義したページまたはメール アドレスを指すよう **[管理者に連絡してください。]** リンクを変更できます。 このリンクは、既にユーザーがサポートの質問に使用しているメール アドレスや Web サイトなどに設定することをお勧めします。

5. 省略可能: **[登録]** ページには、管理者向けのオプションが表示されます。
   * サインイン時にユーザーに登録を求める。
   * ユーザーが認証情報を再確認するように求められるまでの日数を設定する。

6. 省略可能: **[通知]** ページには、管理者向けのオプションが表示されます。
   * パスワードのリセットについてユーザーに通知する。
   * 他の管理者が自分のパスワードをリセットしたときに、すべての管理者に通知する。

この時点で、Azure AD テナントの SSPR が構成できました。 ユーザーは「[セルフサービスによるパスワードのリセットを登録する](active-directory-passwords-reset-register.md)」および[パスワードのリセットまたは変更](active-directory-passwords-update-your-own-password.md)に関するページの手順に従って、管理者による介入なしでパスワードを更新できます。 クラウドのみの場合、ここでやめることができます。 または、引き続き次のセクションで、オンプレミスの Active Directory ドメインへのパスワード同期を構成できます。

> [!TIP]
> Microsoft では Azure 管理者アカウントに対して強力な認証要件を適用しているため、管理者ではなくユーザーで SSPR をテストします。 管理者のパスワード ポリシーの詳細については、[パスワード ポリシー](active-directory-passwords-policy.md#administrator-password-policy-differences)に関する記事を参照してください。

## <a name="configure-synchronization-to-an-existing-identity-source"></a>既存の ID ソースへの同期の構成

Azure AD へのオンプレミスの ID 同期を有効にするには、組織のサーバーに [Azure AD Connect](./connect/active-directory-aadconnect.md) をインストールして構成する必要があります。 このアプリケーションは、既存の ID ソースから Azure AD テナントへのユーザーとグループの同期を処理します。 詳細については、「

* [DirSync または Azure AD Sync から Azure AD Connect へのアップグレード](./connect/active-directory-aadconnect-dirsync-deprecated.md)
* [簡単設定を使用した Azure AD Connect の開始](./connect/active-directory-aadconnect-get-started-express.md)
* [パスワード ライトバックを構成](active-directory-passwords-writeback.md#configure-password-writeback)し、パスワードを Azure AD からオンプレミス ディレクトリに書き戻します。

### <a name="on-premises-policy-change"></a>オンプレミスのポリシーの変更

オンプレミスの Active Directory ドメインからユーザーを同期して、すぐにそのユーザーがパスワードをリセットできるようにするには、オンプレミスのパスワード ポリシーを次のように変更します。

1. **[コンピューターの構成]** > **[ポリシー]** > **[Windows 設定]** > **[セキュリティ設定]** > **[アカウント ポリシー]** > **[パスワード ポリシー]** の順に移動します。

2. **[パスワードの変更禁止期間]** を **[0 日]** に設定します。

このセキュリティ設定では、ユーザーがパスワードを変更するにはどのくらいの期間 (日数) それを使用する必要があるかを決定します。 最小の使用設定を **[0 日]** に設定すると、ユーザーはサポート チームがパスワードを変更した際に SSPR を使用できます。

![ポリシー][Policy]

## <a name="disable-self-service-password-reset"></a>セルフサービスのパスワード リセットの無効化

セルフサービスのパスワード リセットは簡単に無効にできます。 Azure AD テナントを開いて **[パスワードのリセット]** > **[プロパティ]** の順に移動し、**[セルフ サービスによるパスワードのリセットが有効]** の **[なし]** を選択します。

### <a name="learn-more"></a>詳細情報
次の記事では、Azure AD によるパスワードのリセットに関する追加情報が得られます。

* [SSPR のロールアウトを正常に完了する方法](active-directory-passwords-best-practices.md)
* [パスワードのリセットまたは変更](active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](active-directory-passwords-licensing.md)
* [SSPR が使用するデータと、ユーザー用に事前設定が必要なデータ。](active-directory-passwords-data.md)
* [ユーザーが使用できる認証方法。](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR のポリシー オプション。](active-directory-passwords-policy.md)
* [パスワード ライトバックの概要とその必要性。](active-directory-passwords-writeback.md)
* [SSPR でアクティビティをレポートする方法。](active-directory-passwords-reporting.md)
* [SSPR のすべてのオプションとその意味。](active-directory-passwords-how-it-works.md)
* [不具合が発生していると思われる場合のSSPR のトラブルシューティング方法。](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、ユーザーのためにセルフ サービスによるパスワードのリセットを構成する方法について説明しました。 これらの手順を完了するには、Azure Portal に移動します。

> [!div class="nextstepaction"]
> [セルフ サービスのパスワード リセットを有効にする](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)

[Authentication]: ./media/active-directory-passwords-getting-started/sspr-authentication-methods.png "Azure AD の使用できる認証方法と必要な数量"
[Policy]: ./media/active-directory-passwords-getting-started/password-policy.png "オンプレミスのパスワード グループ ポリシーを 0 日に設定"

