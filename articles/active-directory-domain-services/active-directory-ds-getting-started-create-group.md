---
title: "Azure Active Directory Domain Services: Azure AD DC 管理者グループの作成 | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスの概要"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 99607cf642bb6767c845ceb6fb4e62b1c15834f9
ms.lasthandoff: 04/12/2017


---
# <a name="get-started-with-azure-active-directory-domain-services"></a>Azure AActive Directory Domain Services の概要
この記事では、Azure Active Directory (Azure AD) テナントの Azure Active Directory Domain Services (Azure AD DS) を有効にするために必要な構成タスクについて説明します。

## <a name="task-1-create-the-azure-ad-dc-administrators-group"></a>タスク 1: "Azure AD DC 管理者" グループの作成
最初のタスクは、Azure AD テナントでの管理グループの作成です。 この特別な管理グループは、 *AAD DC 管理者*と呼ばれます。 このグループのメンバーには、Azure Active Directory Domain Services 管理対象ドメインにドメイン参加するコンピューターへの管理権限が付与されます。 ドメインに参加しているコンピューターでは、このグループは管理者グループに追加されます。 さらに、このグループのメンバーは、リモート デスクトップを使用して、ドメインに参加しているコンピューターにリモート接続できます。  

> [!NOTE]
> Azure Active Directory Domain Services を使用して作成された管理対象ドメインでは、ドメイン管理者権限またはエンタープライズ管理者権限がありません。 管理対象ドメインでは、これらの権限はサービスによって予約されており、テナント内でユーザーが使用することはできません。 ただし、この構成タスクで作成した特殊な管理者グループを使用して、特権操作の一部を実行することはできます。 これらの操作には、ドメインへのコンピューターの参加、ドメインに参加しているコンピューターでの管理グループへの所属、グループ ポリシーの構成などが含まれます。
>

この構成タスクでは、管理グループを作成し、そのグループにディレクトリ内の 1 人以上のユーザーを追加します。 Azure Active Directory Domain Services の管理グループを作成するには、次の手順に従います

1. [Azure クラシック ポータル](https://manage.windowsazure.com)に移動します。
2. 左側のウィンドウで、**[Active Directory]** ボタンを選択します。
3. Azure Active Directory Domain Services を有効にする Azure AD テナント (ディレクトリ) を選択します。 Azure AD ディレクトリごとに作成できるドメインは 1 つだけです。

    ![Azure AD ディレクトリの選択](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. **[ディレクトリのプレビュー]** ページで、**[グループ]** タブをクリックします。
5. Azure AD テナントにグループを追加するには、ウィンドウの下部にある作業ウィンドウで **[グループの追加]** をクリックします。

    ![[グループの追加] ボタン](./media/active-directory-domain-services-getting-started/add-group-button.png)
6. **[グループの追加]** ダイアログ ボックスで、**AAD DC Administrators** という名前のグループを作成し、**[グループの種類]** を **[セキュリティ]** に設定します。

   > [!WARNING]
   > Azure Active Directory Domain Services 管理対象ドメイン内でアクセスを有効にするには、この名前をそのまま使用してグループを作成します。
   >
   >

    ![[グループの追加] ダイアログ ボックス](./media/active-directory-domain-services-getting-started/create-admin-group.png)
7. Azure Active Directory Domain Services 内で管理権限を付与するために、このグループが使用されることを他のユーザーが理解できるように、**[説明]** ボックスにグループの説明を入力します。
8. グループを作成したら、グループ名をクリックして、グループのプロパティを表示します。 
9. ユーザーをこのグループのメンバーとして追加するには、ウィンドウの下部で、**[メンバーの追加]** ボタンをクリックします。

    ![[グループ メンバーの追加] ボタン](./media/active-directory-domain-services-getting-started/add-group-members-button.png)
10. **[メンバーの追加]** ダイアログ ボックスで、このグループのメンバーにするユーザーを選択し、右下のチェック ボックスをオンにします。

    ![管理者グループへのユーザーの追加](./media/active-directory-domain-services-getting-started/add-group-members.png)

## <a name="next-steps"></a>次のステップ
タスク 2: [Azure 仮想ネットワークの作成または選択](active-directory-ds-getting-started-vnet.md)
  

