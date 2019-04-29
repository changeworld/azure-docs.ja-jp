---
title: Azure Service Fabric でサポートされているクラスターのバージョン | Microsoft Docs
description: Azure Service Fabric でのクラスターのバージョンについて説明します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: 75e95737eecb9407a80103d1cad00d4987fe7091
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998823"
---
# <a name="supported-service-fabric-versions"></a>サポートされている Service Fabric のバージョン

クラスターでは常に、サポートされている Azure Service Fabric のバージョンが実行されている状態にします。 Service Fabric の新しいバージョンのリリースが発表された日から最短で 60 日後には、以前のバージョンのサポートが終了します。 新しいリリースについては、[Service Fabric チーム ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)上でお知らせします。

サポートされている Service Fabric のバージョンをクラスターにおいて継続して実行する方法の詳細については、次のドキュメントを参照してください。

- [Azure Service Fabric クラスターのアップグレード](service-fabric-cluster-upgrade.md)
- [スタンドアロン Windows Server クラスター上で実行される Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>サポートされているバージョン

次の表に、Service Fabric のバージョンとサポート終了日の一覧を示します。

| クラスター内の Service Fabric ランタイム | クラスター バージョンから直接アップグレードできる |互換性のある SDK または NuGet パッケージのバージョン | サポート終了 |
| --- | --- |--- | --- |
| 5.3.121 より前のすべてのクラスター バージョン | 5.1.158.* |バージョン 2.3 以前 |2017 年 1 月 20 日 |
| 5.3.* | 5.1.158.* |バージョン 2.3 以前 |2017 年 2 月 24 日 |
| 5.4.* | 5.1.158.* |バージョン 2.4 以前 |2017 年 5 月 10 日       |
| 5.5.* | 5.4.164.* |バージョン 2.5 以前 |2017 年 8 月 10 日    |
| 5.6.* | 5.4.164.* |バージョン 2.6 以前 |2017 年 10 月 13 日   |
| 5.7.* | 5.4.164.* |バージョン 2.7 以前 |2017 年 12 月 15 日  |
| 6.0.* | 5.6.205.* |バージョン 2.8 以前 |2018 年 3 月 30 日     |
| 6.1.* | 5.7.221.* |バージョン 3.0 以前 |2018 年 7 月 15 日      |
| 6.2.* | 6.0.232.* |バージョン 3.1 以前 |2018 年 10 月 26 日   |
| 6.3.* | 6.1.480.* |バージョン 3.2 以前 |2019 年 3 月 31 日  |
| 6.4.* | 6.2.301.* |バージョン 3.3 以前 |最新バージョンのため、終了日なし |

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

次の表に、サポートされている Service Fabric のバージョンに対応しているサポート対象のオペレーティング システムを示します。

| オペレーティング システム | サポートされる最も古い Service Fabric のバージョン |
| --- | --- |
| Windows Server 2012 R2 | すべてのバージョン |
| Windows Server 2016 | すべてのバージョン |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4. |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |

