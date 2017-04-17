---
title: "Azure AD: パスワードのリセット | Microsoft Docs"
description: "セルフサービスによるパスワードのリセットを使用してアカウントへのアクセスを回復する"
services: active-directory
keywords: "Active Directory パスワード管理, パスワード管理, Azure AD セルフサービスによるパスワードのリセット, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 7ba69b18-317a-4a62-afa3-924c4ea8fb49
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 9b7735bbe8d3b2ffc78eba8f1489d70a02fe7342
ms.lasthandoff: 04/12/2017


---
# <a name="help-i-forgot-my-password"></a>パスワードを忘れた場合

手順 1. 焦らず対処する

以下のシナリオに該当する場合は、対処方法をご案内できます

* アカウントへのアクセス方法が不明で、パスワードも思い出せない
* パスワードが割り当てられておらず、管理者にこのページを参照するよう指示された

## <a name="unlock-your-account"></a>自分のアカウントのロックを解除する

アカウントをロック解除するためにこのページにアクセスした場合は、以下の手順に従ってください。 後述する手順 6. で **[新しいパスワードの選択]** が表示される場合は、アカウントをロック解除するか、パスワードを変更することでロックが解除された状態にすることができます。

## <a name="reset-my-password"></a>パスワードをリセットする

自分のアカウントに戻るには、以下の手順に従います。
1. 職場または学校のサインイン ページから **[アカウントにアクセスできませんか?]** リンクをクリックし、**[職場または学校アカウント]** をクリックするか、または[パスワードのリセット用ページ](https://passwordreset.microsoftonline.com/)に直接アクセスします

    ![アカウントにアクセスできない場合][Login]

2. 職場または学校の **[ユーザー ID]** を入力し、CAPTCHA チャレンジを受けて表示されたテキストを入力することで自分がロボットではないことを証明して、**[次へ]** をクリックします
3. 管理者による構成内容に応じて、以下の項目のうち 1 つ以上が表示されます
    * **[連絡用電子メール アドレスにメールを送信]** - 連絡用メール アドレスまたは認証用メール アドレス (選択した方) に 6 桁のコードが記載されたメールが送信されます。
    * **[携帯電話に SMS 送信]** - 携帯電話または認証用電話 (選択した方) に 6 桁のコードが SMS 送信されます。
    * **[携帯電話に発信]** - 携帯電話または認証用電話 (選択した方) への呼び出しが行われます。 # キーを押して呼び出しを確認します。
    * **[会社電話に発信]** - 会社電話への呼び出しが行われます。 # キーを押して呼び出しを確認します。
    * **[セキュリティの質問に回答する]** - 事前に登録済みのセキュリティの質問を表示し、回答を求めます。
4. 必須フィールドにチャレンジ情報への回答を入力し、**[次へ]** をクリックします

    ![認証データを確認する][Verification]

5. 管理者が追加の確認手段を求めている場合もあります。その場合は、別のオプションを選択して手順 4. を繰り返す必要があります
6. **[新しいパスワードの選択]** ページで、組織の要件を満たす新しいパスワードを入力し、パスワードを確認して **[完了]** をクリックします

    ![パスワードを変更する][Change]

7. **[パスワードがリセットされました]** と表示されたら、新しいパスワードでサインインできます。

    ![パスワードがリセットされました][Complete]

> [!NOTE]
> この機能が管理者によって有効化されていない場合は、管理者が電子メールや自分の Web ポータルを通じてサポートできるように、"管理者に連絡してください" というリンクが表示されます。
>

この方法を利用してアカウントのロック解除やパスワードのリセットを行った後は、"Microsoft on behalf of Your Organization Here" のようなアカウントから、この処理が完了したことを確認するメールが届く場合があります。 セルフサービスによるパスワードのリセットを利用してアカウントへのアクセスを回復した覚えがないのに、このようなメールが届いた場合は、管理者に問い合わせてください。

## <a name="change-my-password"></a>パスワードを変更する

パスワードを既に知っていて、それを変更する必要がある場合は、以下の手順を試してください

### <a name="change-your-password-from-the-office-365-portal"></a>Office 365 ポータルからパスワードを変更する

1. 右上にある自分のプロファイルをクリックし、**[アカウントを表示]** をクリックします
2. **[セキュリティとプライバシー]**
3. **パスワード**
4. 古いパスワードを入力し、新しいパスワードを設定して、それを確認します
5. **[送信]**

### <a name="change-your-password-from-the-azure-access-panel"></a>Azure アクセス パネルからパスワードを変更する

1. 既存のパスワードを使用して [Azure アクセス ポータル](https://myapps.microsoft.com/)にサインインします
2. 右上にある自分のプロファイルをクリックし、**[プロファイル]** をクリックします
3. **[パスワードの変更]**
4. 古いパスワードを入力し、新しいパスワードを設定して、それを確認します
5. **[送信]**

## <a name="next-steps"></a>次のステップ

* [セルフサービスによるパスワードのリセットを使用するための登録方法](active-directory-passwords-reset-register.md)
* [パスワードのリセット登録ページ](http://aka.ms/ssprsetup)
* [パスワードのリセット用ポータル](https://passwordreset.microsoftonline.com/)

[Login]: ./media/active-directory-passwords-update-your-own-password/reset-1-login.png "ログイン ページ: アカウントにアクセスできませんか?"
[Verification]: ./media/active-directory-passwords-update-your-own-password/reset-2-verification.png "認証データを確認する"
[Change]: ./media/active-directory-passwords-update-your-own-password/reset-3-change.png "パスワードを変更する"
[Complete]: ./media/active-directory-passwords-update-your-own-password/reset-4-complete.png "パスワードがリセットされました"

