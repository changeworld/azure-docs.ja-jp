---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/19/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3267f649e360c512a5523ce1d5948719a1969934
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
## <a name="deployment-considerations"></a>デプロイに関する考慮事項

* N シリーズ VM を利用できるリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。

* N シリーズ VM をデプロイするには、Resource Manager デプロイメント モデルを使用する必要があります。

* N シリーズ VM は、それぞれのディスクに対してサポートされる Azure Storage の種類が異なります。 NC および NV の VM では、Standard Disk Storage (HDD) で提供される VM ディスクのみがサポートされます。 NCv2、ND、および NCv3 の VM では、Premium Disk Storage (SSD) で提供される VM ディスクのみがサポートされます。

* 多数の N シリーズ VM をデプロイする場合は、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

* Azure サブスクリプションの (リージョンごとの) コア クォータと NC、NCv2、NCv3、ND、または NV のコアの個別のクォータを増やす必要が生じる場合があります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](../articles/azure-supportability/how-to-create-azure-support-request.md) (無料) してください。 既定の制限は、サブスクリプション カテゴリによって異なる場合があります。




