---
title: Azure VMware Solutions (AVS) プライベート クラウドの拡張
description: 既存の AVS プライベート クラウドを拡張して、既存または新規のクラスターに容量を追加する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025301"
---
# <a name="expand-an-avs-private-cloud"></a>AVS プライベート クラウドの拡張

AVS は、AVS プライベート クラウドを動的に拡張する柔軟性を提供します。 小規模な構成から出発し、より大きな容量が必要になったら拡張することができます。 または、現在のニーズに基づいて AVS プライベート クラウドを作成し、利用の増加に合わせて拡張することができます。

AVS プライベート クラウドは、1 つ以上の vSphere クラスターで構成されます。 各クラスターには、3 から 16 個のノードを含めることができます。 AVS プライベート クラウドを拡張する場合、既存のクラスターにノードを追加するか、新しいクラスターを作成します。 既存のクラスターを拡張するには、追加するノードが既存のノードと同じ種類 (SKU) である必要があります。 新しいクラスターを作成する場合は、ノードの種類が違っていても構いません。 AVS プライベート クラウドの制限の詳細については、記事「[AVS プライベート クラウドの概要](cloudsimple-private-cloud.md)」の「制限」セクションを参照してください。

AVS プライベート クラウドは、vCenter 上の既定の**データセンター**を使用して作成されます。 各データセンターは、最上位レベルの管理エンティティとして機能します。 新しいクラスターの場合、AVS では、既存のデータセンターに追加するか、新しいデータセンターを作成するかを選択できます。

新規クラスターの構成の一環として、AVS は VMware インフラストラクチャを構成します。 設定には、vSAN ディスク グループ用のストレージ設定、VMware High Availability、および Distributed Resource Scheduler (DRS) が含まれます。

AVS プライベート クラウドは複数回拡張できます。 拡張は、全体的なノードの制限を超えない場合にのみ実行できます。 AVS プライベート クラウドを拡張するたびに、既存のクラスターに追加するか、または新しいクラスターを作成します。

## <a name="before-you-begin"></a>開始する前に

AVS プライベート クラウドを拡張する前に、ノードをプロビジョニングする必要があります。 ノードのプロビジョニングの詳細については、[Azure での VMware Solution by AVS 用ノードのプロビジョニング](create-nodes.md)に関する記事を参照してください。 新しいクラスターを作成するには、同じ SKU の使用可能なノードが少なくとも 3 つ必要です。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="expand-an-avs-private-cloud"></a>AVS プライベート クラウドの拡張

1. [AVS ポータルにアクセスします](access-cloudsimple-portal.md)。

2. **[リソース]** ページを開き、拡張する AVS プライベート クラウドを選択します。

3. 概要セクションで、 **[Expand]\(拡張\)** をクリックします。

    ![AVS プライベート クラウドの拡張](media/resources-expand-private-cloud.png)

4. 既存のクラスターを拡張するか、新しい vSphere クラスターを作成するか選択します。 変更を加えると、ページ上の要約情報が更新されます。

    * 既存のクラスターを拡張するには、 **[Expand existing cluster]\(既存のクラスターの拡張\)** を選択します。 拡張するクラスターを選択し、追加するノードの数を入力します。 各クラスターは、最大 16 個のノードを持つことができます。
    * 新しいクラスターを追加するには、 **[新しいクラスターの作成]** をクリックします。 クラスターの名前を入力します。 既存のデータセンターを選択するか、名前を入力して新しいデータセンターを作成します。 ノードの種類を選択します。 新しい vSphere クラスターを作成するときは異なるノードの種類を選択できますが、既存の vSphere クラスターを拡張するときは選択できません。 ノードの数を選択します。 新しいクラスターのそれぞれに、少なくとも 3 つのノードが必要です。

    ![AVS プライベート クラウドの拡張 - ノードの追加](media/resources-expand-private-cloud-add-nodes.png)

5. **[送信]** をクリックして、AVS プライベート クラウドを拡張します。

## <a name="next-steps"></a>次のステップ

* [Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)
* [AVS プライベート クラウド](cloudsimple-private-cloud.md)の詳細を確認する