---
title: "Azure Active Directory Domain Services: 概要 | Microsoft Docs"
description: "Azure Portal を使用して Azure Active Directory Domain Services を有効にする"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maheshu
ms.openlocfilehash: 858adf2b0a0431078561a6f0edf80860927f8233
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal を使用して Azure Active Directory Domain Services を有効にする


## <a name="before-you-begin"></a>開始する前に
[Azure Active Directory Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)のページを参照してください。


## <a name="task-2-configure-network-settings"></a>タスク 2: ネットワーク設定を構成する
次の構成タスクでは、Azure 仮想ネットワークを作成し、その中に専用サブネットを作成します。 仮想ネットワーク内のこのサブネットで、Azure Active Directory Domain Services を有効にします。 既存の仮想ネットワークを選択して、その中に専用サブネットを作成することもできます。

1. **[仮想ネットワーク]** をクリックし、仮想ネットワークを選択します。
    > [!NOTE]
    > **新しいデプロイでは、クラシック仮想ネットワークはサポートされません。** 新しいデプロイでは、クラシック仮想ネットワークはサポートされません。 クラシック仮想ネットワークにデプロイされている既存の管理対象ドメインは、引き続きサポートされます。 近いうちに、既存の管理対象ドメインをクラシック仮想ネットワークから Resource Manager 仮想ネットワークに移行する機能が提供される予定です。
    >

2. **[仮想ネットワークの選択]** ページに、既存の仮想ネットワークがすべて表示されます。 **[基本]** ウィザード ページで選択したリソース グループと Azure の場所に属している仮想ネットワークのみが表示されます。
3. Azure AD Domain Services を有効にする仮想ネットワークを選択します。 既存の仮想ネットワークを選択することも、新しい仮想ネットワークを作成することもできます。

  > [!TIP]
  > **管理対象ドメインは、Azure AD Domain Services を有効にした後、別の仮想ネットワークに移動できません。** 管理対象ドメインを有効にするには、適切な仮想ネットワークを選択します。 管理対象ドメインを作成したら、その管理対象ドメインは、一度削除しないと別の仮想ネットワークには移動できません。 詳細については、「[Azure AD Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)」を参照してください。  
  >

4. **仮想ネットワークの作成:** **[新規作成]** をクリックして、新しい仮想ネットワークを作成します。 Azure AD Domain Services の専用サブネットを使用することを強くお勧めします。 たとえば、"DomainServices" という名前のサブネットを作成すると、サブネット内にデプロイされている内容を他の管理者が理解しやすくなります。 完了したら **[OK]**をクリックします。

    ![仮想ネットワークを選択する](./media/getting-started/domain-services-blade-network-pick-vnet.png)

  > [!WARNING]
  > 必ずプライベート IP アドレス空間の範囲内にあるアドレス空間を選んでください。 ご自身が所有していないパブリック アドレス空間内の IP アドレスを指定すると、Azure AD Domain Services 内でエラーが発生します。

5. **既存の仮想ネットワーク:** 既存の仮想ネットワークを選択することを計画している場合、[仮想ネットワークの拡張機能を使って専用サブネットを作成](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)し、そのサブネットを選択します。 **[仮想ネットワーク]** をクリックして、既存の仮想ネットワークを選択します。 **[サブネット]** をクリックして、新しい管理対象ドメインを有効にする、既存の仮想ネットワーク内の専用サブネットを選択します。 完了したら **[OK]**をクリックします。

    ![仮想ネットワーク内のサブネットを選択する](./media/getting-started/domain-services-blade-network-pick-subnet.png)

  > [!NOTE]
  > **サブネットを選択するためのガイドライン**
  > 1. Azure AD Domain Services の専用サブネットをご使用ください。 他の仮想マシンをこのサブネットにデプロイしないでください。 この構成を使用すると、管理対象ドメインに支障をきたすことなく、ワークロード/仮想マシンのネットワーク セキュリティ グループ (NSG) を構成できます。 詳細については、[Azure Active Directory Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)を参照してください。
  2. ゲートウェイ サブネットは、サポートされている構成ではないため、Azure AD Domain Services のデプロイでは選択しないでください。
  3. 選択したサブネットに、十分な空きアドレス空間が存在する (使用可能な IP アドレスが少なくとも 3 個から 5 個ある) こと、またそのサブネットがプライベート IP アドレス空間に存在することを確認します。
  >

6. 完了したら、**[OK]** をクリックして、ウィザードの **[管理者グループ]** ページに進みます。


## <a name="next-step"></a>次のステップ
[タスク 3: 管理グループを構成して Azure AD Azure AD Domain Services を有効にする](active-directory-ds-getting-started-admingroup.md)
