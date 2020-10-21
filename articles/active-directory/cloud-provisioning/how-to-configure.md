---
title: Azure AD Connect クラウド プロビジョニングの新しいエージェント構成
description: この記事では、クラウド プロビジョニングのインストール方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628897"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Azure AD Connect クラウドベース プロビジョニングの新しい構成を作成する

エージェントをインストールしたら、Azure portal にサインインして Azure Active Directory (Azure AD) Connect クラウド プロビジョニングを構成する必要があります。 エージェントを有効にするには、次の手順に従います。

## <a name="configure-provisioning"></a>プロビジョニングの構成
プロビジョニングを構成するのには、次の手順に従います。

 1. Azure portal で、 **[Azure Active Directory]** を選択します
 2. **[Azure AD Connect]** を選びます。
 3. **[プロビジョニングの管理]** を選択します。

 ![プロビジョニングを管理する](media/how-to-configure/manage1.png)
 
 4. **[新しい構成]** を選択します。
 5. 構成画面で、ドメインと、パスワード ハッシュ同期を有効にするかどうかを選択します。**Create** をクリックしてください。  
 
 ![新しい構成を作成する](media/how-to-configure/configure1.png)


 6.  [Edit provisioning configuration]\(プロビジョニングの構成の編集\) 画面が開きます。

   ![構成を編集する](media/how-to-configure/configure2.png)

 7. **[通知用メール]** に入力します。 このメールは、プロビジョニングが正常でない場合に通知されます。
 8. セレクターを [有効] に移動し、[保存] を選択します。

## <a name="scope-provisioning-to-specific-users-and-groups"></a>特定のユーザーとグループに対するプロビジョニングのスコープ設定
オンプレミスの Active Directory グループまたは組織単位を使用して、特定のユーザーとグループを同期するようにエージェントをスコープ設定できます。 1 つの構成内でグループと組織単位を構成することはできません。 

 1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
 2. **[Azure AD Connect]** を選びます。
 3. **[プロビジョニングの管理 (プレビュー)]** を選択します。
 4. **[構成]** の下で、自分の構成を選択します。

 ![構成セクション](media/how-to-configure/scope1.png)
 
 5. **[構成]** で **[Edit scoping filters]\(スコープ フィルターの編集\)** を選択して、構成規則のスコープを変更します。
 ![スコープの編集](media/how-to-configure/scope3.png)
 7. 右側で、スコープを変更できます。  終わったら、 **[完了]** と **[保存]** をクリックします。
 8. スコープを変更したら、 [プロビジョニングを再開](#restart-provisioning)して、変更の即時同期を開始する必要があります。

## <a name="attribute-mapping"></a>属性マッピング
Azure AD Connect クラウド プロビジョニングを使用すると、オンプレミスのユーザーまたはグループ オブジェクトと Azure AD 内のオブジェクトの間に属性を簡単にマップすることができます。  既定の属性マッピングをビジネスのニーズに合わせてカスタマイズできます。 そのため、既存の属性マッピングを変更、削除したり、新規の属性マッピングを作成したりすることができます。  詳細については、[属性マッピング](how-to-attribute-mapping.md)に関する記事を参照してください。

## <a name="on-demand-provisioning"></a>オンデマンド プロビジョニング
Azure AD Connect クラウド プロビジョニングを使用すると、これらの変更を 1 人のユーザーまたは 1 つのグループに適用することで、構成の変更をテストできます。  これを使用して、構成に対して行われた変更が正しく適用されたこと、および Azure AD に正しく同期されていることの妥当性を確認し、検証することができます。  詳細については、[オンデマンド プロビジョニング](how-to-on-demand-provision.md)に関する記事を参照してください。

## <a name="restart-provisioning"></a>プロビジョニングを再開する 
スケジュールされた次回の実行を待ちたくない場合は、**[プロビジョニングを再開する]** ボタンを使用してプロビジョニングの実行をトリガーします。 
 1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
 2. **[Azure AD Connect]** を選びます。
 3.  **[プロビジョニングの管理 (プレビュー)]** を選択します。
 4. **[構成]** の下で、自分の構成を選択します。

   ![プロビジョニングを再開するための構成の選択](media/how-to-configure/scope1.png)

 5. 上部にある **[プロビジョニングを再開する]** を選択します。

## <a name="remove-a-configuration"></a>構成の削除
構成を削除するには、次の手順に従います。

 1.  Azure Portal で、 **[Azure Active Directory]** を選びます。
 2. **[Azure AD Connect]** を選びます。
 3. **[プロビジョニングの管理 (プレビュー)]** を選択します。
 4. **[構成]** の下で、自分の構成を選択します。
   
   ![構成を削除するための構成の選択](media/how-to-configure/scope1.png)

 5. 構成画面の上部にある **[削除]** を選択します。

>[!IMPORTANT]
>構成を削除する前に確認は行われません。 **[削除]** を選択する前に、これが実行するアクションであることを確認してください。


## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
