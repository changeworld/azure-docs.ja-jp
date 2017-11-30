---
title: "カスタマイズ: Azure AD SSPR | Microsoft Docs"
description: "Azure AD のセルフサービスによるパスワード リセットのカスタマイズ オプション"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: e8cf0ce8ed154a7e42b885e605dcdf37827a6447
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2017
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>セルフ サービスによるパスワードのリセットのための Azure AD 機能のカスタマイズ

Azure Active directory (Azure AD) のセルフサービスによるパスワードのリセット (SSPR) をデプロイする IT プロフェッショナルは、ユーザーのニーズに合わせてエクスペリエンスをカスタマイズできます。

## <a name="customize-the-contact-your-administrator-link"></a>"管理者に問い合わせてください" リンクのカスタマイズ

SSPR が有効になっていない場合でも、ユーザーにはパスワード リセット ポータルに "管理者に問い合わせてください" リンクが表示されます。 ユーザーがこのリンクを選択した場合、次のいずれかが実行されます:
   * メールが管理者に送信され、ユーザーのパスワードの変更についての支援が求められます。 
   * 支援のために指定した URL がユーザーに送信されます。 

この連絡先は、ユーザーがサポートのために既に使用しているメール アドレスや Web サイトなどに設定することをお勧めします。

![連絡先][Contact]

この連絡先メールは、次の受信者に次の順序で送信されます。

1. **パスワード管理者**ロールが割り当てられている場合は、このロールを持つ管理者が通知を受け取ります。
2. パスワード管理者が割り当てられていない場合は、**ユーザー管理者**ロールを持つ管理者が通知を受け取ります。
3. 上記のどちらのロールも割り当てられていない場合は、**グローバル管理者**が通知を受け取ります。

どの場合も、最大 100 人の受信者が通知を受け取ります。

さまざまな管理者ロールと、それらの管理者ロールを割り当てる方法の詳細については、「[Azure Active Directory での管理者ロールの割り当て](active-directory-assign-admin-roles-azure-portal.md)」を参照してください。

### <a name="disable-contact-your-administrator-emails"></a>"管理者に問い合わせてください" メールを無効にする

組織でパスワードのリセット要求を管理者に通知しないようにする場合は、以下の構成を有効にできます。

* すべてのエンド ユーザーに対してセルフ サービスによるパスワードのリセットを有効にします。 このオプションは **[パスワード リセット]** > **[プロパティ]** の下にあります。
  
  ユーザーに自分のパスワードをリセットさせたくない場合は、アクセスの対象を空のグループにすることができます。 *このオプションは推奨されません。*
* ヘルプデスクのリンクをカスタマイズして、ユーザーがヘルプを得るために使用できる Web URL または mailto: アドレスを指定します。 このオプションは **[パスワード リセット]** > **[カスタマイズ]** > **[カスタム ヘルプデスクの電子メールまたは URL]** の下にあります。

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>SSPR の AD FS サインイン ページをカスタマイズする

Active Directory Federation Services (AD FS) 管理者は、[サインイン ページの説明の追加](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)の記事のガイダンスを参考にして、サインイン ページへのリンクを追加できます。

AD FS サインイン ページにリンクを追加するには、AD FS サーバーで次のコマンドを使用します。 ユーザーは、このページを使用して、SSPR ワークフローを入力できます。

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>サインイン ページとアクセス パネルの外観のカスタマイズ

サインイン ページをカスタマイズできます。 会社のブランドに合ったイメージと一緒に表示されるロゴを追加できます。

選択したグラフィックスは、次の状況で表示されます。

* ユーザーが自分のユーザー名を入力した後
* ユーザーがカスタマイズされた URL にアクセスした場合:
    * パスワード リセット ページに *whr* パラメーターを渡すことによって (例: "https://login.microsoftonline.com/?whr=contoso.com")
    * パスワード リセット ページに *username* パラメーターを渡すことによって (例: "https://login.microsoftonline.com/?username=admin@contoso.com")

