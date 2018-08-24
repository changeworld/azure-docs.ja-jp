---
title: Microsoft Authenticator アプリの概要 - Azure Active Directory | Microsoft Docs
description: Microsoft Authenticatior の最新バージョンにアップグレードする方法について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: active-directoary
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.reviewer: librown
ms.openlocfilehash: c8e14091d7a3fb5b925735824b1dd3ce26f034e8
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143354"
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリの概要

Microsoft Authenticator アプリは、職場/学校のアカウント (たとえば、alain@contoso.com) や個人の Microsoft アカウント (たとえば、alain@outlook.com) に追加のセキュリティ レベルを提供することで、アカウントへの不正アクセスを防止し、不正なトランザクションを停止するものです。

2 段階認証用に使用する場合、このアプリは次のいずれかの方法で使用できます。

- **通知**。 アプリからデバイスに通知が送信されます。 ユーザーは通知内容が正しいことを確認し、**[確認]** を選択します。 通知に心当たりがない場合は、**[拒否]** を選択します。

- **確認コード**。 ユーザー名とパスワードを入力した後、ユーザーはアプリを開き、**[アカウント]** 画面に表示された確認コードをサインイン画面にコピーできます。 この検証コードにより、2 段階目の認証が実行されます。

## <a name="opt-in-for-two-step-verification"></a>2 段階認証のオプトイン

職場や学校のアカウントで 2 段階認証を使用するかどうかは、それらの組織によって決定されます。 どの認証方法を設定して使用するは、管理者がユーザーに知らせます。 詳しくは、「[Azure Multi-Factor Authentication とは何ですか](multi-factor-authentication-end-user.md)」をご覧ください。

個人の Microsoft アカウントの場合は、ユーザーが自分で 2 段階認証を設定できます。 詳しい情報と手順については、「[2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)」をご覧ください。

Microsoft Authenticator アプリは、Microsoft 以外のアカウントでも使用できます。 それらのアカウントでは 2 段階認証以外の機能が必要となる場合もありますが、それらの機能はセキュリティ設定やサインイン設定で設定できます。 Microsoft 以外のアカウントの設定方法について詳しくは、[Microsoft カスタマー サポート ビデオ](https://www.youtube.com/playlist?list=PLyhj1WZ29G65QdD9NxTOAm8HwOS-OBUrX)をご覧ください。

## <a name="install-the-app"></a>アプリのインストール

Microsoft Authenticator アプリは、[Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594)、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071) で利用できます。 最適なエクスペリエンスを得るには、ユーザーが情報を要求された際に、アプリに通知が送られるようにしてください。 

## <a name="add-accounts-to-the-app"></a>アプリへのアカウントの追加

Microsoft Authenticator アプリには、職場/学校用アカウントや個人用アカウントを追加できます。 

### <a name="add-a-personal-microsoft-account"></a>個人用の Microsoft アカウントを追加する

個人用の Microsoft アカウント (Outlook.com、Xbox、Skype などへのサインインに使用するアカウント) の場合、Microsoft Authenticator アプリでアカウントにサインインするだけです。

### <a name="add-a-work-or-school-account"></a>職場または学校用のアカウントを追加する

1. 可能であれば、別の PC やデバイスで [[追加のセキュリティ確認]](http://aka.ms/mfasetup) 画面にアクセスします。 この画面の表示方法について詳しくは、 [セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)に関するページを参照するか、管理者に問い合わせてください。

    >[!Note]
    >管理者がセキュリティ情報のプレビューを有効にしている場合は、「[認証アプリを使用するようにセキュリティ情報を設定する](security-info-setup-auth-app.md)」セクションの手順をご覧ください。

2. **[Authenticator アプリ]** の横のチェック ボックスをオンにし、**[構成]** を選択します。

    ![セキュリティ確認の設定画面の [構成] ボタン](./media/microsoft-authenticator-app-how-to/auth-app-configure.png)

    **[モバイル アプリケーションの構成]** 画面に、認証アプリでスキャンするための QR コードが表示されます。

    ![QR コードを提供する画面](./media/microsoft-authenticator-app-how-to/auth-app-barcode.png)

3. Microsoft Authenticator アプリを開きます。 **[アカウント]** 画面で、**[アカウントの追加]** を選択し、**[職場または学校アカウント]** を選択します。

4. デバイスのカメラを使って QR コードをスキャンし、 **[完了]** を選択して QR コードの画面を閉じます。

    >[!Note]
    >カメラが適切に動作しない場合は、[QR コードと URL を手動で入力](#add-an-account-to-the-app-manually)できます。

    アプリの **[アカウント]** 画面に、アカウント名と 6 桁の認証コードが表示されます。 同じコードが 2 回使用されるのを防ぐため、確認コードは 30 秒ごとに変更されます。  

    ![[アカウント] 画面](./media/microsoft-authenticator-app-how-to/auth-app-accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>アプリにアカウントを手動で追加する

1. **[追加のセキュリティ確認]** 画面に移動します。 この画面の表示方法の詳細については、 [セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)に関するページを参照してください。

2. **[Authenticator アプリ]** の横のチェック ボックスをオンにし、**[構成]** を選択します。

    **[モバイル アプリケーションの構成]** 画面が表示されます。

3. QR スキャナー画面に手動で入力できるよう、**[モバイル アプリケーションの構成]** 画面からコードと URL 情報をコピーします。

4. Microsoft Authenticator アプリを開きます。 **[アカウント]** 画面で、**[アカウントの追加]** を選択し、**[職場または学校アカウント]** を選択します。

5. QR スキャナー画面で、 **[コードを手動で入力する]** を選択します。

    ![QR コードをスキャンする画面](./media/microsoft-authenticator-app-how-to/auth-app-manual-code.png)
   
6. QR コードの画面にあるコードと URL を **[アカウントの追加]** 画面に入力し、**[完了]** を選択します。

    ![コードと URL を入力する画面](./media/microsoft-authenticator-app-how-to/auth-app-code-url.png)

    アプリの **[アカウント]** 画面に、アカウント名と 6 桁の認証コードが表示されます。 同じコードが 2 回使用されるのを防ぐため、確認コードは 30 秒ごとに変更されます。

### <a name="using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>デバイスの指紋または顔認識機能を使用する

組織によっては、本人確認のために PIN が要求されることもあります。 Microsoft Authenticator アプリでは、PIN の代わりにデバイスの指紋認識や顔認識機能を使用するように設定することができます。 これを設定するには、認証アプリでの初回のアカウント確認時に、デバイスの生体認証機能を (PIN の代わりに) ID として使用するオプションを選択します。

## <a name="use-the-app-when-you-sign-in"></a>サインイン時にアプリを使用する

アプリにアカウントを追加したら、アプリを使用してアカウントにサインインできるようになります。

アプリで確認コードを使用するように選択した場合は、それらが **[アカウント]** ページに表示されるようになります。 コードは 30 秒ごとに変更されるので、ユーザーは毎回新しいコードを使用できます。 ただし、サインインし、確認コードを入力するように求められるまでは、何もする必要はありません。

## <a name="next-steps"></a>次の手順

- アプリに関する一般的な質問については、「[Microsoft Authenticator アプリに関する FAQ](microsoft-authenticator-app-faq.md)」をご覧ください

- 2 段階認証の詳細については、[アカウントの 2 段階認証の設定](multi-factor-authentication-end-user-first-time.md)に関する記事を参照してください。

- セキュリティ情報の詳細については、[セキュリティ情報の管理](security-info-manage-settings.md)に関する記事を参照してください。
