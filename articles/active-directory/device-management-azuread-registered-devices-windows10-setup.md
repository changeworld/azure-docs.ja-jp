---
title: "Azure Active Directory 登録済み Windows 10 デバイスの設定 | Microsoft Docs"
description: "Azure Active Directory 登録済み Windows 10 デバイスを設定する方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: e2554450bdb426cff50f302132158f39f5630ee3
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---

# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Azure Active Directory 登録済み Windows 10 デバイスの設定

Azure Active Directory (Azure AD) のデバイス管理は、セキュリティとコンプライアンスの標準を満たすデバイスからユーザーがリソースにアクセスできるようにするのに役立ちます。 詳しくは、「[Azure Active Directory のデバイス管理の概要](device-management-introduction.md)」を参照してください。

[Azure AD 登録済みデバイス](device-management-introduction.md#azure-ad-registered-devices)を使用すると、**Bring Your Own Device (BYOD)** シナリオに対応し、ユーザーが個人所有のデバイスを使用して組織のリソースにアクセスできるようにすることが可能です。  

Azure AD では、次の環境の Azure AD 登録済みデバイスを設定できます。

- Windows 10
- iOS
- Android
- macOS  

このトピックでは、Windows 10 デバイスを Azure AD に登録する手順を紹介します。 


## <a name="prerequisites"></a>前提条件

始める前に、次のことを確認する必要があります。

- デバイスを登録するためのアクセス許可 

    ![登録](./media/device-management-azuread-registered-devices-windows10-setup/21.png)

- ユーザーあたりのデバイスの最大数をまだ超えていない 

    ![登録](./media/device-management-azuread-registered-devices-windows10-setup/22.png)

詳細については、「[デバイス設定の構成](device-management-azure-portal.md#configure-device-settings)」を参照してください。

## <a name="what-you-should-know"></a>知っておくべきこと

デバイスを登録する場合は、次の点に注意してください。

- Windows では、Azure AD 内の組織のディレクトリにデバイスが登録されます。

- 多要素認証チャレンジを渡すよう求められる場合があります。 IT 管理者は、このチャレンジを設定できます。

- Azure AD によって、デバイスをモバイル デバイス管理に登録する必要があるかどうかがチェックされます。 該当する場合は、デバイスが登録されます。

- Windows により、自動サインインを介して管理対象ユーザーがデスクトップにリダイレクトされます。

- フェデレーション ユーザーは、資格情報を入力するために Windows サインイン ページにリダイレクトされます。


## <a name="register-a-device"></a>デバイスの登録

Windows 10 デバイスを Azure AD に登録するには、次の手順を実行します。 デバイスが Azure AD に正常に登録されると、**[職場または学校にアクセスする]** ページに **[職場または学校アカウント]** のエントリと共にその旨が表示されます。

![登録](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


Windows 10 デバイスを登録するには:

1. **[スタート]** メニューの **[設定]** をクリックします。

    ![[設定] を選択する](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. **[アカウント]** をクリックします。

    ![[アカウント] を選択する](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. **[職場または学校にアクセスする]** をクリックします。

    ![[職場または学校にアクセスする] を選択する](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. **[職場または学校にアクセスする]** ページで、**[接続]** をクリックします。

    ![[職場または学校にアクセスする] ページ](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. **[職場または学校アカウントの設定]** ページで、アカウント名 (例: someone@example.com) を入力し、**[次へ]** をクリックします。

    ![[職場または学校アカウントの設定] ページ](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. **[パスワードの入力]** ページで、パスワードを入力し、**[次へ]** をクリックします。

    ![パスワードの入力](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. **[You're all set]\(これで完了です\)** ページで、**[完了]** をクリックします。

    ![[You're all set]\(これで完了です\) ページ](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>確認

デバイスが Azure AD に参加しているかどうかを確認するには、デバイスで **[職場または学校にアクセスする]** ページを確認します。

![職場または学校アカウントの状態](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

または、Azure AD ポータルでデバイスの設定を確認することもできます。

![Azure AD 登録済みデバイス](./media/device-management-azuread-registered-devices-windows10-setup/09.png)


## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。 

- [Azure Active Directory のデバイス管理の概要](device-management-introduction.md)

- [Azure Portal によるデバイスの管理](device-management-azure-portal.md)





