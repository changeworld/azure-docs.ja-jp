---
title: Azure のブート診断
description: Azure のブート診断とマネージ ブート診断の概要
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 52a2b5e27cd5857416343e559237d08ea9a591be
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972392"
---
# <a name="azure-boot-diagnostics"></a>Azure のブート診断

ブート診断は、VM ブート エラーの診断を可能にする、Azure の仮想マシン (VM) のデバッグ機能です。 ブート診断を使用すると、ユーザーは、シリアル ログ情報とスクリーンショットを収集して、起動中の VM の状態を確認できます。

## <a name="boot-diagnostics-storage-account"></a>ブート診断ストレージ アカウント
Azure portal で VM を作成するとき、ブート診断は既定で有効になっています。 推奨されるブート診断エクスペリエンスは、マネージド ストレージ アカウントを使用することです。これにより、Azure VM の作成にかかる時間の面で、パフォーマンスが大幅に向上します。 これは、Azure が管理するストレージ アカウントが使用されることで、ブート診断データの保存用に新しいユーザー ストレージ アカウントを作成するのにかかる時間が不要になるためです。

別のブート診断エクスペリエンスでは、ユーザーが管理するストレージ アカウントを使用します。 ユーザーは、新しいストレージ アカウントを作成することも、既存のものを使用することもできます。

> [!IMPORTANT]
> Azure のお客様には、2020 年 10 月までは、マネージド ストレージ アカウントを使用したブート診断に関連するストレージ コストが請求されることはありません。
>
> ブート診断データの BLOB (ログとスナップショット イメージで構成されます) は、マネージド ストレージ アカウントに格納されます。 お客様は、ディスクのプロビジョニング サイズではなく、BLOB で使用された Gib に対してのみ課金されます。 スナップショット メーターは、マネージド ストレージ アカウントの課金に使用されます。 管理対象のアカウントは Standard LRS と Standard ZRS のどちらかで作成されるため、お客様の診断データ BLOB のサイズに対して 1 か月あたり $ 0.05/GB のみが請求されます。 価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」を参照してください。 お客様には、VM リソース URI に関連付けられているこの料金が表示されます。 

## <a name="boot-diagnostics-view"></a>ブート診断のビュー
仮想マシンのブレードにある [ブート診断] オプションは、Azure portal の *[サポートとトラブルシューティング]* セクションにあります。 [ブート診断] を選択すると、スクリーンショットとシリアル ログ情報が表示されます。 シリアル ログには、カーネル メッセージングが含まれています。スクリーンショットは、VM の現在の状態のスナップショットです。 VM で Windows と Linux のどちらが実行されているかによって、どのような外観のスクリーンショットが予想されるかが決まります。 Windows の場合、ユーザーにはデスクトップの背景が表示され、Linux の場合はログイン プロンプトが表示されます。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux のブート診断のスクリーンショット":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Linux のブート診断のスクリーンショット":::


## <a name="limitations"></a>制限事項
- ブート診断は、Azure Resource Manager VM でのみ使用できます。 
- ブート診断は、Premium Storage アカウントをサポートしていません。ブート診断に Premium Storage アカウントを使用すると、ユーザーは VM の起動時に `StorageAccountTypeNotSupported` エラーを受け取ります。 
- マネージド ストレージ アカウントは、Resource Manager API バージョン "2020-06-01" 以降でサポートされています。
- Azure シリアル コンソールは現在、ブート診断用のマネージド ストレージ アカウントと互換性がありません。 [Azure シリアル コンソール](./troubleshooting/serial-console-overview.md)についてさらに詳しく学習します。
- マネージド ストレージ アカウントを利用したブート診断は現在、Azure portal からのみ適用できます。 

## <a name="next-steps"></a>次の手順

[Azure シリアル コンソール](./troubleshooting/serial-console-overview.md)の詳細、およびブート診断を使用して [Azure の仮想マシンでトラブルシューティング](./troubleshooting/boot-diagnostics.md)を行う方法について確認します。