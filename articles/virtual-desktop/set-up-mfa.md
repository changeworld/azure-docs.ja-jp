---
title: Azure Virtual Desktop の Azure 多要素認証 - Azure
description: Azure Virtual Desktop のセキュリティを強化するために Azure 多要素認証を設定する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 12/10/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2e86597f0307699c2fc4cac9f48eac5b884e8df3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363609"
---
# <a name="enable-azure-multifactor-authentication-for-azure-virtual-desktop"></a>Azure Virtual Desktop で Azure 多要素認証を有効にする

>[!IMPORTANT]
> Azure Virtual Desktop (classic) のドキュメントからこのページに来た方は、この記事を読み終えた後 [Azure Virtual Desktop (classic) のドキュメントに戻る](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)ことを忘れないでください。

Azure Virtual Desktop の Windows 用クライアントは、Azure Virtual Desktop とローカル コンピューターを統合するための優れた手段です。 ですが、Azure Virtual Desktop アカウントを Windows Client に設定する場合、自分自身とユーザーの安全を保つための対策を行う必要があります。

初めてサインインすると、クライアントによって、ユーザー名、パスワード、Azure 多要素認証が要求されます。 その後、次回のサインイン時に、クライアントで、Azure Active Directory (AD) エンタープライズ アプリケーションからのトークンが記憶されます。 セッション ホストの資格情報を求めるプロンプトで **[このアカウントを記憶する]** を選択した場合、ユーザーは、クライアントの再起動後に、資格情報を再入力しなくてもサインインできます。

資格情報を記憶させることは便利ですが、企業のシナリオや個人のデバイスでのデプロイのセキュリティを低下させる可能性もあります。 ユーザーを保護するには、クライアントが Azure 多要素認証の資格情報をより頻繁に要求し続けるようにできます。 この記事では、Azure Virtual Desktop の条件付きアクセス ポリシーを使用して、この設定を有効にする方法を説明します。

## <a name="prerequisites"></a>前提条件

使用を開始するために必要なものを次に示します。

- Azure Active Directory Premium P1 または P2 を含むライセンスをユーザーに割り当てます。
- ユーザーがグループ メンバーとして割り当てられている Azure Active Directory グループ。
- すべてのユーザーに対して Azure 多要素認証を有効にします。 その方法の詳細については、「[ユーザーに 2 段階認証を要求する方法](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)」を参照してください。

> [!NOTE]
> 次の設定は、[Azure Virtual Desktop Web クライアント](https://rdweb.wvd.microsoft.com/arm/webclient/index.html)にも適用されます。

## <a name="create-a-conditional-access-policy"></a>条件付きアクセス ポリシーを作成する

Azure Virtual Desktop に接続するときに多要素認証が必要な条件付きアクセス ポリシーを作成する方法を、ここで説明します。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
2. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
3. **[新しいポリシー]** を選択します。
4. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
5. **[割り当て]** で、 **[ユーザーとグループ]** を選択します。
6. **[Include]\(含める\)** で **[ユーザーとグループの選択]**  >  **[ユーザーとグループ]** を選択し、[事前準備](#prerequisites)の段階で作成したグループを選択します。
7. **[Done]** を選択します。
8. **[クラウド アプリまたはアクション]**  >  **[Include]\(含める\)** で、 **[アプリを選択]** を選択します。
9. 使用している Azure Virtual Desktop のバージョンに合わせて、次のアプリのいずれか 1 つを選びます。
   
   - Azure Virtual Desktop (classic) を使用している場合は、これらのアプリを選びます。
       
       - **Azure Virtual Desktop** (アプリ ID 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Azure Virtual Desktop Client** (アプリ ID fa4345a4-a730-4230-84a8-7d9651b86739)、Web クライアントでポリシーを設定できます
       
        その後、手順 11 に進みます。

   - Azure Virtual Desktop を使用している場合は、代わりにこのアプリを選びます。
       
       -  **Azure Virtual Desktop** (アプリ ID 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        その後、手順 10 に進みます。

   >[!IMPORTANT]
   > Azure Virtual Desktop Azure Resource Manager Provider というアプリは選ばないでください (50e95039-b200-4007-bc97-8d5790743a63)。 このアプリは、ユーザー フィードを取得するためだけに使用され、多要素認証を持つことはできません。
   > 
   > Azure Virtual Desktop (classic) を使用している場合に、条件付きアクセス ポリシーによって、Azure Virtual Desktop のアプリ ID を除くすべてのアクセスがブロックされるときは、アプリ ID 9cdead84-a844-4324-93f2-b2e6bb768d07 をポリシーに追加することで、この問題を解消できます。 このアプリ ID を追加しないと、Azure Virtual Desktop (classic) リソースのフィード検出がブロックされます。

10. **[条件]**  >  **[クライアント アプリ]** に移動します。 **[構成]** で、 **[はい]** を選択し、ポリシーを適用する場所を選択します。
    
    - ポリシーを Web クライアントに適用する場合は、 **[ブラウザー]** を選択します。
    - ポリシーを他のクライアントに適用する場合は、 **[モバイル アプリとデスクトップ クライアント]** を選択します。
    - ポリシーをすべてのクライアントに適用する場合は、両方のチェック ボックスをオンにします。
   
    > [!div class="mx-imgBorder"]
    > ![クライアント アプリ ページのスクリーンショット。 ユーザーが [モバイル アプリとデスクトップ クライアント] チェック ボックスをオンにしました。](media/select-apply.png)

11. アプリを選択したら、 **[選択]** を選び、 **[完了]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[クラウド アプリまたはアクション] ページのスクリーンショット。 Azure Virtual Desktop と Azure Virtual Desktop Client アプリが赤色で強調表示されている。](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >選択するアプリのアプリ ID を検索するには、 **[エンタープライズ アプリケーション]** に移動し、[アプリケーションの種類] ドロップダウン メニューから **[Microsoft アプリケーション]** を選択します。

12. **[アクセス制御]**  >  **[許可]** で、 **[アクセス権の付与]** 、 **[Require multi-factor authentication]\(多要素認証を要求する\)** の順に選択し、 **[Select]\(選択する\)** を選択します。
13. **[アクセス制御]**  >  **[セッション]** で、 **[サインインの頻度]** を選択し、プロンプトの間隔として設定する時間の値を設定してから **[選択]** を選択します。 たとえば、値を **1** に設定し、単位を **[時間]** に設定した場合、最終接続の 1 時間後に接続が起動された場合に多要素認証が必要になります。
14. 設定を確認し、 **[Enable policy]\(ポリシーの有効化\)** を **[オン]** に設定します。
15. **[作成]** を選択して、ポリシーを有効化します。

>[!NOTE]
>Web クライアントでブラウザーを使用して Azure Virtual Desktop にサインインすると、クライアント アプリ ID が a85cf173-4192-42f8-81fa-777a763e6e2c (Azure Virtual Desktop client) としてログに記載されます。 これは、クライアント アプリが、条件付きアクセス ポリシーが設定されたサーバー アプリ ID に内部的にリンクされているためです。 

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス ポリシーの詳細について確認する](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [ユーザー サインインの頻度の詳細について確認する](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
