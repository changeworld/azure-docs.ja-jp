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
ms.openlocfilehash: c6f83c1590057b8b46b19f3100947bd838666792
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022321"
---
- 増分スナップショットは、現時点ではサブスクリプション間で移動できません。
- 現時点では、任意の時点で特定のスナップショット ファミリの最大 5 つのスナップショットの SAS URI のみを生成することができます。
- 特定のディスクの増分スナップショットを、そのディスクのサブスクリプションの外部で作成することはできません。
- ディスクあたり最大 7 つの増分スナップショットを 5 分ごとに作成できます。
- 1 つのディスクに対して合計 200 の増分スナップショットを作成できます。
- 4 TB の境界をまたいで親ディスクのサイズを変更する際、変更の前と後に作成されたスナップショットの間の変更を取得することはできません。 たとえば、ディスクのサイズが 2 TB の場合に増分スナップショット `snapshot-a` を作成したとします。 これで、ディスクのサイズが 6 TB に増加し、別の増分スナップショット `snapshot-b` が作成されました。 `snapshot-a` と `snapshot-b` 間の変更を取得することはできません。 サイズ変更後に作成された `snapshot-b` の完全なコピーを再度ダウンロードする必要があります。 その後、`snapshot-b` と `snapshot-b` の後に作成されたスナップショット間の変更を取得できます。
