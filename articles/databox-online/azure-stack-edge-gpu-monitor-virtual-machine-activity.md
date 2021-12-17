---
title: Azure Stack Edge Pro GPU デバイスで VM のアクティビティを監視する
description: Azure portal で Azure Stack Edge Pro GPU デバイスの VM アクティビティを監視する方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: aa1974347caa04c314e51b33daba47d7835026db
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757612"
---
# <a name="monitor-vm-activity-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスで VM のアクティビティを監視する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイス上の仮想マシンのアクティビティ ログを Azure portal で表示する方法を説明します。

> [!NOTE]
> 仮想マシンの **[メトリック]** タブでは、アクティビティの期間中の CPU やメモリの使用量を拡大して見ることができます。 詳細については、[VM メトリックの監視](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)に関する記事をご覧ください。

## <a name="view-activity-logs"></a>アクティビティ ログの表示

Azure Stack Edge Pro GPU デバイス上の仮想マシンのアクティビティ ログを表示するには、以下の手順を行います。

1. デバイスにアクセスして、 **[Virtual Machines]** に移動します。 **[アクティビティ ログ]** を選択します。

    ![Azure Stack Edge デバイス上の仮想マシンの [アクティビティ ログ] ペインを示すスクリーンショット。 ナビゲーション ウィンドウでアクティビティ ログが強調表示されています。](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-01.png)

    デバイス上の仮想マシンの VM ゲスト ログが表示されていることが確認できます。

1. 一覧の上にあるフィルターを使用して、表示する必要があるアクティビティを対象にします。

    ![Virtual Machines の [アクティビティ ログ] ページの [期間] オプションを示すスクリーンショット。 選択した期間が強調表示されています。](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-02.png)<!--Reshoot to remove pointer. Lightbox treatment?-->

1. 操作名の下矢印をクリックすると、関連するアクティビティが表示されます。

    ![Azure Stack Edge デバイス上の仮想マシンの [アクティビティ ログ] ページを示すスクリーンショット。 展開された操作が一覧で強調表示されています。](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-03.png)<!--Reshoot to remove pointer. May be able to replace drop-down only.-->

Azure の **[アクティビティ ログ]** ペインでは、アクティビティのフィルタリングと並べ替え、表示する列の選択、特定のアクティビティの詳細へのドリルダウンが可能で、過去 24 時間に発生したエラー、デプロイの失敗、アラート、サービスの正常性、セキュリティの変更についての **[クイック分析情報]** を得ることができます。 ログとフィルタリングのオプションの詳細については、[アクティビティ ログの表示](../azure-monitor/essentials/activity-log.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [VM のデプロイに関するトラブルシューティング](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)。
- [サポート パッケージで VM ゲスト ログを収集する](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)。