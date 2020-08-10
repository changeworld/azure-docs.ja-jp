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
ms.openlocfilehash: be014a257a68a266a5946bf5822b094c5a968f6e
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535594"
---
- ディスク アクセス オブジェクトにリンクできる仮想ネットワークは 1 つだけです。
- これらをリンクするには、仮想ネットワークがディスク アクセス オブジェクトと同じサブスクリプションにある必要があります。
- 同じディスク アクセス オブジェクトを使用して、最大 10 個のディスクまたはスナップショットを同時にインポートまたはエクスポートできます。
- 仮想ネットワークをディスク アクセス オブジェクトにリンクするために手動承認を要求することはできません。
- ディスク アクセス オブジェクトに関連付けられている増分スナップショットでは、差分機能はサポートされていません。