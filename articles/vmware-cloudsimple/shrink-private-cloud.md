---
title: Azure VMware Solutions (AVS) プライベート クラウドの縮小
description: AVS プライベート クラウドを縮小する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014268"
---
# <a name="shrink-an-avs-private-cloud"></a>AVS プライベート クラウドの縮小

AVS には、AVS プライベート クラウドを動的に縮小できる柔軟性があります。 AVS プライベート クラウドは、1 つまたは複数の vSphere クラスターで構成されます。 各クラスターには、3 から 16 個のノードを含めることができます。 AVS プライベート クラウドを縮小する場合、既存のクラスターからノードを削除するか、クラスター全体を削除します。 

## <a name="before-you-begin"></a>開始する前に

AVS プライベート クラウドを縮小するには、事前に次の条件を満たしておく必要があります。 AVS プライベート クラウドを作成すると、管理クラスター (最初のクラスター) が作成されます。 これを削除することはできません。

* vSphere クラスターには 3 つのノードが必要です。 ノードが 3 つしかないクラスターは縮小できません。
* 使用されているストレージの合計が、クラスターの縮小後の合計容量を超えないようにする必要があります。
* Distributed Resource Scheduler (DRS) ルールによって仮想マシンの vMotion が禁止されているかどうかを確認します。 ルールが存在する場合は、そのルールを無効にするか削除します。 DRS ルールには、アフィニティ ルールをホストする仮想マシンが含まれます。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="shrinking-an-avs-private-cloud"></a>AVS プライベート クラウドの縮小

1. [AVS ポータルへのアクセス](access-cloudsimple-portal.md)

2. **[リソース]** ページを開きます。

3. 縮小する AVS プライベート クラウドをクリックします。

4. 概要ページで **[縮小]** をクリックします。

    ![AVS プライベート クラウドを縮小する](media/shrink-private-cloud.png)

5. 縮小または削除するクラスターを選択します。 

    ![AVS プライベート クラウドを縮小する - クラスターの選択](media/shrink-private-cloud-select-cluster.png)

6. **[Remove one node]\(1 つのノードを削除する\)** または **[Delete the whole cluster]\(クラスター全体を削除する\)** を選択します。 

7. クラスターの容量を確認します。

8. **[送信]** をクリックして、AVS プライベート クラウドを縮小します。

AVS プライベート クラウドの縮小が開始されます。 タスクの進行状況を監視できます。 vSAN で再同期する必要があるデータによっては、縮小プロセスに数時間かかることがあります。

> [!NOTE]
> 1. データセンター内の最後または唯一のクラスターを削除してプライベート クラウドを縮小した場合、データセンターは削除されません。
> 2. DRS ルール違反が発生した場合、ノードはクラスターから削除されず、ノードの削除がクラスター上の DRS ルールに違反することがタスクの説明に示されます。    


## <a name="next-steps"></a>次のステップ

* [Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)
* [AVS プライベート クラウド](cloudsimple-private-cloud.md)の詳細を確認する
