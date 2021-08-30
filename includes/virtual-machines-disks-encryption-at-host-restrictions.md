---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/22/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 528b3243a7c7d8ca1f96d9b4a311393820e19685
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738998"
---
- Ultra Disks はサポートされません。
- VM または仮想マシン スケール セットで Azure Disk Encryption (bitlocker/DM-Crypt を使用するゲスト VM の暗号化) が有効になっている場合、有効にすることはできません。
- ホストでの暗号化が有効になっているディスクで Azure Disk Encryption を有効にすることはできません。
- 既存の仮想マシン スケール セットで暗号化を有効にすることができます。 しかし、暗号化を有効にした後に作成された新しい VM のみが自動的に暗号化されます。
- 暗号化するには、既存の VM の割り当てを解除して再割り当てする必要があります。
- エフェメラル OS ディスクをサポートしますが、プラットフォーム マネージド キーを使用した場合のみです。
