---
title: "Azure AD: セルフサービスによるパスワードのリセットの登録 | Microsoft Docs"
description: "セルフサービスによるパスワードのリセット用の認証データを登録する"
services: active-directory
keywords: "Active Directory パスワード管理, パスワード管理, Azure AD セルフサービスによるパスワードのリセット, SSPR"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/16/2017
ms.author: joflore
ms.custom: end-user
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: c6d3d9d8f399816928e794e8956bc35825462fb9
ms.lasthandoff: 04/26/2017


---
# <a name="register-for-self-service-password-reset"></a>セルフサービスによるパスワードのリセットを登録する

セルフサービスによるパスワードのリセット (SSPR) が管理者によって有効化されている場合は、それを利用することで、エンド ユーザーがパスワードのリセットやアカウントのロック解除を自分自身で実行できます。 この機能を利用する前に、認証方法を登録するか、管理者によって設定されている定義済みの認証方法を確認する必要があります。

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR への認証データの登録または確認

1. デバイスで Web ブラウザーを開き、[パスワードのリセット登録ページ](http://aka.ms/ssprsetup)にアクセスします
2. 管理者から提供されているユーザー名とパスワードを入力します
3. パスワードのリセットが必要な場合に使用されるものとして構成または確認すべき以下の項目が、管理者の承認したオプションに応じて 1 つ以上表示されます
    * [会社電話] - このオプションは管理者のみが設定できます
    * [認証用電話] - このオプションには、テキスト メッセージや音声通話を受信できる携帯電話など、ユーザーが利用できる別の電話番号を設定する必要があります (携帯電話番号の情報を使用することについて既にユーザーの同意が得られている場合は、管理者がこのオプションにその番号を設定することもできます)
    * [認証用メール] - このオプションには、リセットが必要なパスワードを使用しなくてもアクセスできる、別の電子メール アドレスを設定する必要があります
    * [セキュリティの質問] - このオプションでは、ユーザーが回答することを管理者が承認した質問のリストが提示されます。 複数の質問に対して同じ回答を使用することはできません。
4. 管理者から要求されている情報を入力および確認します。 複数のオプションを指定できる場合は、別の認証方法が利用できないとき (たとえば、移動中のため会社の電話にアクセスできないなど) に柔軟に対応できるように、複数の方法を登録することをお勧めします

    ![認証方法を登録して [完了] をクリックする][Register]

5. 手順 4. を実行したら、**[完了]** を選択します。これで、今後必要なときに、セルフサービスによるパスワードのリセットが利用できます。

[認証用電話] や [認証用メール] にデータを入力した場合、そのデータはグローバル ディレクトリ上には表示されません。 このデータを見ることができるのは、データを入力したユーザーとその管理者だけです。 セキュリティの質問に対する回答を見ることができるのは、それを指定したユーザーだけです。

適切な認証方法が継続して登録されているか確認するため、管理者から、一定期間経過後に認証方法の確認を求められる場合もあります。

## <a name="next-steps"></a>次のステップ

* [セルフサービスによるパスワードのリセットを使用してパスワードを変更する方法](active-directory-passwords-update-your-own-password.md)
* [パスワードのリセット登録ページ](http://aka.ms/ssprsetup)
* [パスワードのリセット用ポータル](https://passwordreset.microsoftonline.com/)
* [Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "登録済みの方法と [完了] ボタンが表示されているパスワードのリセット登録ページ"

