---
title: 携帯電話向けの Microsoft Authenticator アプリ | Microsoft Docs
description: Azure Authenticatior の最新バージョンにアップグレードする方法について説明します。
services: multi-factor-authentication
documentationcenter: ''
author: barlanmsft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 1532054a9463d710685d3f865d2e26ee7ff5014f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2018
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Microsoft Authenticator アプリの概要
Microsoft Authenticator アプリは、職場または学校アカウント (bsimon@contoso.com など)、または Microsoft アカウント (bsimon@outlook.com など) に追加のセキュリティ レベルを提供します。

アプリは次のいずれかの方法で機能します。

* **通知**。 スマートフォンまたはタブレットに通知をプッシュして、アカウントへの不正アクセスを防止したり、不正なトランザクションを停止させることができます。 通知を確認し、適切である場合は **[認証]**を選択するだけです。 適切でない場合は、 **[拒否]**を選択します。
* **確認コード**。 アプリをソフトウェア トークンとして使用して、OAuth 検証コードを生成できます。 ユーザー名とパスワードを入力したら、アプリから提供されたコードをサインイン画面に入力します。 検証コードにより、2 番目の形式の認証が行われます。

Azure Authenticator アプリは Microsoft Authenticator アプリに置き換えられています。 Azure Authenticator アプリは引き続き機能しますが、新しい Microsoft Authenticator アプリに移行することにした場合は、この記事が役に立ちます。  

## <a name="opt-in-for-two-step-verification"></a>2 段階認証のオプトイン

Microsoft Authenticator アプリは、単独で動作しません。 ユーザー名とパスワードでサインインした後に、2 つ目の確認方法を求めるように、それぞれのアカウントを設定してください。

職場または学校アカウントの場合、通常、この機能を自分で選択することはありません。 代わりに、セキュリティ管理者がオプトインし、アカウントの確認方法を登録するように通知します。 このシナリオが当てはまる場合は、「[Azure Multi-Factor Authentication とは何ですか](multi-factor-authentication-end-user.md)」で詳細を参照してください。

個人アカウントの場合は、自分で 2 段階認証を設定する必要があります。 Microsoft アカウントがある場合は、「[2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)」で手順を参照してください。

Microsoft 以外のアカウントで Microsoft 認証システムを使用することもできます。 その機能は 2 段階認証と呼ばれていない場合がありますが、セキュリティまたはサインイン設定にあるはずです。

## <a name="install-the-app"></a>アプリのインストール
Microsoft Authenticator アプリは、[Android](https://go.microsoft.com/fwlink/?linkid=866594)、[iOS](https://go.microsoft.com/fwlink/?linkid=866594)、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071) で利用できます。

## <a name="add-accounts-to-the-app"></a>アプリへのアカウントの追加
Microsoft Authenticator アプリに追加する各アカウントについて、次のいずれかの手順を実行します。

### <a name="add-a-personal-microsoft-account-to-the-app"></a>個人用の Microsoft アカウントをアプリに追加する

個人用の Microsoft アカウント (Outlook.com、Xbox、Skype などへのサインインに使用するアカウント) の場合、Microsoft Authenticator アプリでアカウントにサインインするだけです。

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>QR コード スキャナーを使用して職場または学校アカウントをアプリに追加する
1. セキュリティ確認の設定画面に移動します。  この画面の表示方法の詳細については、 [セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page)に関するページを参照してください。
2. **[Authenticator アプリ]** の横のチェック ボックスをオンにし、**[構成]** を選択します。

    ![セキュリティ確認の設定画面の [構成] ボタン](./media/authenticator-app-how-to/azureauthe.png)

    QR コードを記載した画面が表示されます。

    ![QR コードを提供する画面](./media/authenticator-app-how-to/barcode2.png)
3. Microsoft Authenticator アプリを開きます。 **[アカウント]** 画面で、**+** を選択し、職場または学校のアカウントの追加を指定します。
4. カメラを使って QR コードをスキャンし、 **[完了]** を選択して QR コードの画面を閉じます。

    カメラが適切に動作しない場合は、[QR コードと URL を手動で入力](#add-an-account-to-the-app-manually)できます。

5. アプリにアカウント名とその下に 6 桁のコードが表示されたら完了です。

    ![[アカウント] 画面](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>アプリにアカウントを手動で追加する
1. セキュリティ確認の設定画面に移動します。  この画面の表示方法の詳細については、 [セキュリティ設定の変更](multi-factor-authentication-end-user-manage-settings.md)に関するページを参照してください。
2. **[構成]**をクリックします。

    ![セキュリティ確認の設定画面の [構成] ボタン](./media/authenticator-app-how-to/azureauthe.png)

    QR コードを記載した画面が表示されます。  コードと URL を書き留めます。

    ![QR コードと URL を提供する画面](./media/authenticator-app-how-to/barcode2.png)
3. Microsoft Authenticator アプリを開きます。 **[アカウント]** 画面で、**+** を選択し、職場または学校のアカウントの追加を指定します。

4. スキャナーで、 **[enter code manually (コードを手動で入力)]**を選択します。

    ![QR コードをスキャンする画面](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. アプリの適切なボックスにコードと URL を入力し、**[完了]** を選択します。

    ![コードと URL を入力する画面](./media/authenticator-app-how-to/manual.png)

6. アプリにアカウント名とその下に 6 桁のコードが表示されたら完了です。

    ![[アカウント] 画面](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>デバイスの指紋または顔認識機能を使用してアプリにアカウントを追加する
検証チャレンジを完了するには、組織に PIN が必要になることがあります。 Microsoft Authenticator アプリでは、PIN の代わりに、デバイスの指紋または顔認識機能を使用できます。 アプリで初めて検証を行うとき、これを設定するために、代わりに Touch ID (iOS の場合) または指紋認証を使用するオプションが表示されます。 

Microsoft Authenticator 用に Touch ID を設定するには、PIN を使用して、通常の検証チャレンジを完了する必要があります。 これは、Microsoft Authenticator により、Touch ID をサポートするデバイスに対して自動的に設定されます。 

![Touch ID セットアップの検証](./media/authenticator-app-how-to/touchid1.png)

それ以降、サインインの確認が必要になった場合は、受信したプッシュ通知を選択し、PIN を入力する代わりに指紋をスキャンします。

![プッシュ通知](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>サインイン時にアプリを使用する

アカウントをアプリに追加すると、すべて正しく構成されているかどうかを確認するテスト検証を行うように求められることがあります。 検証したら、これで完了です。 次回サインインするまで何も行う必要はありません。

アプリで確認コードを使用するように選択した場合は、それらがホーム ページに表示されるようになります。 それらは、30 秒ごとに変更されるため、必要に応じて常に新しいコードが得られます。 ただし、サインインし、確認コードを入力するように求められるまでは、何もする必要はありません。  
