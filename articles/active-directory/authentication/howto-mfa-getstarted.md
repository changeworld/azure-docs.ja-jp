---
title: クラウドでの Azure MFA の概要
description: 条件付きアクセスを使用した Microsoft Azure Multi-Factor Authentication の概要
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: c2d0f14bca2b9ab062c61407479ab45a0104ff0a
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716265"
---
# <a name="deploy-cloud-based-azure-multi-factor-authentication"></a>クラウドベースの Azure Multi-Factor Authentication をデプロイする

Azure Multi-Factor Authentication (Azure MFA) の使用開始は簡単なプロセスです。

開始する前に、前提条件となる次のものがあることを確認してください。

* Azure AD テナントでのグローバル管理者アカウント。 この手順を完了するためのヘルプが必要な場合は、「[Get started with Azure AD (Azure AD の概要)](../get-started-azure-ad.md)」を参照してください。
* ユーザーに割り当てられた適切なライセンス。 詳細情報が必要な場合は、トピック「[Azure Multi-Factor Authentication の入手方法](concept-mfa-licensing.md)」を参照してください。

## <a name="choose-how-to-enable"></a>有効にする方法を選択する

**条件付きアクセス ポリシーで有効にする** - この方法は、この記事の中で説明します。 ユーザーの 2 段階認証を有効にするうえで最も柔軟性の高い手段です。 条件付きアクセス ポリシーを使用して有効にする方法は、クラウド内の Azure MFA に対してのみ機能します。これは Azure AD の Premium 機能です。

Azure AD Identity Protection で有効にする - この方法では、Azure AD Identity Protection のリスク ポリシーを使用して、すべてのクラウド アプリケーションのサインイン リスクのみに基づいた 2 段階認証を要求します。 この方法では、Azure Active Directory P2 ライセンスが必要です。 この方法の詳細については、「[Azure Active Directory Identity Protection](../identity-protection/overview.md#risky-sign-ins)」を参照してください。

ユーザーの状態を変更することで有効にする - 2 段階認証を要求するための従来の方法です。 これは、Azure MFA Server とクラウド内の Azure MFA の両方で機能します。 この方法を使用すると、ユーザーはサインインする際に**毎回** 2 段階認証が求められるようになります。また、この方法は条件付きアクセス ポリシーをオーバーライドします。 この方法に関する詳細については、「[ユーザーに 2 段階認証を要求する方法](howto-mfa-userstates.md)」を参照してください。

> [!Note]
> ライセンスと価格の詳細については、[Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) と [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) の価格に関するページを参照してください。

## <a name="choose-authentication-methods"></a>認証方法を選択する

組織の要件に基づいて、ユーザーの認証方法を少なくとも 1 つ有効にします。 Microsoft Authenticator アプリをユーザーに対して有効にすると、最適なユーザー エクスペリエンスを提供できることがわかっています。 使用可能な方法と、その設定方法を理解するには、[認証方法](concept-authentication-methods.md)に関する記事を参照してください。

## <a name="get-users-to-enroll"></a>ユーザーによる登録を促す

条件付きアクセス ポリシーを有効にすると、ユーザーはポリシーで保護されているアプリの次回使用時に強制的に登録されます。 すべてのユーザーに対して MFA を要求するポリシーをすべてのクラウド アプリで有効にした場合、このアクションがユーザーとヘルプデスクにとって不都合なものになりかねません。 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) の登録ポータルを使用する前に認証方法を登録するよう、ユーザーに依頼することをお勧めします。 多くの組織では、ポスター、卓上カード、メール メッセージを作成することで導入を促進しています。

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>条件付きアクセスを使用して Multi-Factor Authentication を有効にする

グローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。

### <a name="choose-verification-options"></a>認証オプションを選択する

Azure Multi-Factor Authentication を有効にする前に、どのような認証オプションを許可するかを組織で決定する必要があります。 この演習では、ほとんど全員が使用できる汎用的なオプションである、電話への呼び出しと電話へのテキスト メッセージを有効にします。 認証方法とその使用方法の詳細については、「[What are authentication methods? (認証方法についての説明)](concept-authentication-methods.md)」を参照してください。

