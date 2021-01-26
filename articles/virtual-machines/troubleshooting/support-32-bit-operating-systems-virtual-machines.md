---
title: Azure 仮想マシンにおける 32 ビット オペレーティング システムのサポート | Microsoft Docs
description: Azure 仮想マシンでサポートされているオペレーティング システムに関する情報
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 81b7efdd6bca0471719c11d130be95405f4d54e1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210190"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>Azure 仮想マシンでの 32 ビット オペレーティング システムのサポート

Microsoft Azure をご利用のユーザーは、32 ビットの Windows オペレーティング システムを Azure で利用できるようになりました。 特殊化された VHD のみがサポートされ、一般化されたイメージは Azure では機能しません。 これらのオペレーティング システムの一部は既にサポート有効期間が終了しているため、Microsoft では追加サポートを提供しない場合があります。 Microsoft Azure 仮想マシン (VM) 上で実行される Linux ベースまたは Berkeley ソフトウェア配布 (BSD) ベースのオペレーティング システムに対しても、サポートは提供されません。

> [!NOTE]
> Azure プラットフォームでは、32 ビット オペレーティング システムが実行されている VM に対して、メモリ アドレス空間の制限が設けられています。この場合、VM では 1 GB のメモリしか使用できないことがあり ("*特に、Win7 や Win10 などのクライアント SKU において*")、VM の残りのメモリは、ゲスト VM 内で予約済みとして表示されます。 これは既知の問題であり、現時点では修正に関する ETA はありません。 64 ビット版の OS に移行することをお勧めします。
> 

## <a name="more-information"></a>詳細情報

Azure 仮想マシンでサポートされているオペレーティング システムの詳細については、次の Microsoft サポート技術情報の記事を参照してください。

* [Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)
* [Azure での Linux とオープン ソース テクノロジのサポート](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>References

* [Azure の Windows Server 2008/R2のセキュリティ更新プログラムを無料で延長する方法に関する詳細情報](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [Azure の Windows Server 2008 SP2 32 ビットに特化したイメージのサポートに関する詳細情報](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [Azure Site Recovery を使用した Azure への Windows Server 2008 イメージの移行サポートに関する詳細情報](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [Azure の拡張機能がサポートされるオペレーティング システムに関する詳細情報](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [Microsoft Azure で Windows Server 2003 を実行する方法に関する詳細情報](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>次のステップ

この記事についてさらにヘルプが必要な場合は、[MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートにお問い合わせください。

または、Azure サポート インシデントを送信してください。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
