---
title: Azure VMware Solutions (AVS) のクイックスタート - サービスの作成
description: AVS サービスの作成方法、ノードの購入方法、ノードの予約方法について説明します
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024434"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>クイックスタート - Azure VMware Solutions (AVS) の作成

開始するには、Azure portal で Azure VMware Solutions (AVS) を作成します。

## <a name="vmware-solutions-avs---service-overview"></a>VMware Solutions (AVS) - サービスの概要

Azure VMware Solution by AVS は、AVS サービスを通じて利用できます。 このサービスを作成すると、ノードをプロビジョニングしたり、ノードを予約したり、AVS プライベート クラウドを作成したりすることができます。 AVS サービスを利用できる各 Azure リージョンに、AVS サービスを追加します。 このサービスでは、Azure VMware Solution by AVS のエッジ ネットワークが定義されます。 このエッジ ネットワークは、VPN、ExpressRoute、AVS プライベート クラウドへのインターネット接続を含むサービスに使用されます。

AVS サービスを追加するには、ゲートウェイ サブネットを作成する必要があります。 ゲートウェイ サブネットはエッジ ネットワークを作成するときに使用し、/28 CIDR ブロックを必要とします。 ゲートウェイ サブネットのアドレス空間は一意である必要があります。 これは、オンプレミス ネットワークのアドレス空間や Azure の仮想ネットワーク アドレス空間と重複できません。

## <a name="before-you-begin"></a>開始する前に

ゲートウェイ サブネットに /28 CIDR ブロックを割り当てます。 AVS サービスごとに、その作成先のリージョンに固有のゲートウェイ サブネットが必要となります。 ゲートウェイ サブネットは、Azure VMware Solution by AVS エッジ ネットワーク サービスで使用され、/28 CIDR ブロックが必要です。 ゲートウェイ サブネットのアドレス空間は一意である必要があります。 AVS 環境と通信するネットワークと重複しないようにしてください。 AVS と通信するネットワークには、オンプレミスのネットワークや Azure Virtual Networks があります。

[ネットワークの前提条件](cloudsimple-network-checklist.md)を確認してください。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="create-the-service"></a>サービスの作成

1. **[すべてのサービス]** を選択します。
2. **AVS サービス**を検索します。

    ![AVS サービスの検索](media/create-cloudsimple-service-search.png)

3. **[AVS Services]\(AVS サービス\)** を選択します。
4. 新しいサービスを作成するには、**Add** (追加) をクリックします。

    ![AVS サービスの追加](media/create-cloudsimple-service-add.png)

5. AVS サービスを作成するサブスクリプションを選択します。
6. サービスのリソース グループを選択します。 リソース グループを新規に追加するには、 **[Create New]** (新規作成) をクリックします。
7. サービスを識別する名前を入力します。
8. サービス ゲートウェイの CIDR を入力します。 オンプレミス サブネット、Azure サブネット、計画された AVS サブネットのいずれとも重複しない /28 サブネットを指定します。 サービスを作成した後は、CIDR を変更できません。

    ![AVS サービスの作成](media/create-cloudsimple-service.png)

9. **[OK]** をクリックします。

サービスが作成され、サービスの一覧に追加されます。

## <a name="provision-nodes"></a>ノードをプロビジョニングする

AVS プライベート クラウド環境に従量課金制の容量を設定するには、まず、Azure portal 内のノードをプロビジョニングします。

1. **[すべてのサービス]** を選択します。
2. **AVS ノード**を検索します。

    ![AVS ノードの検索](media/create-cloudsimple-node-search.png)

3. **[AVS Nodes]\(AVS ノード\)** を選択します。
4. **[追加]** をクリックして、ノードを作成します。

    ![AVS ノードの追加](media/create-cloudsimple-node-add.png)

5. AVS ノードをプロビジョニングするサブスクリプションを選択します。
6. ノードのリソース グループを選択します。 リソース グループを新規に追加するには、 **[Create New]** (新規作成) をクリックします。
7. ノードを識別するためにプレフィックスを入力します。
8. ノード リソースの場所を選択します。
9. ノード リソースをホスティングする専用の場所を選択します。
10. [ノードの種類](cloudsimple-node.md)を選択します。
11. プロビジョニングするノードの数を選択します。
12. **[確認および作成]** を選択します。
13. 設定を確認します。 設定を変更素ウルには、 **[前へ]** をクリックします。
14. **作成** を選択します。

## <a name="next-steps"></a>次のステップ

* [AVS プライベート クラウドを作成して環境を構成する](quickstart-create-private-cloud.md)
* [AVS サービス](https://docs.azure.cloudsimple.com/cloudsimple-service)について詳しく確認する
