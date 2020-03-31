---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8e4c1c05b133f85b7473c3ce97c9f509ac8f7d37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299180"
---
- 増分スナップショットは、現時点ではサブスクリプション間で移動できません。
- 現時点では、任意の時点で特定のスナップショット ファミリの最大 5 つのスナップショットの SAS URI のみを生成することができます。
- 特定のディスクの増分スナップショットを、そのディスクのサブスクリプションの外部で作成することはできません。
- ディスクあたり最大 7 つの増分スナップショットを 5 分ごとに作成できます。
- 1 つのディスクに対して合計 200 の増分スナップショットを作成できます。