---
title: Azure AD Connect クラウド プロビジョニングの新しいエージェント構成
description: このトピックでは、クラウド プロビジョニングのインストール方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 956962503dcb3a6355d455ff96744143c5965d1e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795470"
---
# <a name="azure-ad-connect-cloud-provisioning-new-configuration"></a>Azure AD Connect クラウド プロビジョニングの新しい構成

エージェントをインストールしたら、Azure Portal にサインインしてプロビジョニングを構成する必要があります。  エージェントを有効にするには、次の手順に従います。

## <a name="configure-provisioning"></a>プロビジョニングの構成
プロビジョニングを構成するには次の手順に従います。

1.  Azure AD ポータルで **[Azure Active Directory]** をクリックします。
2.  **[Azure AD Connect]** をクリックします。
3.  **[プロビジョニングの管理 (プレビュー)]** を選択します。
![](media/how-to-configure/manage1.png)

4.  **[新しい構成]** をクリックします。
5.  構成画面では、オンプレミス ドメインが事前に設定されています。
6. **[通知用メール]** に入力します。 このメールに通知が送信されます 
7. プロビジョニングが正常でない場合。  
8. セレクターを **[有効]** に移動し、 **[保存]** をクリックします。
![](media/tutorial-single-forest/configure2.png)

## <a name="scoping-provisioning-to-specific-users-and-groups"></a>特定のユーザーとグループに対するプロビジョニングのスコープ設定
特定のユーザーとグループだけを同期するようにエージェントをスコープ設定したい場合は、それを行うことができます。 オンプレミスの AD グループまたは組織単位を使用して、スコープを設定できます。 1 つの構成内でグループと組織単位を構成することはできません。 

1.  Azure AD ポータルで **[Azure Active Directory]** をクリックします。
2.  **[Azure AD Connect]** をクリックします。
3.  **[プロビジョニングの管理 (プレビュー)]** を選択します。
4.  **[構成]** の下で、自分の構成をクリックします。  
![](media/how-to-configure/scope1.png)

5.  **[構成]** で **[すべてのユーザー]** を選択して、構成規則のスコープを変更します。
![](media/how-to-configure/scope2.png)

6. 右側で、グループの識別名を入力して **[追加]** をクリックし、セキュリティ グループだけを含むようにスコープを変更できます。
![](media/how-to-configure/scope3.png)

7. または、特定の OU だけを含むように変更します。 **[完了]** 、 **[保存]** の順にクリックします。
![](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>プロビジョニングを再開する 
スケジュールされた次回の実行を待ちたくない場合は、[プロビジョニングを再開する] ボタンを使用してプロビジョニングの実行をトリガーできます。 
1.  Azure AD ポータルで **[Azure Active Directory]** をクリックします。
2.  **[Azure AD Connect]** をクリックします。
3.  **[プロビジョニングの管理 (プレビュー)]** を選択します。
4.  **[構成]** の下で、自分の構成をクリックします。  
![](media/how-to-configure/scope1.png)

5.  上部にある **[プロビジョニングを再開する]** をクリックします。

## <a name="removing-a-configuration"></a>構成の削除
構成を削除する場合は、次の手順に従って行うことができます。

1.  Azure AD ポータルで **[Azure Active Directory]** をクリックします。
2.  **[Azure AD Connect]** をクリックします。
3.  **[プロビジョニングの管理 (プレビュー)]** を選択します。
4.  **[構成]** の下で、自分の構成をクリックします。  
![](media/how-to-configure/scope1.png)

5.  上部にある **[削除]** をクリックします。
![](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>構成を削除する前に確認が行われないため、 **[削除]** をクリックする前に、これが行いたいアクションであることを必ず確認してください。


## <a name="next-steps"></a>次の手順 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