### <a name="graphics-details"></a>グラフィックスの詳細

次の設定を使用して、サインイン ページの表示特性を変更できます。 **[Azure Active Directory]** > **[会社のブランド]** > **[会社のブランドを編集する]** に移動します。

* サインイン ページのイメージは、1420 x 1200 ピクセルで 500 KB 未満の .png ファイルまたは .jpg ファイルにする必要があります。 最良の結果を得るために、200 KB 程度に維持することをお勧めします。
* サインイン ページの背景色は接続の待機時間が長い場合に使用され、RGB 16 進形式でなければなりません。
* バナー イメージは、60 x 280 ピクセルで 10 KB 以内の .png ファイルまたは .jpg ファイルにする必要があります。
* 正方形のロゴ (標準および濃色のテーマ) は、240 x 240 (サイズ変更可能) ピクセルで 10 KB 以内の .png ファイルまたは .jpg ファイルにする必要があります。

### <a name="sign-in-text-options"></a>サインイン テキストのオプション

次の設定を使用して、組織に関連するテキストをサインイン ページに追加することができます。 **[Azure Active Directory]** > **[会社のブランド]** > **[会社のブランドを編集する]** に移動します。

* **ユーザー名のヒント**: *someone@example.com* のテキスト例をユーザーにより適したものに置き換えます。 内部ユーザーと外部ユーザーをサポートする場合は、既定のヒントのままにしておくことをお勧めします。
* **サインイン ページのテキスト**: 最大 256 文字の長さまで指定できます。 このテキストは、ユーザーがオンラインでサインインする任意の場所と、Windows 10 の Azure AD Workplace 参加エクスペリエンスに表示されます。 このテキストは、使用条件、手順、およびヒントをユーザーに示すために使用します。 

   >[!IMPORTANT]
   >サインイン ページは誰でも表示できるため、ここには機密情報を入れないでください。
   >

### <a name="the-keep-me-signed-in-disabled-setting"></a>"[サインインしたままにする] は無効" 設定

**[サインインしたままにする] は無効**オプションを使用すると、ユーザーは、ブラウザー ウィンドウを閉じて開き直した場合でもサインインしたままにできます。 このオプションはセッションの有効期間には影響しません。 **[Azure Active Directory]** > **[会社のブランド]** > **[会社のブランドを編集する]** に移動します。

SharePoint Online と Office 2010 の一部の機能は、ユーザーのこのチェック ボックスを選択する機能に依存します。 このオプションが非表示の場合は、ユーザーに追加の予期しないサインイン画面が表示されることがあります。

### <a name="directory-name"></a>ディレクトリ名

**[Azure Active Directory]** > **[プロパティ]** で、ディレクトリ名属性を変更できます。 ポータルと自動通信で表示される組織のフレンドリ名を表示できます。 このオプションは、以下の形式の自動メールで最も多く表示されます。

* メールのフレンドリ名 (例: "Microsoft on behalf of CONTOSO demo")
* メールの件名行 (例: "CONTOSO demo account email verification code")

## <a name="next-steps"></a>次のステップ

* [SSPR のロールアウトを適切に完了する方法。](active-directory-passwords-best-practices.md)
* [パスワードのリセットまたは変更](active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](active-directory-passwords-licensing.md)
* [SSPR が使用するデータと、ユーザー用に設定するデータ。](active-directory-passwords-data.md)
* [ユーザーが使用できる認証方法。](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR のポリシー オプション。](active-directory-passwords-policy.md)
* [パスワード ライトバックと、それが必要な理由。](active-directory-passwords-writeback.md)
* [SSPR でアクティビティをレポートする方法。](active-directory-passwords-reporting.md)
* [SSPR のすべてのオプションとその意味。](active-directory-passwords-how-it-works.md)
* [エラーが発生していると思われる場合のSSPR のトラブルシューティング方法。](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

[Contact]: ./media/active-directory-passwords-customize/sspr-contact-admin.png "管理者に連絡してパスワードのリセットを依頼するメールの例"
