---
title: チュートリアル - プライベート クラウドにアクセスする
description: Azure VMware Solution プライベート クラウドにアクセスする方法を学習する
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: b7f21dbba5256712607241bb1258962fc22418fb
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578422"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>チュートリアル:Azure VMware Solution プライベート クラウドにアクセスする

Azure VMware Solution では、オンプレミスの vCenter でプライベート クラウドを管理することはできません。 ジャンプ ボックスを通してローカル vCenter インスタンスへの追加の設定と接続を行う必要があります。 

このチュートリアルでは、[前のチュートリアル](tutorial-configure-networking.md)で作成したリソース グループにジャンプ ボックスを作成し、vCenter にサインインします。 ジャンプ ボックスは、作成したのと同じ仮想ネットワーク上にある Windows 仮想マシン (VM) です。  これにより、vCenter と NSX Manager へのアクセスを提供されます。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * vCenter への接続に使用する Windows 仮想マシンを作成する
> * 仮想マシンから vCenter にログインする

## <a name="create-a-new-windows-virtual-machine"></a>新しい Windows 仮想マシンを作成する

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>プライベート クラウドのローカル vCenter に接続する

1. クラウド管理者のユーザー名を使用して、ジャンプ ボックスから VMware vCenter SSO によって vSphere クライアントにサインインし、ユーザー インターフェイスが正常に表示されることを確認します。

1. Azure portal で、お使いのプライベート クラウドを選択し、 **[概要]** ビューで **[ID]> [既定値]** を選択します。 

   プライベート クラウドの vCenter および NSX-T Manager の URL とユーザーの資格情報が表示されます。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報を表示する。" border="true":::

1. 前の手順で作成した VM に移動し、仮想マシンに接続します。 

   VM への接続について助けが必要な場合は、[仮想マシンへの接続](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)に関するページで詳細を参照してください。

1. Windows VM でブラウザーを開き、2 つのタブを使用して vCenter と NSX-T Manger の URL に移動します。 

1. vCenter タブで、前の手順の `cloudadmin@vmcp.local` ユーザー資格情報を入力します。

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報を表示する。" border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報を表示する。" border="true":::

1. ブラウザーの 2 番目のタブで、NSX-T Manager にサインインします。

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報を表示する。" border="true":::



## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * vCenter への接続に使用する Windows 仮想マシンを作成する
> * 仮想マシンから vCenter にログインする

次のチュートリアルに進み、プライベート クラウド クラスターのローカル管理を設定するために仮想ネットワークを作成する方法を学習します。

> [!div class="nextstepaction"]
> [仮想ネットワークの作成](tutorial-configure-networking.md)


