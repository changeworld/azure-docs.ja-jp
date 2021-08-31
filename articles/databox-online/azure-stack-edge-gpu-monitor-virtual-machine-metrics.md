---
title: Azure Stack Edge Pro GPU デバイス上の VM の CPU、メモリを監視する
description: Azure portal 内で Azure Stack Edge Pro GPU デバイス上の VM の CPU、メモリのメトリックを監視する方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: c58b7fd1cf4ab08dd8d645e4fc5f537d9ee937e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779558"
---
# <a name="monitor-vm-metrics-for-cpu-memory-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 上の CPU、メモリに関する VM メトリックを監視する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイス上の仮想マシンの CPU とメモリのメトリックを監視する方法について説明します。

## <a name="about-vm-metrics"></a>VM メトリックについて

仮想マシンの **[メトリック]** タブでは、CPU とメモリのメトリックを表示し、期間を調整して、目的の期間にズームインできます。

VM メトリックは、VM のゲスト オペレーティング システムから収集された CPU およびメモリの使用状況データに基づいています。 リソースの使用量は、1 分ごとに 1 回サンプリングされます。

デバイスが接続されていない場合、メトリックはデバイスにキャッシュされます。 デバイスが再接続されると、メトリックがキャッシュからプッシュされ、VM **メトリック** が更新されます。

## <a name="monitor-cpu-and-memory-metrics"></a>CPU とメモリのメトリックを監視する

1. Azure portal 内でデバイスを開き、 **[仮想マシン]** に移動します。 仮想マシンを選択し、 **[メトリック]** を選択します。

    ![Azure Stack Edge デバイス上の仮想マシンの [メトリック] タブを表示しているスクリーンショット。 [メトリック] タブが強調表示されています。](media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-01.png)

2. 既定では、グラフには、過去 1 時間の CPU とメモリの平均使用率が表示されます。 別の期間のデータを表示するには、 **[次に指定する直近の期間のデータを表示する]** の横にある別のオプションを選択します。

    ![Azure Stack Edge デバイス上の仮想マシンの [メトリック] タブのスクリーンショット。 [次に指定する直近の期間のデータを表示する] オプションと選択した値が強調表示されています。](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-02.png)

3. いずれかのグラフ内の任意の場所をマウスでポイントすると手のマークが付いた縦線が表示され、これを左または右に移動して前または後のデータ サンプルを表示できます。 クリックすると、その期間の詳細ビューが開きます。

    ![仮想マシンの [メトリック] タブを示すスクリーンショット。 グラフの領域をポイントすると表示されるポインターが強調表示されています。](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-03.png)


## <a name="next-steps"></a>次のステップ

- [デバイス上の VM アクティビティを監視します](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)。
- [サポート パッケージで VM ゲスト ログを収集します](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)。