1. **[Azure Active Directory]**、**[ユーザー]**、**[Multi-Factor Authentication]** の順に移動します。
   ![Azure portal 内の Azure AD の [ユーザー] ブレードから Multi-Factor Authentication ポータルにアクセスする](media/howto-mfa-getstarted/users-mfa.png) 
2. 開いた新しいタブで、**[サービスの設定]** に移動します。
3. **[検証オプション]** で、ユーザーが使用できる方法として次のチェック ボックスをオンにします。
   * 電話の呼び出し
   * 電話へのテキスト メッセージ

   ![Multi-Factor Authentication の [サービスの設定] タブで認証方法を構成する](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

4. [設定] メニューの **[保存]**
5. **[サービスの設定]** タブを閉じます

### <a name="create-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

1. グローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]**、**[条件付きアクセス]** の順に移動します
1. **[新しいポリシー]** を選択します
1. ポリシーにわかりやすい名前を付けます
1. **[ユーザーとグループ]** で次の手順を実行します。
   * **[Include]\(追加\)** タブで、**[All users]\(すべてのユーザー\)** ラジオ ボタンを選択します
   * 推奨: **[Exclude]\(除外\)** タブで、**[ユーザーとグループ]** のチェック ボックスをオンにし、ユーザーが特定の認証方法を利用できない場合の除外に使用するグループを選択します。
   * **[完了]** をクリックします
1. **[クラウド アプリ]** で、**[すべてのクラウド アプリ]** ラジオ ボタンを選択します
   * オプション: **[Exclude]\(除外\)** タブで、組織において MFA の必要がないクラウド アプリを選択します。
   * **[完了]** をクリックします
1. **[条件]** セクションで次の手順を実行します。
   * オプション: Azure Identity Protection を有効にしてある場合は、ポリシーの一部としてのサインイン リスクの評価を選択できます。
   * オプション: 信頼できる場所または名前付きの場所を構成してある場合は、これらの場所をポリシーに追加するか、ポリシーから除外するよう指定できます。
1. **[許可]** で、**[アクセス権の付与]** ラジオ ボタンが選択されていることを確認します
    * **[多要素認証を要求する]** チェック ボックスをオンにします。
    * **[選択]** をクリックします。
1. **[セッション]** セクションはスキップします
1. **[ポリシーを有効にする]** を **[オン]** に切り替えます。
1. **[作成]**

![パイロット グループ内の Azure portal のユーザーに対して MFA を有効にする条件付きアクセス ポリシーを作成する](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

### <a name="test-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication をテストする

条件付きアクセス ポリシーが機能することを確認するには、MFA を必要としないリソースへのログインをテストし、次に MFA を必要とする Azure portal へのログインをテストします。

1. InPrivate または incognito モードで新しいブラウザー ウィンドウを開き、[https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com) に移動します。
   * この記事の前提条件のセクションで作成したテスト ユーザーとしてログインし、MFA を完了するように求められないことを確認します。
   * ブラウザー ウィンドウを閉じます
2. InPrivate または incognito モードで新しいブラウザー ウィンドウを開き、[https://portal.azure.com](https://portal.azure.com) に移動します。
   * この記事の前提条件のセクションで作成したテスト ユーザーでログインし、今度は Azure Multi-Factor Authentication に登録してこれを使用するように要求されることを確認します。
   * ブラウザー ウィンドウを閉じます

## <a name="next-steps"></a>次の手順

これで、クラウドでの Azure Multi-Factor Authentication Server の設定が完了しました。

信頼できる IP、カスタム音声メッセージ、不正アクセスのアラートに関する追加の設定を構成する方法については、「[Azure Multi-Factor Authentication の設定を構成する](howto-mfa-mfasettings.md)」を参照してください。

Azure Multi-Factor Authentication のユーザー設定の管理について詳しくは、「[クラウドでの Azure Multi-Factor Authentication によるユーザー設定の管理](howto-mfa-userdevicesettings.md)」を参照してください。

[Azure Multi-Factor Authentication と Azure AD のセルフサービスによるパスワードのリセットで集中型登録を有効にする](concept-registration-mfa-sspr-converged.md)
