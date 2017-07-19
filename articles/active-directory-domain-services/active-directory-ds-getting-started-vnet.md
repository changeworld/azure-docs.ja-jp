---
title: "Azure Active Directory Domain Services: 仮想ネットワークの作成または選択 | Microsoft Docs"
description: "Azure クラシック ポータルを使って Azure Active Directory Domain Services を有効にする"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 457519b00b65b0157effe2d4aba033a1c99852e8
ms.contentlocale: ja-jp
ms.lasthandoff: 06/30/2017


---
<a id="create-or-select-a-virtual-network-for-azure-active-directory-domain-services" class="xliff"></a>

# Azure Active Directory Domain Services の仮想ネットワークの作成または選択
<a id="before-you-begin" class="xliff"></a>

## 開始する前に
[Azure Active Directory Domain Services のネットワークに関する考慮事項](active-directory-ds-networking.md)のページを参照してください。

<a id="task-2-create-an-azure-virtual-network" class="xliff"></a>

## タスク 2: Azure 仮想ネットワークを作成する
次の構成タスクでは、Azure 仮想ネットワークを作成し、その中にサブネットを作成します。 仮想ネットワーク内のこのサブネットで、Azure Active Directory Domain Services を有効にします。 希望する仮想ネットワークが既にある場合は、この手順を省略してかまいません。

> [!NOTE]
> Azure Active Directory Domain Services 用に作成 (または選択) する Azure 仮想ネットワークが、Azure Active Directory Domain Services でサポートされている Azure リージョンに属していることを確認してください。 Azure Active Directory Domain Services を利用できる Azure リージョンを確認するには、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/)に関するページを参照してください。
>
>仮想ネットワークの名前はメモしておいてください。この後の構成手順で Azure Active Directory Domain Services を有効にする際、適切な仮想ネットワークを選択するために必要になります。


Azure Active Directory Domain Services を有効にする Azure 仮想ネットワークを作成するには、以下の構成手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)に移動します。
2. 左側のウィンドウで、**[ネットワーク]** を選択します。

    ![[ネットワーク] ノード](./media/active-directory-domain-services-getting-started/networks-node.png)  
    **[Virtual Networks]** ウィンドウが開きます。
3. ウィンドウ下部にある作業ウィンドウで **[新規]** をクリックします。

    ![[Virtual Networks] ウィンドウ](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. **[ネットワーク サービス]** をクリックし、**[仮想ネットワーク]** を選択します。

    ![Virtual network - quick create](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. 仮想ネットワークを作成するには、**[簡易作成]** をクリックします。

6. 仮想ネットワークの **[名前]** を指定し、以下の点について検討します。
    * このネットワークの **[アドレス空間]** や **[最大 VM 数]** を構成することができます。
    * 現時点では、**[DNS サーバー]** 設定は **[なし]** のままでかまいません。 この設定は、Azure Active Directory Domain Services を有効にした後で更新できます。
7. **[場所]** ボックスの一覧で、サポートされている Azure リージョンを選択します。  
    Azure Active Directory Domain Services を利用できる Azure リージョンを確認するには、[リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services/)に関するページを参照してください。
8. 仮想ネットワークを作成するには、 **[仮想ネットワークの作成]** をクリックします。

    ![Azure Active Directory Domain Services の仮想ネットワークを作成する](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. 仮想ネットワークを作成したら、その仮想ネットワークの名前を選択し、**[構成]** タブをクリックします。

    ![サブネットの作成](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. **[仮想ネットワーク アドレス空間]** で、**[サブネットの追加]** をクリックし、**AaddsSubnet** という名前のサブネットを指定します。

    ![Azure Active Directory Domain Services のサブネットを作成する](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. サブネットを作成するには、**[保存]** をクリックします。


<a id="next-step" class="xliff"></a>

## 次のステップ
[タスク 3: Azure Active Directory Domain Services を有効にする](active-directory-ds-getting-started-enableaadds.md)

