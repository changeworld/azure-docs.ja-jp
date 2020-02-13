---
title: Azure VMware Solutions (AVS) - AVS サービスの作成
description: Azure portal で AVS サービスを作成する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024825"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Azure VMware Solutions (AVS) サービスの作成

Azure VMware Solutions (AVS) を開始するには、Azure portal で Azure VMware Solutions (AVS) サービスを作成します。

## <a name="before-you-begin"></a>開始する前に

ゲートウェイ サブネットに /28 CIDR ブロックを割り当てます。 AVS サービスごとに、その作成先のリージョンに固有のゲートウェイ サブネットが必要となります。 ゲートウェイ サブネットは、エッジ ネットワーク サービスを作成するときに使用され、/28 CIDR ブロックを必要とします。 ゲートウェイ サブネットのアドレス空間は一意である必要があります。 AVS 環境と通信するネットワークと重複しないようにしてください。 AVS と通信するネットワークには、オンプレミスのネットワークや Azure Virtual Networks があります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

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

## <a name="next-steps"></a>次のステップ

* [ノードをプロビジョニングする](create-nodes.md)方法を学習する
* [AVS プライベート クラウドを作成する](create-private-cloud.md)方法を確認する
* [AVS プライベート クラウド環境を構成する](quickstart-create-private-cloud.md)方法を確認する
