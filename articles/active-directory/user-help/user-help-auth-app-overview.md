---
title: Microsoft Authenticator アプリの概要 - Azure Active Directory | Microsoft Docs
description: Microsoft Authenticator アプリについて、その概要としくみのほか、コンテンツのこのセクションに記載されている情報を説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/24/2019
ms.author: lizross
ms.openlocfilehash: 2379f1ff4fb4385015cc6077cb923cab998d1d11
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175215"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリとは
Microsoft Authenticator アプリは、お客様が 2 要素認証を使用する場合にアカウントにサインインするのに役立ちます。 2 要素認証は、特に機密情報の表示中に、お客様のアカウントにより安全にアクセスするために役立ちます。 パスワードは忘れたり、盗まれたり、侵害されたりすることがあるため、2 要素認証は、他のユーザーの侵入を困難にすることでお客様のアカウントの保護に役立つ追加のセキュリティ手順です。

Microsoft Authenticator アプリは次のような複数の方法で使用できます。

- お客様が自分のユーザー名とパスワードを使ってサインインした後に、2 つ目の認証方法が求められるようにする。

- 自分のユーザー名のほか、指紋、顔、または PIN と共に自分のモバイル デバイスを使用して、パスワードを必要とせずにサインインできるようにする。

>[!Important]
>このコンテンツはユーザーを対象としています。 管理者の方は、Azure Active Directory (Azure AD) 環境の設定と管理方法の詳細については、「[Azure Active Directory のドキュメント](https://docs.microsoft.com/azure/active-directory)」を参照してください。<br><br>アカウントへのサインインに問題がある場合は、「[Microsoft アカウントにサインインできない場合](https://support.microsoft.com/help/12429)」をヘルプとして参照してください。  Microsoft アカウントへのサインイン試行時に ["指定した Microsoft アカウントは登録されていません"](https://support.microsoft.com/help/13811) というメッセージが表示される場合の対処方法を、詳しく確認してください。

## <a name="terminology"></a>用語集
|用語|説明|
|----|-----------|
|2 要素認証 |わずか 2 種類の認証情報 (パスワードと PIN など) を特に使用しなければならない認証プロセスです。 Microsoft Authenticator アプリでは、標準の 2 要素認証とパスワードなしのサインインの両方がサポートされます。|
|Multi-Factor Authentication (MFA)|すべての 2 要素認証は多要素認証です。お客様の組織の要件に基づいて、"*少なくとも*" 2 種類の認証情報を使用することが求められます。|
|Microsoft アカウント (別称: MSA)|コンシューマー向けの Microsoft 製品およびクラウド サービス (Outlook、OneDrive、Xbox LIVE、Office 365 など) にアクセスするために、お客様が自分の個人アカウントを作成します。 お使いの Microsoft アカウントは、Microsoft が運営する Microsoft コンシューマー ID アカウント システムを使用して作成、保存されます。|
|会社または学校のアカウント|内部リソースおよび制限付きの可能性があるリソース (Microsoft Azure、Windows Intune、Office 365 など) へのアクセスをお客様に許可するために、お客様の組織が職場または学校アカウント (alain@contoso.com など) を作成します。|

## <a name="how-two-factor-verification-works-with-the-app"></a>アプリでの 2 要素認証のしくみ
Microsoft Authenticator アプリでは、2 要素認証が次の方法で機能します。

- **通知**。 職場または学校アカウントあるいは個人の Microsoft アカウントのいずれかについて、お客様がログインするデバイスにユーザー名とパスワードを入力します。その後、Microsoft Authenticator アプリから、**サインインの承認**をお客様に求める通知が送信されます。 サインインを承認する場合は **[承認]** を選択します。 承認しない場合は、**[拒否]** を選択します。 **[拒否]** を選択すると、要求を不正としてマークすることもできます。

- **確認コード**。 職場または学校アカウントあるいは個人の Microsoft アカウントのいずれかについて、お客様がログインするデバイスにユーザー名とパスワードを入力します。その後、関連付けられている確認コードを Microsoft Authenticator アプリの **[アカウント]** 画面からコピーします。

- **パスワードなしのサインイン。** 職場または学校アカウントあるいは個人の Microsoft アカウントのいずれかについて、お客様がログインするデバイスにユーザー名とパスワードを入力します。その後、モバイル デバイスを使って、自分の指紋、顔、または PIN を使用して本人確認を行います。 この方法では、パスワードを入力する必要がありません。

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>デバイスの生体認証機能を使用するかどうか
PIN を使用して認証プロセスを完了する場合、代わりにお客様のデバイスの指紋認識機能または顔認識機能 (生体認証) を使用するよう、Microsoft Authenticator アプリを設定できます。 これを設定するには、認証アプリでの初回のアカウント確認時に、デバイスの生体認証機能を (PIN の代わりに) ID として使用するオプションを選択します。

## <a name="who-decides-if-you-use-this-feature"></a>この機能の使用を決定するユーザー
アカウントの種類に応じて、2 要素認証を使用しなければならないことをお客様の組織が決定する場合、またはお客様が自分で決定できる場合があります。

- **職場または学校のアカウント。** 職場または学校アカウント (例: alain@contoso.com) を使用している場合、お客様が 2 要素認証を使用しなければならないかどうかは、特定の認証方法と共にお客様の組織が決定します。 多要素認証の詳細については、「[Azure Multi-Factor Authentication とは何ですか](multi-factor-authentication-end-user.md)」を参照してください。 Microsoft 認証アプリを使用するためにセキュリティ情報を設定する方法の詳細については、「[認証アプリを使用するようにセキュリティ情報を設定する (プレビュー)](security-info-setup-auth-app.md)」を参照してください。

- **個人の Microsoft アカウント。** 個人の Microsoft アカウント (例: alain@outlook.com) の場合、2 要素認証の設定を選択できます。

- **Microsoft 以外の個人アカウント。** Microsoft 以外の個人アカウント (例: alain@gmail.com) の場合、2 要素認証の設定を選択できます。 Microsoft 以外の個人アカウントでは 2 要素認証という用語が使用されていない可能性がありますが、**セキュリティ**設定内または**サインイン**設定内で機能を見つけられるはずです。

## <a name="in-this-section"></a>このセクションの内容

|記事 |説明 |
|------|------------|
|[アプリのダウンロードとインストール](microsoft-authenticator-app-how-to.md)|Android、iOS、Windows Phone を実行する各デバイス用の Microsoft Authenticator アプリの入手場所、入手方法、およびインストール方法について説明します。|
|[職場または学校アカウントの追加](microsoft-authenticator-app-add-work-account.md)|さまざまな職場または学校アカウントと個人アカウントを Microsoft Authenticator アプリに追加する方法について説明します。|
|[個人アカウントの追加](microsoft-authenticator-app-add-personal-account.md)|個人の Microsoft アカウントと Microsoft 以外のアカウントを Microsoft Authenticator アプリに追加する方法について説明します。|
|[手動によるアカウントの追加](microsoft-authenticator-app-add-account-manual.md)|提供された QR コードをスキャンできない場合に、お客様のアカウントを Microsoft Authenticator アプリに手動で追加する方法について説明します。|
|[アプリを使用したサインイン](microsoft-authenticator-app-phone-signin-faq.md)|Microsoft Authenticator アプリを使用して、さまざまアカウントにサインインする方法について説明します。|
|[アカウント資格情報のバックアップと復旧](microsoft-authenticator-app-backup-and-recovery.md)| Microsoft Authenticator アプリを使用してアカウント資格情報をバックアップおよび復旧する方法について説明します。|
|[Microsoft Authenticator アプリに関する FAQ](microsoft-authenticator-app-faq.md)|アプリについてよく寄せられる質問とその回答を紹介します。|
