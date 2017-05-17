---
title: "カスタマイズ: Azure AD SSPR | Microsoft Docs"
description: 
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: joflore
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 41da786ed13308a1fc030e6b02fac3d8fbca9e61
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---
# <a name="customize-azure-ad-functionality-for-self-service-password-reset"></a>セルフ サービスによるパスワードのリセットのための Azure AD 機能のカスタマイズ

Azure AD 内で変更できるオプションは多数あり、その多くはセルフ サービスによるパスワードのリセット (SSPR) に関連しています。このガイドでは、SSPR で何ができるかについて説明します。

## <a name="customize-the-contact-your-administrator-link"></a>[管理者に問い合わせてください] リンクのカスタマイズ

SSPR が有効になっていない場合でも、ユーザーにはパスワード リセット ポータルに [管理者に問い合わせてください] リンクが表示されます。  このリンクをクリックすると、ユーザーのパスワードを変更についての支援を求めるメールが管理者に送信されます。 このメールは、次の受信者に次の順序で送信されます。

1. **パスワード管理者**ロールが割り当てられている場合は、このロールを持つ管理者が通知を受け取ります
2. パスワード管理者が割り当てられていない場合は、**ユーザー管理者**ロールを持つ管理者が通知を受け取ります
3. 前記のロールのどちらも割り当てられていない場合は、**グローバル管理者**が通知を受け取ります

どの場合も、合計 100 人までの受信者が通知を受け取ります。

### <a name="disable-contact-your-administrator-emails"></a>[管理者に問い合わせてください] メールを無効にする

組織でパスワードのリセット要求を管理者に通知しないようにする場合は、次の構成を有効にできます

* すべてのエンド ユーザーに対してセルフ サービスによるパスワードのリセットを有効にします。 このオプションは、**[パスワード リセット]**、**[プロパティ]** の下にあります。
    * ユーザーに自分のパスワードをリセットさせたくない場合は、アクセスの対象を空のグループにすることができますが、このオプションはお勧めできません**。
* ヘルプデスクのリンクをカスタマイズして、ユーザーがヘルプを得るために使用できる Web URL または mailto: アドレスを指定します。 このオプションは、**[パスワード リセット]**、**[カスタマイズ]**、**[カスタム ヘルプデスクの電子メールまたは URL]** の下にあります。

## <a name="customize-the-sign-in-and-access-panel-look-and-feel"></a>サインインとアクセス パネルの外観のカスタマイズ

ユーザーがログイン ページにアクセスするときにサインイン ページのイメージと共に表示されるロゴを、会社のブランドに合わせてカスタマイズすることができます。

これらのグラフィックスは、次の状況で表示されます。

* ユーザーが自分のユーザー名を入力した後
* ユーザーがカスタマイズされた url にアクセスしたとき
    * パスワード リセット ページに "whr" パラメーターを渡すことによって (例: "https://login.microsoftonline.com/?whr=contoso.com")
    * パスワード リセット ページに "username" パラメーターを渡すことによって (例: "https://login.microsoftonline.com/?username=admin@contoso.com")

### <a name="graphics-details"></a>グラフィックスの詳細

次の設定により、サインイン ページの表示特性を変更することができます。これは、**[Azure Active Directory]**、**[会社のブランド]**、**[会社のブランドを編集する]** の下にあります。

* サインイン ページのイメージは、1420x1200 ピクセルで 500 KB 未満の PNG ファイルまたは JPG ファイルにしてください。 最良の結果を得るためには、約 200 KB のファイルにすることをお勧めします。
* 接続の待機時間が長いときに使用されるサインイン ページの背景色は、RGB の 16 進形式である必要があります。
* バナー イメージは、60x280 ピクセルで 10 KB 未満の PNG ファイルまたは JPG ファイルにしてください。
* 四角形のロゴ (標準的な濃い色のテーマ) は、240x240 (サイズ変更可能) で 10 KB 未満の PNG または JPG にしてください。

### <a name="sign-in-text-options"></a>サインイン テキストのオプション

次の設定により、組織に関連するテキストをサインイン ページに追加することができます。 これらの設定は、**[Azure Active Directory]**、**[会社のブランド]**、**[会社のブランドを編集する]** の下にあります。

* **[ユーザー名のヒント]** は、someone@example.com のサンプルのテキストをユーザーにより適したテキストに置き換えます。内部のユーザーと外部のユーザーをサポートしている場合は、既定のままにすることをお勧めします。
* **[サインイン ページのテキスト]** の最大の長さは 256 文字です。 このテキストは、ユーザーがオンラインでログインする任意の場所と、Windows 10 の Azure AD 参加エクスペリエンスに表示されます。 このテキストは、使用条件、手順、およびヒントをユーザーに示すために使用します。 **サインイン ページは誰でも表示できるため、ここには機密情報を入れないでください。**

### <a name="keep-me-signed-in-disabled"></a>[サインインしたままにする] は無効

オプション "[サインインしたままにする] は無効" により、ユーザーがブラウザー ウィンドウを閉じてから開き直したときでもサインインしたままになり、セッションの有効期間には影響しません。 この設定は、**[Azure Active Directory]**、**[会社のブランド]**、**[会社のブランドを編集する]** の下にあります。

SharePoint Online と Office 2010 の一部の機能は、ユーザーがこのチェック ボックスをオンにできるかどうかに依存します。 このオプションが非表示の場合は、ユーザーに追加の予期しないサインイン画面が表示されることがあります。

### <a name="directory-name"></a>ディレクトリ名

**[Azure Active Directory]**、**[プロパティ]** の下にある名前属性を変更して、ポータルおよび自動化された通信で組織のフレンドリ名を表示することができます。 このオプションは、以下の形式の自動メールの形式で最も多く表示されます

* “Microsoft on behalf of CONTOSO demo (CONTOSO を代表する Microsoft のデモ)” というメールのフレンドリ名
* “CONTOSO demo account email verification code (CONTOSO デモアカウントのメール確認コード)” というメールの件名行

## <a name="next-steps"></a>次のステップ

次のリンク先では、Azure AD を使用したパスワードのリセットに関する追加情報が得られます。

* [**クイック スタート**](active-directory-passwords-getting-started.md) - Azure AD のセルフ サービスによるパスワードのリセットの管理を始めることができます 
* [**ライセンス**](active-directory-passwords-licensing.md) - Azure AD のライセンスを構成します
* [**データ**](active-directory-passwords-data.md) - パスワード管理に必要なデータとその使用方法がわかります
* [**展開**](active-directory-passwords-best-practices.md) - ここで見つかるガイダンスを使用してユーザーに対する SSPR を計画してデプロイできます
* [**ポリシー**](active-directory-passwords-policy.md) - Azure AD のパスワード ポリシーを把握し、設定します
* [**パスワード ライトバック**](active-directory-passwords-writeback.md) - オンプレミスのディレクトリでのパスワード ライトバックのしくみ
* [**レポート**](active-directory-passwords-reporting.md) - ユーザーが SSPR 機能にアクセスしたかどうかや、アクセスしたタイミングと場所を検出します
* [**技術的詳細**](active-directory-passwords-how-it-works.md) - しくみを詳しく説明しています
* [**よく寄せられる質問**](active-directory-passwords-faq.md) - 方法は? なぜですか? 何ですか? どこですか? 誰ですか? いつですか? - ずっと確認したかった質問に対する回答
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - SSPR の一般的な問題を解決する方法について説明しています


