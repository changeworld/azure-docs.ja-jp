---
title: Azure VMware Solution で VM をデプロイするためのコンテンツ ライブラリを作成する
description: コンテンツ ライブラリを作成して、Azure VMware Solution プライベート クラウドに VM をデプロイします。
ms.topic: how-to
ms.date: 06/28/2021
ms.openlocfilehash: ed98d48037df6cfb50c3c94bb6a7187097f5b0e9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323690"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Azure VMware Solution で VM をデプロイするためのコンテンツ ライブラリを作成する

コンテンツ ライブラリは、ライブラリ項目の形式でコンテンツを格納および管理します。 1 つのライブラリ項目は、仮想マシン (VM) をデプロイするために使用するファイルで構成されます。

この記事では、vSphere クライアントでコンテンツ ライブラリを作成した後、コンテンツ ライブラリにある ISO イメージを使用して VM をデプロイします。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、NSX-T セグメントとマネージド DHCP サービスが必要です。  詳細については、「[Azure VMware Solution の DHCP を構成する](configure-dhcp-azure-vmware-solution.md)」を参照してください。  

## <a name="create-a-content-library"></a>コンテンツ ライブラリを作成する

1. オンプレミスの vSphere クライアントから、 **[Menu]\(メニュー\)**  >  **[Content Libraries]\(コンテンツ ライブラリ\)** を選択します。

   :::image type="content" source="media/content-library/vsphere-menu-content-libraries.png" alt-text="vSphere クライアントの [Content Libraries]\(コンテンツ ライブラリ\) メニュー オプションを示すスクリーンショット。":::

1. **[Add]\(追加\)** を選択して、新しいコンテンツ ライブラリを作成します。

   :::image type="content" source="media/content-library/create-new-content-library.png" alt-text="vSphere で新しいコンテンツ ライブラリを作成する方法を示すスクリーンショット。":::

1. 名前を入力し、vCenter Server の IP アドレスを確認して、 **[Next]\(次へ\)** を選択します。

   :::image type="content" source="media/content-library/new-content-library-step-1.png" alt-text="新しいコンテンツ ライブラリの名前と vCenter Server の IP を示すスクリーンショット。":::

1. **[ローカル コンテンツ ライブラリ]** を選択し、 **[次へ]** を選択します。

   :::image type="content" source="media/content-library/new-content-library-step-2.png" alt-text="新しいコンテンツ ライブラリ用に選択された [Local content library]\(ローカル コンテンツ ライブラリ\) オプションを示すスクリーンショット。":::

1. コンテンツ ライブラリを格納するデータストアを選択し、 **[次へ]** を選択します。

   :::image type="content" source="media/content-library/new-content-library-step-3.png" alt-text="選択された保存場所 vsanDatastore を示すスクリーンショット。":::

1. コンテンツ ライブラリの設定を確認し、 **[完了]** を選択します。

   :::image type="content" source="media/content-library/new-content-library-step-4.png" alt-text="新しいコンテンツ ライブラリの設定を示すスクリーンショット。":::

## <a name="upload-an-iso-image-to-the-content-library"></a>ISO イメージをコンテンツ ライブラリにアップロードする

コンテンツ ライブラリを作成したので、ISO イメージを追加して、プライベート クラウド クラスターに VM をデプロイできます。 

1. vSphere クライアントから、 **[Menu]\(メニュー\)**  >  **[Content Libraries]\(コンテンツ ライブラリ\)** を選択します。

1. 新しい ISO に使用するコンテンツ ライブラリを右クリックし、 **[Import Item] (インポート項目)** を選択します。

1. 次のいずれかを実行してソースのライブラリ項目をインポートし、 **[インポート]** を選択します。
   1. [URL] を選択し、ISO をダウンロードするための URL を指定します。

   1. **[ローカル ファイル]** を選択して、ローカル システムからアップロードします。

   > [!TIP]
   > 必要に応じて、宛先のカスタム項目名とメモを定義できます。

1. ライブラリを開き、 **[Other Types] (他の種類)** タブを選択して、ISO が正常にアップロードされたことを確認します。


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>プライベート クラウド クラスターに VM をデプロイする

1. vSphere クライアントから、 **[Menu]\(メニュー\)**  >  **[Hosts and Clusters]\(ホストとクラスター\)** を選択します。

1. 左側のパネルで、ツリーを展開してクラスターを選択します。

1. **[Actions]\(アクション\)**  >  **[New Virtual Machine]\(新しい仮想マシン\)** を選択します。

1. ウィザードを実行して、必要な設定を変更します。

1. **[New CD/DVD Drive]\(新しい CD または DVD ドライブ\)**  >  **[Client Device]\(クライアント デバイス\)**  >  **[Content Library ISO File]\(コンテンツ ライブラリ ISO ファイル\)** を選択します。

1. 前のセクションでアップロードした ISO を選択してから、 **[OK]** を選択します。

1. **[接続]** チェック ボックスをオンにして、ISO が電源オン時にマウントされるようにします。

1. **[New Network]\(新しいネットワーク\)**  >  **[Select dropdown]\(ドロップダウンの選択\)**  >  **[Browse]\(参照\)** を選択します。

1. **[logical switch (segment)] (論理スイッチ (セグメント))** を選択し、 **[OK]** を選択します。

1. その他のハードウェア設定を変更し、 **[次へ]** を選択します。

1. 設定を確認し、 **[完了]** を選択します。


## <a name="next-steps"></a>次のステップ

Azure VMware Solution で VM をデプロイするためのコンテンツ ライブラリを作成したので、次のことについて学習することをお勧めします。

- [VM ワークロードのプライベート クラウドへの移行](configure-vmware-hcx.md)
- [Azure VMware Solution での Azure ネイティブ サービスの統合](integrate-azure-native-services.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
