---
title: Azure Service Fabric Mesh アプリケーションの監視と診断 | Microsoft Docs
description: Azure 上の Service Fabric Mesh にあるアプリケーションの監視および診断について説明します。
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 9f857c23b5500bc7790a0ff7fcf81eaec51f37c9
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358311"
---
# <a name="monitoring-and-diagnostics"></a>監視と診断
Azure Service Fabric Mesh は、仮想マシン、ストレージ、ネットワークを管理することなく開発者がマイクロサービス アプリケーションをデプロイできるフル マネージド サービスです。 Service Fabric Mesh の監視と診断は、主に次の 3 種類の診断データに分類されます。

- アプリケーション ログ - これらは、アプリケーションをインストルメント化した方法に基づいて、コンテナー化されたアプリケーションからのログと定義されます (例: Docker のログ)。
- プラットフォーム イベント - 現時点では、コンテナーのアクティブ化、非アクティブ化、および終了などのコンテナーの操作に関連するメッシュ プラットフォームからのイベント。
- コンテナーのメトリック - コンテナーのリソース使用率とパフォーマンス メトリック (docker の統計情報)。

この記事では、使用可能な最新のプレビュー バージョンの監視と診断のオプションについて説明します。

## <a name="application-logs"></a>アプリケーション ログ

デプロイされたコンテナーから、あるいはコンテナーごとに、Docker ログを表示できます。 Service Fabric Mesh アプリケーション モデルでは、各コンテナーは、アプリケーション内のコード パッケージです。 コード パッケージに関連付けられているログを表示するには、次のコマンドを使用します。

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> "az mesh service-replica" コマンドを使用して、レプリカ名を取得できます。 レプリカ名は、0 から増分する数字です。*

この動作が、投票アプリケーションの VotingWeb.Code コンテナーからのログにどのように記録されるかを次に示します。

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="next-steps"></a>次の手順
Service Fabric Mesh の詳細については、以下の概要ページを参照してください。
- [Service Fabric Mesh の概要](service-fabric-mesh-overview.md)
