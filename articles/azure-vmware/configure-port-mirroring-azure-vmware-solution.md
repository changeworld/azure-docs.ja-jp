---
title: Azure VMware Solution 用にポート ミラーリングを構成する
description: 各パケットのコピーのあるネットワーク スイッチ ポートから別のネットワーク スイッチ ポートへの転送を含むネットワーク トラフィックを監視するためにポート ミラーリングを構成する方法について説明します。
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: 3f1b205cc75911132abeed83d1e52e3566919551
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322735"
---
# <a name="configure-port-mirroring-in-the-azure-portal"></a>Azure portal でポート ミラーリングを構成する

Azure VMware Solution を配置したら、Azure portal からポート ミラーリングを構成できます。 ポート ミラーリングでは、ミラー化されたデータを受信するポートにプロトコル アナライザーが配置されます。 これにより、ソース、仮想マシン (VM)、または VM のグループからのトラフィックが分析された後、定義された宛先に送信されます。 

このハウツー記事では、ネットワーク トラフィックを監視するためにポート ミラーリングを構成します。その際、各パケットのコピーをあるネットワーク スイッチ ポートから別のネットワーク スイッチ ポートに転送することが必要になります。 

## <a name="prerequisites"></a>前提条件

vCenter と NSX-T Manager のインターフェイスへのアクセスが可能な Azure VMware Solution プライベート クラウド。 詳細については、[ネットワークの構成](tutorial-configure-networking.md)に関するチュートリアルを参照してください。

## <a name="create-the-vms-or-vm-groups"></a>VM または VM グループを作成する

ソースおよび宛先 VM または VM グループを作成します。 ソース グループには、トラフィックがミラー化される 1 つの VM または複数の VM が含まれます。

1. Azure VMware Solution プライベート クラウドで、 **[ワークロード ネットワーク]** の下にある **[ポート ミラーリング]**  >  **[VM グループ]**  >  **[追加]** を選択します。

   :::image type="content" source="media/networking/add-port-mirroring-vm-groups.png" alt-text="ポート ミラーリング用の VM グループを作成する方法を示すスクリーンショット。":::

1. 新しい VM グループの名前を指定し、一覧から VM を選択してから **[OK]** を選択します。

1. これらの手順を繰り返して、宛先 VM グループを作成します。

   >[!NOTE]
   >ポート ミラーリング プロファイルを作成する前に、ソースと宛先の両方の VM グループが作成されていることを確認してください。

## <a name="create-a-port-mirroring-profile"></a>ポート ミラーリング プロファイルを作成する

ソースおよび宛先 VM グループのトラフィックの方向を定義するポート ミラーリング プロファイルを作成します。

1. **[ポート ミラーリング]**  >  **[ポート ミラーリング]**  >  **[追加]** の順に選択し、次の情報を指定します。

   :::image type="content" source="media/networking/add-port-mirroring-profile.png" alt-text="ポート ミラーリング プロファイルに必要な情報を示すスクリーンショット。":::

   - **[ポート ミラーリング名]** - プロファイルのわかりやすい名前。

   - **[方向]** - [イングレス]、[エグレス]、または [双方向] から選択します。

   - **[ソース]** - ソース VM グループを選択します。

   - **[宛先]** - 宛先 VM グループを選択します。

   - **[説明]** - ポート ミラーリングの説明を入力します。

1. **[OK]** を選択してプロファイルを完了します。 

   プロファイルと VM グループは、Azure VMware Solution コンソールに表示されます。
