---
title: "Azure AD: SSPR 登録 | Microsoft Docs"
description: "Azure AD セルフサービスによるパスワードのリセット用の認証データを登録する"
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
ms.custom: end-user
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: bd0175558a94a146a365e8c1a32522f1e54c184e
ms.contentlocale: ja-jp
ms.lasthandoff: 05/03/2017


---
# <a name="register-for-self-service-password-reset"></a>セルフサービスによるパスワードのリセットを登録する

エンド ユーザーは、セルフサービスによるパスワードのリセット (SSPR) を使用してパスワードのリセットやアカウントのロック解除を自分自身で実行できます。 この機能を利用する前に、認証方法を登録するか、管理者によって設定されている定義済みの認証方法を確認する必要があります。

## <a name="register-or-confirm-authentication-data-with-sspr"></a>SSPR への認証データの登録または確認

1. デバイスで Web ブラウザーを開き、[パスワードのリセット登録ページ](http://aka.ms/ssprsetup)にアクセスします
2. 管理者から提供されているユーザー名とパスワードを入力します
3. IT スタッフがどのように構成したかに応じて、次のオプションの 1 つ以上を構成および確認できます。 管理者が情報を使用するアクセス許可を持っている場合は、管理者がこの一部をユーザーに代わって設定している可能性があります。
    * [会社電話] は管理者のみが設定できます
    * [認証用電話] には、テキストまたは通話を受信できる携帯電話など、ユーザーが利用できる別の電話番号を設定する必要があります。
    * [認証用メール] には、リセットが必要なパスワードを使用しなくてもアクセスできる、別の電子メール アドレスを設定する必要があります。
    * [セキュリティの質問] では、ユーザーが回答することを管理者が承認した質問のリストが提示されます。 同じ質問や回答を複数回使用することはできません。
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


