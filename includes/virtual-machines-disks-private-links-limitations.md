---
title: ファイルを含める
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 293f0f459e1f1e464fdec16b76eaf08336c92e93
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377302"
---
- ディスク アクセス オブジェクトにリンクできる仮想ネットワークは 1 つだけです。
- これらをリンクするには、仮想ネットワークがディスク アクセス オブジェクトと同じサブスクリプションにある必要があります。
- 同じディスク アクセス オブジェクトを使用して、最大 10 個のディスクまたはスナップショットを同時にインポートまたはエクスポートできます。
- 仮想ネットワークをディスク アクセス オブジェクトにリンクするために手動承認を要求することはできません。
- ディスク アクセス オブジェクトに関連付けられている増分スナップショットはエクスポートできません。
- AzCopy を使用して、ストレージ アカウントへのプライベート リンクを介してセキュリティで保護されているディスクおよびスナップショットの VHD をダウンロードすることはできません。 ただし、AzCopy を使用して VHD を VM にダウンロードすることはできます。
