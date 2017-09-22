---
title: "Azure Active Directory 参加済みデバイスの設定 | Microsoft Docs"
description: "Azure Active Directory 参加済みデバイスの設定方法について説明します。"
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
ms.date: 08/29/2017
ms.author: markvi
ms.reviewer: jairoc
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: f548833cca27debb67cb155be0791299470f28dd
ms.contentlocale: ja-jp
ms.lasthandoff: 09/14/2017

---
# <a name="set-up-azure-active-directory-joined-devices"></a>Azure Active Directory 参加済みデバイスの設定

Azure Active Directory (Azure AD) のデバイス管理は、セキュリティとコンプライアンスの標準を満たすデバイスからユーザーがリソースにアクセスできるようにするのに役立ちます。 詳しくは、「[Azure Active Directory のデバイス管理の概要](device-management-introduction.md)」を参照してください。

[Azure AD 参加済みデバイス](device-management-introduction.md#azure-ad-joined-devices)を使用すると、組織が所有する Windows 10 デバイスを Azure AD の管理下に置くことができます。 これらのデバイスは、既に[オンプレミスの AD](device-management-introduction.md#hybrid-azure-ad-joined-devices) に参加していてはなりません。

このトピックでは、Windows 10 デバイスを Azure AD に登録する手順を紹介します。 

## <a name="prerequisites"></a>前提条件

始める前に、次のことを確認する必要があります。

- デバイスを Azure AD に参加させるためのアクセス許可がある。

    ![接続中](./media/device-management-azuread-joined-devices-setup/21.png)

- ユーザーあたりのデバイスの最大数をまだ超えていない 

    ![接続中](./media/device-management-azuread-joined-devices-setup/22.png)


詳しくは、「[デバイス設定の構成](device-management-azure-portal.md#configure-device-settings)」を参照してください。



## <a name="what-you-should-know"></a>知っておくべきこと


- Windows では、Azure AD 内の組織のディレクトリにデバイスが登録されます。

- 多要素認証チャレンジを渡すよう求められる場合があります。 IT 管理者は、このチャレンジを設定できます。

- Azure AD によって、デバイスをモバイル デバイス管理に登録する必要があるかどうかがチェックされます。 該当する場合は、デバイスが登録されます。

- Windows により、自動サインインを介して管理対象ユーザーがデスクトップにリダイレクトされます。

- フェデレーション ユーザーは、資格情報を入力するために Windows サインイン ページにリダイレクトされます。


## <a name="joining-a-device"></a>デバイスを参加させる

このセクションでは、Windows 10 デバイスを Azure AD に参加させる手順を示します。 Azure AD にデバイスが正常に参加すると、**[職場または学校にアクセスする]** ダイアログの **[Connected to \<your Azure AD\>]\(<ユーザーの Azure AD> に接続中\)** エントリで、そのことが示されます。

![接続中](./media/device-management-azuread-joined-devices-setup/13.png)


**Windows 10 デバイスを参加させるには:**

1. **[スタート]** メニューの **[設定]** をクリックします。

    ![設定](./media/device-management-azuread-joined-devices-setup/01.png)

2. **[アカウント]** をクリックします。

    ![アカウント](./media/device-management-azuread-joined-devices-setup/02.png)


3. **[職場または学校にアクセスする]** をクリックします。

    ![職場または学校にアクセスする](./media/device-management-azuread-joined-devices-setup/03.png)

4. **[職場または学校にアクセスする]** ダイアログ ボックスで、**[接続]** をクリックします。

    ![接続](./media/device-management-azuread-joined-devices-setup/04.png)


5. **[職場または学校アカウントの設定]** ダイアログ ボックスで、**[このデバイスを Azure Active Directory に参加させる]** をクリックします。

    ![接続](./media/device-management-azuread-joined-devices-setup/08.png)


6. **[Let's get you signed in]\(サインインしましょう\)** ダイアログ ボックスで、アカウント名 (たとえば someone@example.com) を入力し、**[次へ]** をクリックします。

    ![サインインしましょう](./media/device-management-azuread-joined-devices-setup/10.png)


6. **[パスワードの入力]** ダイアログ ボックスで、パスワードを入力し、**[サインイン]** をクリックします。

    ![パスワードの入力](./media/device-management-azuread-joined-devices-setup/05.png)


7. **[これがあなたの組織のネットワークであることを確認してください]** ダイアログ ボックスで、**[参加]** をクリックします。

    ![これがあなたの組織のネットワークであることを確認してください](./media/device-management-azuread-joined-devices-setup/11.png)


8. **[準備が完了しました]** ダイアログ ボックスで、**[完了]** をクリックします。

    ![準備が完了しました](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>確認

デバイスが Azure AD に参加しているかどうかを確認するには、デバイスで **[職場または学校にアクセスする]** ダイアログを確認します。

![接続中](./media/device-management-azuread-joined-devices-setup/13.png)

代わりに、`dsregcmd /status` というコマンドを実行することもできます。  
正常に参加しているデバイスでは、**AzureAdJoined** が **Yes** になります。

![接続中](./media/device-management-azuread-joined-devices-setup/14.png)

Azure AD ポータルでデバイスの設定を確認することもできます。

![接続中](./media/device-management-azuread-joined-devices-setup/15.png)

詳細については、[デバイスの検索](device-management-azure-portal.md#locate-devices)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。 

- [Azure Active Directory のデバイス管理の概要](device-management-introduction.md)

- [Azure Portal によるデバイスの管理](device-management-azure-portal.md)





