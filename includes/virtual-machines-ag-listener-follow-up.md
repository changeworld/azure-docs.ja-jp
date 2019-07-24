---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8ae22ec7f75b9cd0d7958977dfd97169706c9389
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181122"
---
可用性グループ リスナーを作成すると、リスナー リソース用に RegisterAllProvidersIP と HostRecordTTL のクラスター パラメーターを調整する必要がある場合があります。 これらのパラメーターは、フェールオーバー後の再接続時間を短縮して、接続のタイムアウトを防ぐことができます。 これらのパラメーターに関する詳細とサンプル コードについては、「[可用性グループ リスナーの作成または構成](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)」を参照してください。

