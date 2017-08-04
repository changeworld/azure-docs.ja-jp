---
title: "Azure Active Directory Domain Services: 概要 | Microsoft Docs"
description: "Azure Portal を使って Azure Active Directory Domain Services を有効にする (プレビュー)"
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
ms.date: 07/15/2017
ms.author: maheshu
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 7f420d60862adf61e4f21e5abac2932a742bd55d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal-preview"></a>Azure Portal を使って Azure Active Directory Domain Services を有効にする (プレビュー)


## <a name="before-you-begin"></a>開始する前に
[Azure Active Directory Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)のページを参照してください。


## <a name="task-2-configure-network-settings"></a>タスク 2: ネットワーク設定を構成する
次の構成タスクでは、Azure 仮想ネットワークを作成し、その中に専用サブネットを作成します。 仮想ネットワーク内のこのサブネットで、Azure Active Directory Domain Services を有効にします。 既存の仮想ネットワークを選択して、その中に専用サブネットを作成することもできます。

1. **[仮想ネットワーク]** をクリックし、仮想ネットワークを選択します。
2. **[仮想ネットワークの選択]** ブレードに、既存の仮想ネットワークがすべて表示されます。 **[基本]** ウィザード ページで選択したリソース グループと Azure の場所に属している仮想ネットワークのみが表示されます。

3. Azure AD Domain Services を有効にする仮想ネットワークを選択します。 新しい仮想ネットワークを作成する場合は、**[新規作成]** をクリックします。 Azure AD Domain Services の専用サブネットを使用することを強くお勧めします。 既存の仮想ネットワークを選択する場合、[仮想ネットワークの拡張機能を使用して専用サブネットを作成し](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)、そのサブネットを選択します。 

    ![仮想ネットワークを選択する](./media/getting-started/domain-services-blade-network-pick-vnet.png)

4. **[サブネット]** をクリックして、新しい管理対象ドメインを有効にする、仮想ネットワーク内の専用サブネットを選択します。 **[サブネットの作成]** ブレードで、サブネットの名前を指定し、完了したら **[OK]** をクリックします。 たとえば、"DomainServices" という名前のサブネットを作成すると、サブネット内にデプロイされている内容を他の管理者が理解しやすくなります。

    ![仮想ネットワーク内のサブネットを選択する](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **サブネットを選択するためのガイドライン**
  > 1. Azure AD Domain Services の専用サブネットをご使用ください。 他の仮想マシンをこのサブネットにデプロイしないでください。 この構成を使用すると、管理対象ドメインに支障をきたすことなく、ワークロード/仮想マシンのネットワーク セキュリティ グループ (NSG) を構成できます。 詳細については、[Azure Active Directory Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)を参照してください。
  2. ゲートウェイ サブネットは、サポートされている構成ではないため、Azure AD Domain Services のデプロイでは選択しないでください。
  3. 選択したサブネットが、利用可能なアドレス空間を十分に持ち、使用可能な IP アドレスが少なくとも 3 ～5 個あることを確認します。
  >

5. 完了したら、**[OK]** をクリックして、ウィザードの **[管理者グループ]** ページに移動します。


## <a name="next-step"></a>次のステップ
[タスク 3: 管理グループを構成して Azure AD Azure AD Domain Services を有効にする](active-directory-ds-getting-started-admingroup.md)

