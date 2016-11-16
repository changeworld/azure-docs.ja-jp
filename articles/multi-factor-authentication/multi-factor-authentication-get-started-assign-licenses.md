---
title: "Azure MFA のライセンスを割り当てる | Microsoft Docs"
description: "Microsoft Azure Multi-Factor Authentication のユーザー ライセンスを割り当てる方法について説明します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 514ef423-8ee6-4987-8a4e-80d5dc394cf9
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c93db18866478556e91da8acbc9f0c749b65dc62


---
# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Azure MFA、Azure AD Premium、または Enterprise Mobility のライセンスをユーザーに割り当てる
Azure MFA、Azure AD Premium、または Enterprise Mobility Suite のライセンスを購入済みである場合、Multi-Factor Auth プロバイダーを作成する必要はありません。 ユーザーにライセンスを割り当てたら、ユーザーに対して MFA の有効化を始めることができます。

## <a name="to-assign-a-license"></a>ライセンスを割り当てる方法
1. [Azure クラシック ポータル](https://manage.windowsazure.com)に管理者としてサインインします。
2. 左側で、 **[Active Directory]**を選択します。
3. [Active Directory] ページで、有効にするユーザーが存在するディレクトリをダブルクリックします。
4. ディレクトリ ページの上部にある **[ライセンス]**を選択します。
   ![Assign Licenses](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. [ライセンス] ページで、**[Azure Multi-Factor Authentication]**、**[Active Directory Premium]**、または **[Enterprise Mobility Suite]** を選択します。  1 つしか所有していない場合は、それが自動的に選択されます。
6. ページの下部にある **[割り当て]**をクリックします。
   ![Assign Licenses](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
7. 表示されるボックスで、ライセンスを割り当てるユーザーまたはグループの横をクリックします。  緑色のチェック マークが表示されます。
8. チェック マークのアイコンをクリックして変更を保存します。
   ![Assign Licenses](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
9. 割り当てられたライセンスの数と、割り当てに失敗した場合はその数を示すメッセージが表示されます。  **[OK]**をクリックします。
   ![Assign Licenses](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)




<!--HONumber=Nov16_HO2-->


