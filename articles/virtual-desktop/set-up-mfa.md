---
title: Windows Virtual Desktop 用に Azure Multi-Factor Authentication を設定する - Azure
description: Windows Virtual Desktop でセキュリティを強化するために Azure Multi-Factor Authentication を設定する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b470f9278bdca94d1fe98c64b11b070fb36cb075
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998472"
---
# <a name="set-up-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication を設定する

Windows Virtual Desktop 用の Windows クライアントは、Windows Virtual Desktop をローカル コンピューターに統合するための優れたオプションです。 ただし、Windows Virtual Desktop アカウントを Windows クライアントに構成する場合、自分自身とユーザーの安全を維持するために必要な特定の手段があります。

初めてサインインすると、クライアントによって、ユーザー名、パスワード、Azure MFA が要求されます。 その後、次回のサインイン時に、クライアントで、Azure Active Directory (AD) エンタープライズ アプリケーションからのトークンが記憶されます。 **[このアカウントを記憶する]** を選択した場合、ユーザーは、クライアントの再起動後に、資格情報を再入力しなくてもサインインでき ます。

資格情報を記憶させることは便利ですが、企業のシナリオや個人のデバイスでのデプロイのセキュリティを低下させる可能性もあります。 ユーザーを保護するには、クライアントが Azure Multi-Factor Authentication (MFA) 資格情報を要求し続けることを確認する必要があります。 この記事では、Windows Virtual Desktop の条件付きアクセス ポリシーを構成して、この設定を有効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

使用を開始するために必要なものを次に示します。

- すべてのユーザーに次のライセンスのいずれかを割り当てます。
  - Microsoft 365 E3 または E5
  - Azure Active Directory Premium P1 または P2
  - Enterprise Mobility + Security E3 または E5
- ユーザーがグループ メンバーとして割り当てられている Azure Active Directory グループ。
- すべてのユーザーに対して Azure MFA を有効にします。 その方法の詳細については、「[ユーザーに 2 段階認証を要求する方法](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)」を参照してください。

>[!NOTE]
>次の設定は、[Windows Virtual Desktop Web クライアント](https://rdweb.wvd.microsoft.com/webclient/index.html)にも適用されます。

## <a name="opt-in-to-the-conditional-access-policy"></a>条件付きアクセス ポリシーを選択する

1. **Azure Active Directory** を開きます。

2. **[すべてのアプリケーション]** タブに移動します。[アプリケーションの種類] ドロップダウンメニューで、 **[エンタープライズ アプリケーション]** を選択し、 **[Windows Virtual Desktop Client]** を検索します。

    ![[すべてのアプリケーション] タブのスクリーンショット。ユーザーが検索バーに「windows virtual desktop client」と入力すると、アプリが検索結果に表示されます。](media/all-applications-search.png)

3. **[条件付きアクセス]** を選択します。

    ![ユーザーが [条件付きアクセス] タブにマウス カーソルを合わせていることを示すスクリーンショット。](media/conditional-access-location.png)

4. **[新しいポリシー]** を選択します。

   ![[条件付きアクセス] ページのスクリーンショット。 ユーザーは、新しいポリシー ボタンの上にマウス カーソルを置いています。](media/new-policy-button.png)

5. **規則**の**名前**を入力し、前提条件で作成した**グループ**の*名前を**選択**します。

6. **[選択]** を選択し、 **[完了]** を選択します。

7. 次に、 **[クラウド アプリまたは操作]** を開きます。

8. **[選択]** パネルで、 **[Windows Virtual Desktop]** Enterprise アプリを選択します。

    ![[クラウド アプリまたは操作] ページのスクリーンショット。 ユーザーは、その横にあるチェック マークをオンにして、Windows Virtual Desktop アプリを選択しました。 選択したアプリが赤で強調表示されています。](media/cloud-apps-select.png)
    
    >[!NOTE]
    >次のイメージに示すように、画面の左側で Windows Virtual Desktop Client アプリが選択されていることもわかるはずです。 ポリシーを機能させるには、Windows Virtual Desktop と Windows Virtual Desktop Client Enterprise アプリの両方が必要です。
    >
    > ![[クラウド アプリまたは操作] ページのスクリーンショット。 Windows Virtual Desktop および Windows Virtual Desktop Client アプリが赤色で強調表示されています。](media/cloud-apps-enterprise-selected.png)

9. **[選択]** を選択します

10. 次に、 **[許可]** を開きます 

11. **[多要素認証が必要]** を選択し、 **[選択したコントロールのいずれかが必要]** を選択します。
   
    ![[許可] ページのスクリーンショット。 [多要素認証が必要] が選択されています。](media/grant-page.png)

    >[!NOTE]
    >組織内に MDM に登録されたデバイスがあり、それらに MFA プロンプトを表示させないようにする場合は、 **[デバイスは準拠としてマーク済みである必要があります]** を選択することもできます。

12. **[セッション]** を選択します。

13. **[サインインの頻度]** を **[アクティブ]** に設定し、その値を **[1 時間]** に変更します。

    ![[セッション] ページのスクリーンショット。 [セッション] メニューに、サインインの頻度のドロップダウン メニューが [1] と [時間] に変更されたことが示されています。](media/sign-in-frequency.png)
   
    >[!NOTE]
    >Windows Virtual Desktop 環境のアクティブなセッションは、ポリシーを変更したときに引き続き機能します。 ただし、切断またはサインオフした場合、60 分後に資格情報を再度入力する必要があります。 設定を変更すると、組織のセキュリティ ポリシーと一致している限り、タイムアウト期間を必要なだけ延長できます。
    >
    >既定の設定は 90 日のローリング ウィンドウです。つまり、ユーザーがコンピューターで 90 日間以上非アクティブだった後に、リソースにアクセスしようとしたときに、再度サインインするようにクライアントから要求されます。

14. ポリシーを有効にします。

15. **[作成]** を選択して、ポリシーを確認します。

すべて完了しました。 ポリシーを自由にテストして、許可リストが意図したとおりに機能することを確認してください。
