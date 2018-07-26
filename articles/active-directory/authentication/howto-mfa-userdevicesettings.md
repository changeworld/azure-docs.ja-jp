---
title: 管理者によるユーザーとデバイスの管理 - Azure MFA | Microsoft Docs
description: ここでは、追加のセキュリティ確認を強制するなど、ユーザー設定を変更する方法について説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 2f73d9795ba807e5901568507ad2fae5b001c91a
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161291"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>クラウドでの Azure Multi-factor Authentication によるユーザー設定の管理

管理者は、ユーザーとデバイスの次の設定を管理できます。

* 連絡方法をもう一度提供するようユーザーに要求する
* アプリ パスワードを削除する
* 信頼されているすべてのデバイスについて MFA を要求する 

## <a name="require-users-to-provide-contact-methods-again"></a>連絡方法をもう一度提供するようユーザーに要求する
この設定は、もう一度登録プロセスを完了することを強制します。 ブラウザー以外のアプリは、ユーザーがアプリ パスワードを持っている場合に引き続き動作します。  ユーザー アプリ パスワードを削除するには、 **[選択したユーザーが生成したすべての既存のアプリ パスワードを削除する]** を選択することもできます。

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>連絡方法をもう一度提供するようユーザーに要求する方法
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で、**[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。 多要素認証ページが開きます。 
4. ユーザーまたは管理するユーザーの横にあるチェック ボックスをオンにします。 クイック手順オプションの一覧が右側に表示されます。 
5. **[Manage user settings]** (ユーザー設定の管理) を選択します。
6. **[選択したユーザーについて連絡方法の再指定を必須にする]** チェック ボックスをオンにします。
   ![連絡方法を提供する](./media/howto-mfa-userdevicesettings/reproofup.png)
7. **[Save]** をクリックします。
8. **[閉じる]** をクリックします。

## <a name="delete-users-existing-app-passwords"></a>ユーザーの既存のアプリ パスワードを削除する
この設定では、ユーザーが作成したすべてのアプリ パスワードが削除されます。 これらのアプリ パスワードに関連付けられているブラウザー以外のアプリは、新しいアプリ パスワードが作成されるまで機能しなくなります。

### <a name="how-to-delete-users-existing-app-passwords"></a>ユーザーの既存のアプリ パスワードを削除する方法
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で、**[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。 多要素認証ページが開きます。 
6. ユーザーまたは管理するユーザーの横にあるチェック ボックスをオンにします。 クイック手順オプションの一覧が右側に表示されます。 
7. **[Manage user settings]** (ユーザー設定の管理) を選択します。
8. **[選択したユーザーが生成したすべての既存のアプリ パスワードを削除する]** チェック ボックスをオンにします。
   ![アプリ パスワードを削除する](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
9. **[Save]** をクリックします。
10. **[閉じる]** をクリックします。

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>ユーザーの記憶されたすべてのデバイスで MFA を復元する
Azure Multi-Factor Authentication の構成可能な機能の 1 つに、デバイスを信頼済みとしてマークすることをユーザーが選択できる機能があります。 詳細については、「[Azure Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md#remember-multi-factor-authentication-for-devices-that-users-trust)」をご覧ください。

ユーザーは通常使用しているデバイスで、構成可能な日数の間、2 段階認証を省略できます。 アカウントが侵害されたり、信頼済みデバイスを紛失したりした場合、信頼済みの状態を削除し、2 段階認証を再び要求できる必要があります。

**[記憶されているすべてのデバイスに多要素認証を復元]** 設定は、ユーザーがデバイスを信頼済みとしてマークしているかどうかに関係なく、次回のサインイン時に 2 段階認証の実行を要求することを意味します。 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>ユーザーの中断されたすべてのデバイスで MFA を復元する方法
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側で、**[Azure Active Directory]**  >  **[ユーザーとグループ]**  >  **[すべてのユーザー]** を選択します。
3. **[Multi-Factor Authentication]** を選択します。 多要素認証ページが開きます。 
6. ユーザーまたは管理するユーザーの横にあるチェック ボックスをオンにします。 クイック手順オプションの一覧が右側に表示されます。 
7. **[Manage user settings]** (ユーザー設定の管理) を選択します。
8. **[記憶されているすべてのデバイスに多要素認証を復元]** チェック ボックスをオンにします。
   ![アプリ パスワードの削除](./media/howto-mfa-userdevicesettings/rememberdevices.png)
9. **[Save]** をクリックします。
10. **[閉じる]** をクリックします。

## <a name="next-steps"></a>次の手順

- 詳細については、「[Azure Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)」をご覧ください。

- ヘルプが必要な場合、「[2 段階認証のユーザー ガイド](../user-help/multi-factor-authentication-end-user.md)」を参照してください。
