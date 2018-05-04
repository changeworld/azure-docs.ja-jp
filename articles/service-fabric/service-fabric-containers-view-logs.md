---
title: Azure Service Fabric でコンテナー ログを表示する | Microsoft Docs
description: Service Fabric Explorer を使用して実行中の Service Fabric コンテナー サービスのコンテナー ログを表示する方法について説明します。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/09/2018
ms.author: ryanwi
ms.openlocfilehash: 48ee54460454368deef44c8f84624e32856efafa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Service Fabric コンテナー サービスのログを表示する
Azure Service Fabric はコンテナー オーケストレーターであり、[Linux コンテナーと Windows コンテナー](service-fabric-containers-overview.md)の両方をサポートします。  この記事では、問題を診断してトラブルシューティングできるように、実行中のコンテナー サービスのコンテナー ログを表示する方法について説明します。

## <a name="access-container-logs"></a>コンテナー ログにアクセスする
コンテナー ログには、[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) を使用してアクセスできます。  Web ブラウザーで、[http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer) に移動することによってクラスターの管理エンドポイントから Service Fabric Explorer を開きます。  

コンテナー ログは、コンテナー サービス インスタンスが実行しているクラスター ノードにあります。 例として、[Linux Voting サンプル アプリケーション](service-fabric-quickstart-containers-linux.md)の Web フロントエンド コンテナーのログを取得します。 ツリー ビューで、**[クラスター]**>**[アプリケーション]**>**[VotingType]**>**[fabric:/Voting/azurevotefront]** の順に展開します。  次に、パーティション (この例では d1aa737e-f22a-e347-be16-eec90be24bc1) を展開し、コンテナーがクラスター ノード *_lnxvm_0* 上で実行していることを確認します。

ツリー ビューで、**[ノード]**>**[_lnxvm_0]**>**[fabric:/Voting]**>**[azurevotfrontPkg]**>**[コード パッケージ]**>**[コード]** の順に展開し、*_lnxvm_0* ノード上でコード パッケージを探します。  その後、**[コンテナー ログ]** オプションを選択してコンテナー ログを表示します。

![Service Fabric platform][Image1]


## <a name="next-steps"></a>次の手順
- [Linux コンテナー アプリケーションの作成チュートリアル](service-fabric-tutorial-create-container-images.md)を行う。
- [Service Fabric とコンテナー](service-fabric-containers-overview.md)についてさらに詳しく学習する

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
