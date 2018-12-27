---
title: 個人デバイスを組織のネットワークに登録する - Azure Active Directory | Microsoft Docs
description: 組織の保護されたリソースにアクセスできるように、個人デバイスを組織のネットワークに登録する方法について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 7126a47bd90168c7d86fe9fcc05fab0a60955063
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180583"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>個人デバイスを組織のネットワークに登録する
個人デバイス (通常は電話またはタブレット) を組織のネットワークに登録します。 デバイスが登録されたら、そのデバイスは組織の制限されたリソースにアクセスできるようになります。

>[!Note]
>この記事ではデモンストレーションのために Windows デバイスを使用しますが、iOS、Android、または macOS を実行しているデバイスも登録できます。

## <a name="what-happens-when-you-register-your-device"></a>デバイスを登録したときに実行される内容
デバイスを組織のネットワークに登録している間、次のアクションが実行されます。

- Windows がデバイスを組織のネットワークに登録します。

- オプションで、組織の選択に基づいて、[Multi-Factor Authentication](multi-factor-authentication-end-user-first-time.md) または[セキュリティ情報](user-help-security-info-overview.md)のどちらかを使用して 2 段階認証を設定するよう求められることがあります。

- オプションで、組織の選択に基づいて、Microsoft Intune などのモバイル デバイス管理で自動的に登録されることがあります。 Microsoft Intune での登録の詳細については、[Intune でのデバイスの登録](https://docs.microsoft.com/intune-user-help/enroll-your-device-in-intune-all)に関するページを参照してください。

- 個人の Microsoft アカウントのユーザー名とパスワードを使用して、サインイン プロセスを実行します。

## <a name="to-register-your-windows-device"></a>Windows デバイスを登録するには

個人デバイスをネットワークに登録するには、次の手順に従います。

1. **[設定]** を開いてから、**[アカウント]** を選択します。

    ![[設定] 画面上の [アカウント]](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **[Email & accounts] (電子メールとアカウント)** を選択してから、**[Join a Microsoft account] (Microsoft アカウントを参加させる)** を選択します。

    ![[Email & accounts] (電子メールとアカウント) および [Add a Microsoft account] (Microsoft アカウントを追加する) リンク](./media/user-help-register-device-on-network/register-device-email-and-accounts.png)

3. **[Add your Microsoft account] (Microsoft アカウントを追加する)** 画面で、個人の Microsoft アカウントのメール アドレスを入力します。

    ![電子メールが表示された [Add your Microsoft account] (Microsoft アカウントを追加する) 画面](./media/user-help-register-device-on-network/register-device-add-accounts.png)

4. **[パスワードの入力]** 画面で、個人の Microsoft アカウントのパスワードを入力し、**[サインイン]** を選択します。

    ![[パスワードの入力] 画面](./media/user-help-register-device-on-network/register-device-enter-password.png)

5. 本人確認要求の承認 (2 段階認証を使用する場合) や Windows Hello の設定 (必要な場合) など、登録プロセスの残りを完了します。

## <a name="to-make-sure-youre-registered"></a>登録されたことを確認するには
設定を参照することによって、登録されたことを確認できます。

1. **[設定]** を開いてから、**[アカウント]** を選択します。

    ![[設定] 画面上の [アカウント]](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. **[Email & accounts] (電子メールとアカウント)** を選択し、個人の Microsoft アカウントが表示されることを確認します。

    ![接続されている contoso アカウントが表示された [職場または学校にアクセスする] 画面](./media/user-help-register-device-on-network/register-device-verify-account.png)

## <a name="next-steps"></a>次の手順
個人デバイスを組織のネットワークに登録したら、ほとんどのリソースにアクセスできるようになります。

- 組織で職場のデバイスを参加させるようにする場合は、「[職場のデバイスを組織のネットワークに参加させる](user-help-join-device-on-network.md)」を参照してください。



