---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/15/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4435784592954e4df44d5ea8dc05b33a0023fd43
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108313414"
---
- Ultra Disk はサポートされません。
- VM または仮想マシン スケール セットで Azure Disk Encryption (bitlocker/VM-Decrypt を使用するゲスト VM の暗号化) が有効になっている場合、有効にすることはできません。
- ホストでの暗号化が有効になっているディスクで Azure Disk Encryption を有効にすることはできません。
- 既存の仮想マシン スケール セットで暗号化を有効にすることができます。 しかし、暗号化を有効にした後に作成された新しい VM のみが自動的に暗号化されます。
- 暗号化するには、既存の VM の割り当てを解除して再割り当てする必要があります。
- エフェメラル OS ディスクをサポートしますが、プラットフォーム マネージド キーを使用した場合のみです。