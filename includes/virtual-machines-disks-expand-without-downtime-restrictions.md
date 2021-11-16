---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5813ec99666e7e758dd1d58a639211d3d0ca0c94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448950"
---
- 現在米国中西部でのみご利用いただけます。
- データ ディスクでのみサポートされます。
- 4 TiB 未満のディスクは、ダウンタイムなしでは 4 TiB 以上に拡張できません。
    - ディスクのサイズを 4 TiB 以上に増やすと、ダウンタイムなしで拡張できます。
- 次のいずれかをインストールして使います。
    - [最新の Azure CLI](/cli/azure/install-azure-cli)
    - [最新の Azure PowerShell モジュール](/powershell/azure/install-az-ps)
    - [https://aka.ms/iaasexp/DiskLiveResize](https://aka.ms/iaasexp/DiskLiveResize) によってアクセスされた場合は Azure portal
    - または、2021-04-01 以降の API バージョンを含む Azure Resource Manager テンプレート。