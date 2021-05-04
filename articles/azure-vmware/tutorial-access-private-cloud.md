---
title: チュートリアル - プライベート クラウドにアクセスする
description: Azure VMware Solution プライベート クラウドにアクセスする方法を学習する
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: d2046acaf483022f977559fe74254ca58b8c42ea
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107945649"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>チュートリアル:Azure VMware Solution プライベート クラウドにアクセスする

Azure VMware Solution では、オンプレミスの vCenter でプライベート クラウドを管理することはできません。 ジャンプ ボックスを通して Azure VMware Solution vCenter インスタンスに接続する必要があります。 

このチュートリアルでは、[前のチュートリアル](tutorial-configure-networking.md)で作成したリソース グループにジャンプ ボックスを作成し、Azure VMware Solution vCenter にサインインします。 このジャンプ ボックスは、作成したのと同じ仮想ネットワーク上にある Windows 仮想マシン (VM) です。  これにより、vCenter と NSX Manager の両方へのアクセスが提供されます。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure VMware Solution vCenter へアクセスするための Windows 仮想マシンを作成する
> * この仮想マシンから vCenter にサインインする

## <a name="create-a-new-windows-virtual-machine"></a>新しい Windows 仮想マシンを作成する

1. リソース グループで、 **[+ 追加]** を選択します。次に、**Microsoft Windows 10** を検索して選択し、 **[作成]** を選択します。

   :::image type="content" source="media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="ジャンプ ボックス用に新しい Windows 10 VM を追加する方法を示すスクリーンショット。" border="true":::

1. フィールドに必要な情報を入力し、 **[確認および作成]** を選択します。 

   フィールドの詳細については、次の表を参照してください。

   | フィールド | 値 |
   | --- | --- |
   | **[サブスクリプション]** | 値には、リソース グループが属しているサブスクリプションが事前に設定されています。 |
   | **リソース グループ** | 値には、前のチュートリアルで作成した現在のリソース グループが事前に設定されています。  |
   | **仮想マシン名** | VM の一意の名前を入力します。 |
   | **リージョン** | VM の地理的な場所を選択します。 |
   | **可用性オプション** | 既定値を選択したままにします。 |
   | **Image** | VM イメージを選択します。 |
   | **[サイズ]** | 既定のサイズ値をそのまま使用します。 |
   | **認証の種類**  | **[パスワード]** を選択します。 |
   | **ユーザー名** | VM にログオンするためのユーザー名を入力します。 |
   | **パスワード** | VM にログオンするためのパスワードを入力します。 |
   | **[パスワードの確認入力]** | VM にログオンするためのパスワードを入力します。 |
   | **パブリック インバウンド ポート** | **[なし]** を選択します。 [なし] を選択した場合は、[JIT アクセス](../security-center/security-center-just-in-time.md#jit-configure)を使用して、VM にアクセスする必要があるときにのみ VM へのアクセスを制御できます。 あるいは、ネットワーク ポートを公開せずにインターネットから安全にジャンプ ボックス サーバーにアクセスするようにする場合は、[Azure Bastion](../bastion/tutorial-create-host-portal.md) を使用することもできます。  |


1. 検証に合格したら、 **[作成]** を選択して仮想マシンの作成プロセスを開始します。

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>プライベート クラウドのローカル vCenter に接続する

1. クラウド管理者のユーザー名を使用して、ジャンプ ボックスから VMware vCenter SSO によって vSphere クライアントにサインインし、ユーザー インターフェイスが正常に表示されることを確認します。

1. Azure portal で、プライベート クラウドを選択し、 **[管理]**  >  **[ID]** を選択します。 

   プライベート クラウドの vCenter および NSX-T Manager の URL とユーザーの資格情報が表示されます。

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="プライベート クラウドの vCenter および NSX Manager の URL と資格情報を表示する。" border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. 前の手順で作成した VM に移動し、仮想マシンに接続します。 

   VM への接続について助けが必要な場合は、[仮想マシンへの接続](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)に関するページで詳細を参照してください。

1. Windows VM でブラウザーを開き、2 つのタブを使用して vCenter と NSX-T Manger の URL に移動します。 

1. vCenter タブで、前の手順の `cloudadmin@vmcp.local` ユーザー資格情報を入力します。

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="プライベート クラウドの vCenter にサインインする。" border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter ポータル。" border="true":::

1. ブラウザーの 2 番目のタブで、NSX-T Manager にサインインします。

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="ブラウザーの 2 番目のタブで、NSX-T Manager にサインインします。" border="true":::



## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * vCenter への接続に使用する Windows 仮想マシンを作成する
> * 仮想マシンから vCenter にログインする

次のチュートリアルに進み、プライベート クラウド クラスターのローカル管理を設定するために仮想ネットワークを作成する方法を学習します。

> [!div class="nextstepaction"]
> [仮想ネットワークの作成](tutorial-configure-networking.md)


