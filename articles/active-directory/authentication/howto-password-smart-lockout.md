---
title: Azure AD のスマート ロックアウトを使用してブルート フォース攻撃を防止する
description: Azure Active Directory のスマート ロックアウトを使用すると、パスワードを推測するブルート フォース攻撃から組織を守れます
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: b0fded9f5543d151091955c0b0d645bf9db16b7d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158585"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory のスマート ロックアウト

スマート ロックアウトでは、クラウド インテリジェンスを使用して、組織のユーザーのパスワードを推測したり、ブルート フォース方法を使用して侵入しようとする悪意のあるユーザーをロックアウトします。 インテリジェンスにより、有効なユーザーからのサインインを認識し、攻撃者や他の不明なソースからのユーザーと異なる方法で処理します。 スマート ロックアウトを使用することで、組織のユーザーは自分のアカウントへのアクセスを継続できるため、生産性を落とすことなく攻撃者をロックアウトできます。

既定のスマート ロックアウトでは、10 回試行に失敗した後、アカウントによるサインインの試行が 1 分間ロックされます。 後続のサインインの試行が失敗するたびに、アカウントは再度ロックされます。最初は 1 分間、後続の試行ではより長い時間ロックされます。

スマート ロックアウトは、すべての Azure AD 顧客に既定として設定され、常に有効で、セキュリティとユーザビリティを適切な割合で提供します。 スマート ロックアウト設定を組織固有の値にカスタマイズするには、ユーザーに Azure AD Basic 以上のライセンスが必要です。

スマート ロックアウトは、パスワード ハッシュ同期またはパススルー認証を使用するハイブリッド デプロイと統合でき、オンプレミスの Active Directory アカウントが攻撃者によってロックアウトされることを防止します。 Azure AD でスマート ロックアウト ポリシーを適切に設定することにより、オンプレミスの Active Directory に到達する前に攻撃を防止できます。

[パススルー認証](../connect/active-directory-aadconnect-pass-through-authentication.md)を使用する場合は、以下を確認してください。

   * Azure AD のロックアウトしきい値が、Active Directory アカウント ロックアウトしきい値より**小さい**。 Active Directory アカウント ロックアウトしきい値が Azure AD のロックアウトしきい値より少なくとも 2 - 3 倍長くなるように値を設定します。 
   * Azure AD のロックアウト期間 **(秒単位)** が、Active Directory のロックアウト カウンターのリセットの期間 **(分単位)** より **長い**。

> [!IMPORTANT]
> 現時点では、ユーザーのクラウド アカウントがスマート ロックアウト機能によってロックされている場合、管理者はロックを解除できません。 管理者はロックアウト期間が期限切れになるまで待つ必要があります。

## <a name="verify-on-premises-account-lockout-policy"></a>オンプレミス アカウントのロックアウト ポリシーを検証する

次の指示に従って、オンプレミス Active Directory アカウントのロックアウト ポリシーを検証します。

1. グループ ポリシー管理ツールを開きます。
2. 組織のアカウント ロックアウト ポリシーを含む、グループ ポリシー (例: **デフォルト ドメイン ポリシー)** を編集します。
3. **[コンピューターの構成]** > **[ポリシー]** > **[Windows 設定]** > **[セキュリティ設定]** > **[アカウント ポリシー]** > **[アカウント ロックアウト ポリシー]** の順に移動します。
4. **[アカウント ロックアウトのしきい値]** と **[ロックアウト カウンターのリセット]** の値を確認します。

![グループ ポリシー オブジェクトを使用して、オンプレミス Active Directory アカウントを編集する](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Azure AD スマート ロックアウトの値を管理する

組織の要件によっては、スマート ロックアウトの値をカスタマイズする必要があります。 スマート ロックアウト設定を組織固有の値にカスタマイズするには、ユーザーに Azure AD Basic 以上のライセンスが必要です。

組織のスマート ロックアウト値を確認または編集するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインし、**[Azure Active Directory]**、**[認証方法]** の順にクリックします。
1. 何回サインインに失敗したらアカウントがロックアウトされるかを基に **[ロックアウトのしきい値]** を設定します。 既定値は 10 です。
1. **[Lockout duration in seconds]\(秒単位のロックアウト期間\)** で、各ロックアウトの長さを秒単位で設定します。 既定値は 60 秒 (1 分) です。

> [!NOTE]
> ロックアウト後、はじめてのサインインにも失敗した場合は、アカウントは再度ロックアウトされます。 アカウントが繰り返しロックされた場合は、ロックアウト時間が長くなります。

![Azure Portal で Azure AD スマート ロックアウト ポリシーをカスタマイズする](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)
## <a name="next-steps"></a>次の手順

[Azure AD を使用して、組織内の不適切なパスワードの使用を禁止する方法について説明します。](howto-password-ban-bad.md)

[ユーザーが自分でアカウントのロックを解除できるよう、セルフ サービスのパスワード リセット機能を構成します。](quickstart-sspr.md)