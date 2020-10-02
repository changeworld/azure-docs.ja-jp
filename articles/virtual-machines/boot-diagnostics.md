---
title: Azure のブート診断
description: Azure のブート診断とマネージ ブート診断の概要
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 0b3e1b3bc296676c44eddf34b35a0d4e06d3b8c4
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007334"
---
# <a name="azure-boot-diagnostics"></a>Azure のブート診断

ブート診断は、VM ブート エラーの診断を可能にする、Azure の仮想マシン (VM) のデバッグ機能です。 ブート診断を使用すると、ユーザーは、シリアル ログ情報とスクリーンショットを収集して、起動中の VM の状態を確認できます。

## <a name="boot-diagnostics-storage-account"></a>ブート診断ストレージ アカウント
Azure portal で VM を作成するとき、ブート診断は既定で有効になっています。 推奨されるブート診断エクスペリエンスは、マネージド ストレージ アカウントを使用することです。これにより、Azure VM の作成にかかる時間の面で、パフォーマンスが大幅に向上します。 これは、Azure が管理するストレージ アカウントが使用されることで、ブート診断データの保存用に新しいユーザー ストレージ アカウントを作成するのにかかる時間が不要になるためです。

別のブート診断エクスペリエンスでは、ユーザーが管理するストレージ アカウントを使用します。 ユーザーは、新しいストレージ アカウントを作成することも、既存のものを使用することもできます。

> [!IMPORTANT]
> Azure のお客様には、2020 年 10 月までは、マネージド ストレージ アカウントを使用したブート診断に関連するストレージ コストが請求されることはありません。

## <a name="boot-diagnostics-view"></a>ブート診断のビュー
仮想マシンのブレードにある [ブート診断] オプションは、Azure portal の *[サポートとトラブルシューティング]* セクションにあります。 [ブート診断] を選択すると、スクリーンショットとシリアル ログ情報が表示されます。 シリアル ログには、カーネル メッセージングが含まれています。スクリーンショットは、VM の現在の状態のスナップショットです。 VM で Windows と Linux のどちらが実行されているかによって、どのような外観のスクリーンショットが予想されるかが決まります。 Windows の場合、ユーザーにはデスクトップの背景が表示され、Linux の場合はログイン プロンプトが表示されます。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux のブート診断のスクリーンショット":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Linux のブート診断のスクリーンショット":::


## <a name="limitations"></a>制限事項
- ブート診断は、Azure Resource Manager VM でのみ使用できます。 
- ブート診断は、Premium Storage アカウントをサポートしていません。ブート診断に Premium Storage アカウントを使用すると、ユーザーは VM の起動時に `StorageAccountTypeNotSupported` エラーを受け取ります。 
- マネージド ストレージ アカウントは、Resource Manager API バージョン "2020-06-01" 以降でサポートされています。
- Azure シリアル コンソールは現在、ブート診断用のマネージド ストレージ アカウントと互換性がありません。 [Azure シリアル コンソール](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)についてさらに詳しく学習します。

## <a name="next-steps"></a>次の手順

[Azure シリアル コンソール](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)の詳細、およびブート診断を使用して [Azure の仮想マシンでトラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)を行う方法について確認します。
