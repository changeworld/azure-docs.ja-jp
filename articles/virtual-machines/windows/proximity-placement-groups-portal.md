---
title: ポータルを使用して、近接通信配置グループを作成します。
description: Azure portal を使用して、近接通信配置グループを作成する方法について説明します。
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180023"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>ポータルを使用して、近接通信配置グループを作成します。

各 VM をできるだけ近くに配置して、可能性のある最も短い待ち時間を実現するには、それらを[近接通信配置グループ](co-location.md#proximity-placement-groups)内にデプロイするようにしてください。

近接通信配置グループは、Azure コンピューティング リソースが互いに物理的に近くに配置されるようにするために使用される論理的なグループ化です。 近接通信配置グループは、短い待ち時間が要件であるワークロードに役立ちます。


## <a name="create-the-proximity-placement-group"></a>近接通信配置グループを作成する

1. 検索に、「**近接通信配置グループ**」と入力します。
1. 検索結果の **[サービス]** の下で、 **[近接通信配置グループ]** を選択します。
1. **[近接通信配置グループ]** ページで、 **[追加]** を選択します。
1. **[基本]** タブの **[プロジェクトの詳細]** で、正しいサブスクリプションが選択されていることを確認します。
1. **[リソース グループ]** で、 **[新規作成]** を選択して新しいグループを作成するか、またはドロップダウンから既存のリソース グループを選択します。
1. **[リージョン]** で、近接通信配置グループを作成する場所を選択します。
1. **[Proximity placement group name]\(近接通信配置グループの名前\)** に、名前を入力して **[Review + create]\(確認および作成\)** を選択します。
1. 検証に合格したら、 **[作成]** を選択して近接通信配置グループを作成します。

    ![近接通信配置グループの作成のスクリーンショット](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>VM の作成

1. ポータル上で VM を作成しているときに、 **[詳細]** タブにアクセスします。 
1. **[近接通信配置グループ]** の選択で、適切な配置グループを選択します。 

    ![ポータル上で新しいVM を作成する際の、[近接通信配置グループ] セクションのスクリーンショット](./media/ppg/vm-ppg.png)

1. その他の必要な選択をすべて完了したら、 **[Review + create]** \(確認および作成\) を選択します。
1. 検証に合格したら、 **[作成]** を選択して配置グループに VM をデプロイします。




## <a name="next-steps"></a>次のステップ

[Azure PowerShell](proximity-placement-groups.md) を使用して近接通信配置グループを作成することもできます。

