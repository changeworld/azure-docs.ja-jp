---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420944"
---
この記事では、ローカル コンピューターから Azure マネージド ディスクに VHD をアップロードする方法や、AzCopy を使用してマネージド ディスクを別のリージョンにコピーする方法について説明します。 このプロセス (直接アップロード) を使用すると、VHD を最大 32 TiB のサイズで直接マネージド ディスクにアップロードすることもできます。 現在、直接アップロードは、Standard HDD、Standard SSD、および Premium SSD マネージド ディスクでサポートされています。 Ultra ディスクでは、まだサポートされていません。

Azure の IaaS VM 向けにバックアップ ソリューションを提供している場合は、直接アップロードを使用して顧客のバックアップをマネージド ディスクに復元することをお勧めします。 Azure の外部のソースから VHD をアップロードする場合、速度はご利用のローカル帯域幅によって異なります。 Azure VM からアップロードまたはコピーを行う場合、帯域幅は標準の HDD と同じになります。