---
title: Microsoft Authenticator アプリに職場または学校アカウントを追加する - Azure AD
description: 2 要素検証の使用中に ID を検証するために、Microsoft Authenticator アプリに職場または学校アカウントを追加します。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/09/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 276590a295b59465184a9afa2f7ce5efea7bb8eb
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228701"
---
# <a name="add-your-work-or-school-account-to-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリに職場または学校アカウントを追加する

組織で 2 要素認証を使用している場合は、認証方法の 1 つとして、Microsoft Authenticator アプリを使用するように職場または学校のアカウントを設定できます。

>[!Important]
>アカウントを追加する前に、Microsoft Authenticator アプリをダウンロードしてインストールする必要があります。 まだ完了していない場合は、「[アプリのダウンロードとインストール](user-help-auth-app-download-install.md)」という記事の手順に従ってください。

## <a name="add-your-work-or-school-account"></a>職場または学校のアカウントを追加する

Microsoft Authenticator アプリに職場または学校アカウントを追加するには、次のいずれかを行います。

- 職場または学校アカウントの資格情報を使用してサインインする (プレビュー)
- QR コードをスキャンする

### <a name="sign-in-with-your-credentials"></a>資格情報を使用してサインイン

>[!Note]
>これで、Microsoft Authenticator アプリにサインインして、職場または学校アカウントを追加できます。

アカウントを追加するには、自分の資格情報を使用して職場または学校アカウントにサインインします。

1. Microsoft Authenticator アプリを開き、 **[+]** ボタンを選択し、 **[Add work or school account]\(職場または学校アカウントの追加\)** をタップします。 **[サインイン]** を選択します。

1. 職場または学校アカウントの資格情報を入力します。 一時アクセス パス (TAP) がある場合は、それを使用してサインインできます。 この時点で、次のいずれかの条件によって処理がブロックされる可能性があります。

   - アカウントに効果的な認証トークンを取得するための十分な認証方法がない場合は、アカウントの追加作業に進むことができません。

   - `You might be signing in from a location that is restricted by your admin` というメッセージが表示された場合、管理者はあなたに対してこの機能を有効にしておらず、セキュリティ情報登録条件付きアクセス ポリシーを設定している可能性があります。 この認証方法を使用するには、職場または学校アカウントの管理者に問い合わせてください。

1. スマートフォンの Authenticator アプリによるサインインを管理者により許可されている場合は、デバイス登録を行って、スマートフォンでのパスワードレス サインインと Azure AD Multi-Factor Authentication を設定できます。 ただし、多要素認証は、スマートフォンでのサインインが有効かどうかにかかわらず設定できます。

1. この時点で、組織から提供された QR コードをスキャンして、アプリでオンプレミスの多要素認証アカウントを設定するように求められる場合があります。 この操作を行う必要があるのは、組織がオンプレミスの MFA Server を使用している場合のみです。

1. デバイス上でアカウントをタップし、全画面モードで、アカウントが正しいことを確認してください。 セキュリティ強化のため、他のユーザーによってコードが複数回使用されないように、確認コードは 30 秒ごとに変更されます。

## <a name="sign-in-with-a-qr-code"></a>QR コードを使用してサインインする

QR コードをスキャンしてアカウントを追加するには、以下を実行します。

1. お使いのコンピューターで、**[追加のセキュリティ確認]** ページに移動します。

   >[!Note]
   >**[追加のセキュリティ確認]** ページが表示されない場合は、管理者がセキュリティ情報 (プレビュー) エクスペリエンスをオンにしている可能性があります。 その場合は、[[認証アプリを使用するようにセキュリティ情報を設定する]](security-info-setup-auth-app.md) セクションの指示に従います。 そうでない場合は、組織のヘルプ デスクに問い合わせる必要があります。 セキュリティ情報の詳細については、「[サインイン プロンプトから自分のセキュリティ情報を設定する](security-info-setup-signin.md)」を参照してください。

1. Authenticator アプリの横にあるチェック ボックスをオンにし、 **[構成]** を選択します。 **[モバイル アプリケーションの構成]** ページが表示されます。

   ![QR コードを提供する画面](./media/user-help-auth-app-add-work-school-account/auth-app-barcode.png)

1. Microsoft Authenticator アプリを開き、プラス アイコン ![iOS または Android のデバイスでプラス アイコンを選択する](media/user-help-auth-app-add-work-school-account/plus-icon.png) を選択して、 **[アカウントの追加]** を選択し、 **[職場または学校アカウント]** 、その後に  **[QR コードをスキャンします]** を選択します。
   Authenticator アプリにアカウントが設定されていない場合は、 **[アカウントの追加]** という大きな青いボタンが表示されます。

QR コードのスキャンにカメラを使用することについて確認を求められない場合は、携帯電話の設定で Authenticator アプリが電話のカメラにアクセスできることを確認してください。 QR コードを使用してアカウントを追加した後、スマートフォンでのサインインを設定できます。 "管理者によって制限されている場所からサインインしている可能性があります" というメッセージが表示された場合、管理者はあなたに対してこの機能を有効にしておらず、セキュリティ情報登録条件付きアクセス ポリシーを設定している可能性があります。 この認証方法を使用するには、職場または学校アカウントの管理者に問い合わせてください。 スマートフォンの Authenticator アプリによるサインインを管理者に許可 "*されている*" 場合は、デバイス登録を行って、スマートフォンでのパスワードレス サインインと Azure AD Multi-Factor Authentication を設定できます。

>[!Note]
> 米国政府機関の場合、スマートフォンでのサインイン アカウントを追加する唯一の方法は、QR コード ベースのアカウントからアップグレードするのではなく、[資格情報でサインインする](#sign-in-with-your-credentials)オプションを使用して追加する方法です。

## <a name="sign-in-on-a-remote-computer"></a>リモート コンピューターでサインインする

アプリには、PC などの他のデバイス上でコードを入力することで、認証を実行できるものも多いです。 リモート コンピューターでサインインするには、Microsoft Authenicator アプリをインストールします。

1. Microsoft Authenticator アプリを開き、 **[+]** ボタン を押し、&gt; **[職場または学校アカウントの追加]** &gt; **サインイン** を選択します。
1. **[別のデバイスからサインインする]** を選択します。
1. リモート画面で [ **[アカウントへのサインイン]** ページ](https://microsoft.com/devicelogin)を開き、Authenticator アプリに表示されるコードを入力します。
1. リモート画面で、職場または学校アカウントのサインイン情報を使用してサインインします。 一時アクセス パス (TAP) がある場合は、それを使用してサインインできます。
1. リモート画面で認証を完了したら、Authenticator アプリに戻って設定を完了します。

 ## <a name="next-steps"></a>次のステップ

- アプリにアカウントを追加すると、デバイスで Authenticator アプリを使用してサインインできます。 詳細については、[アプリを使用したサインイン](user-help-auth-app-sign-in.md)に関するページを参照してください。

- iOS を実行しているデバイスの場合、アカウント資格情報と、アカウントの順序などの関連アプリ設定を、クラウドにバックアップすることもできます。 詳細については、[Microsoft Authenticator アプリを使用したバックアップと復旧](user-help-auth-app-backup-recovery.md)に関するページを参照してください。
