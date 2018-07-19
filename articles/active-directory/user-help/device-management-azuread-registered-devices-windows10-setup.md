---
title: Azure Active Directory 登録済みデバイスの設定 | Microsoft Docs
description: Azure Active Directory 登録済みデバイスを設定する方法について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 7ce632c76a86fb00101db6664e9e79615484f9a1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39058114"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Azure Active Directory 登録済み Windows 10 デバイスの設定

Azure Active Directory (Azure AD) のデバイス管理を使用して、ユーザーがセキュリティとコンプライアンスの基準と一致するデバイスからリソースにアクセスしていることを保証できます。 詳しくは、「[Azure Active Directory のデバイス管理の概要](../device-management-introduction.md)」を参照してください。

**Bring Your Own Device (BYOD)** シナリオを有効にする場合は、Azure AD 登録済みデバイスを構成します。 Azure AD では、Windows 10、iOS、Android、macOS で Azure AD 登録済みデバイスを構成できます。 この記事では、Windows 10 デバイスの関連手順を示します。 


## <a name="before-you-begin"></a>開始する前に

Windows 10 デバイスを登録するには、デバイスを登録できるようにデバイス登録サービスを構成する必要があります。 さらに、登録されたデバイス数を、構成済みの最大数よりも少なくする必要があります。 詳しくは、「[デバイス設定の構成](../device-management-azure-portal.md#configure-device-settings)」を参照してください。

## <a name="what-you-should-know"></a>知っておくべきこと

デバイスを登録するときは、以下の点に注意してください。

- Windows では、Azure AD 内の組織のディレクトリにデバイスが登録されます。

- Multi-Factor Authentication で必要な情報の入力を求められる場合があります。 この認証は、IT 管理者が構成できます。

- Azure AD によって、このデバイスをモバイル デバイス管理に登録する必要があるかどうかがチェックされ、必要に応じて登録されます。

- 管理対象ユーザーの場合は、自動サインインによりデスクトップに移動します。

- フェデレーション ユーザーの場合は、資格情報を入力するための Windows サインイン画面が表示されます。


## <a name="registering-a-device"></a>デバイスの登録

このセクションでは、Windows 10 デバイスを Azure AD に登録する手順を示します。 正常に登録されたデバイスが、**職場または学校アカウント** エントリとともに表示されます。

![Register](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**Windows 10 デバイスを登録するには:**

1. **[スタート]** メニューの **[設定]** をクリックします。

    ![設定](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. **[アカウント]** をクリックします。

    ![アカウント](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. **[職場または学校にアクセスする]** をクリックします。

    ![職場または学校にアクセスする](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. **[職場または学校にアクセスする]** ダイアログ ボックスで、**[接続]** をクリックします。

    ![接続](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. **[職場または学校アカウントの設定]** ダイアログで、アカウント名 (例: someone@example.com) を入力し、**[次へ]** をクリックします。

    ![接続](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. **[パスワードの入力]** ダイアログ ボックスで、パスワードを入力し、**[次へ]** をクリックします。

    ![接続](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. **[準備が完了しました]** ダイアログ ボックスで、**[完了]** をクリックします。

    ![接続](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>確認

デバイスが Azure AD に参加しているかどうかを確認するには、デバイスで **[職場または学校にアクセスする]** ダイアログを確認します。

![Register](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

または、Azure AD ポータルでデバイスの設定を確認することもできます。

![Register](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>次の手順

- 詳しくは、「[Azure Active Directory のデバイス管理の概要](../device-management-introduction.md)」を参照してください。

- Azure AD ポータルでのデバイス管理の詳細については、[Azure Portal によるデバイスの管理](../device-management-azure-portal.md)に関するページを参照してください。




