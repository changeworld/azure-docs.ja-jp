---
title: Azure Service Fabric Explorer を使用したクラスターの視覚化 | Microsoft Docs
description: Service Fabric Explorer は、Microsoft Azure Service Fabric クラスター内のクラウド アプリケーションとノードを検査および管理するためのアプリケーションです。
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.assetid: c875b993-b4eb-494b-94b5-e02f5eddbd6a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: mikhegn
ms.openlocfilehash: 459dd86fd614cb185801b074cea70c36dc7f6ccb
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972334"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Service Fabric Explorer を使用したクラスターの視覚化

Service Fabric Explorer (SFX) は、Azure Service Fabric クラスターを検査および管理するためのオープン ソース ツールです。 Service Fabric Explorer は、Windows、macOS、Linux 用のデスクトップ アプリケーションです。

## <a name="service-fabric-explorer-download"></a>Service Fabric Explorer のダウンロード

デスクトップ アプリケーションとして Service Fabric Explorer をダウンロードするには、次のリンクを使ってください。

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> Service Fabric Explorer のデスクトップ バージョンでは、クラスター サポートより機能が多い場合や少ない場合があります。 クラスターに展開される Service Fabric Explorer バージョンにフォールバックすることで、完全な機能の互換性が保証されます。
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>クラスターからの Service Fabric Explorer の実行

Service Fabric Explorer は、Service Fabric クラスターの HTTP 管理エンドポイントでもホストされています。 Web ブラウザーで SFX を起動するには、任意のブラウザーからクラスターの HTTP 管理エンドポイント (例: https://clusterFQDN:19080 ) を参照します。

開発者ワークステーションのセットアップでは、 https://localhost:19080/Explorer に移動することにより、ローカル クラスターで Service Fabric Explorer を起動できます。 詳しくは、「[開発環境を準備する](service-fabric-get-started.md)」をご覧ください。

## <a name="connect-to-a-service-fabric-cluster"></a>Service Fabric クラスターに接続する
Service Fabric クラスターに接続するには、クラスター管理エンドポイント (FQDN/IP) と HTTP 管理エンドポイント ポート (既定では 19080) が必要です。 たとえば、「 https://mysfcluster.westus.cloudapp.azure.com:19080 」のように指定します。 ワークステーションでローカル クラスターに接続するには、[ローカルホストに接続] チェック ボックスを使います。

### <a name="connect-to-a-secure-cluster"></a>セキュリティ保護されたクラスターに接続する
証明書または Azure Active Directory (AAD) を使用して、クライアントによる Service Fabric クラスターへのアクセスを制御できます。

セキュリティで保護されたクラスターに接続する場合は、クラスターの構成に応じて、クライアント証明書を提示するか、AAD を使ってログインする必要があります。

## <a name="video-tutorial"></a>ビデオ チュートリアル

Service Fabric Explorer の使用方法については、次の Microsoft Virtual Academy のビデオをご覧ください。

> [!NOTE]
> このビデオで示されている Service Fabric Explorer は、デスクトップ バージョンではなく、Service Fabric クラスターでホストされています。
>
>

[<center><img src="./media/service-fabric-visualizing-your-cluster/SfxVideo.png" WIDTH="360" HEIGHT="244"></center>](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=bBTFg46yC_9806218965)

## <a name="understand-the-service-fabric-explorer-layout"></a>Service Fabric Explorer のレイアウトについて
左側のツリーを使用して、Service Fabric Explorer 内を移動できます。 ツリーのルートでは、クラスター ダッシュボードにクラスターの概要 (アプリケーションとノードの正常性の概要など) が表示されます。

![Service Fabric Explorer のクラスター ダッシュボード][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>クラスターのレイアウトを表示する
Service Fabric クラスターのノードは、障害ドメインとアップグレード ドメインの 2 次元グリッドにわたって配置されます。 このように配置することで、ハードウェアの障害やアプリケーションのアップグレード時にもアプリケーションを継続して利用できます。 現在のクラスターの配置を表示するには、クラスター マップを使用します。

![Service Fabric Explorer のクラスター マップ][sfx-cluster-map]

### <a name="view-applications-and-services"></a>アプリケーションとサービスを表示する
クラスターには、2 つのサブツリーが含まれています。1 つはアプリケーション、もう 1 つはノードのサブツリーです。

アプリケーション ビューでは、Service Fabric の論理階層 (アプリケーション、サービス、パーティション、レプリカ) 内を移動できます。

次の例では、**MyApp** アプリケーションは、**MyStatefulService** と **WebService** の 2 つのサービスで構成されています。 **MyStatefulService** はステートフルであるため、1 つのプライマリ レプリカと 2 つのセカンダリ レプリカがあるパーティションが 1 つ含まれています。 これに対して、WebSvcService はステートレスであり、インスタンスが 1 つだけ含まれています。

![Service Fabric Explorer のアプリケーション ビュー][sfx-application-tree]

ツリーの各レベルでは、メイン ウィンドウにその項目の関連情報が表示されます。 たとえば、特定のサービスの正常性状態とバージョンを確認できます。

![Service Fabric Explorer のメイン ウィンドウ][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>クラスターのノードを表示する
ノード ビューには、クラスターの物理的なレイアウトが表示されます。 特定のノードについて、そのノードでコードがデプロイされているアプリケーション、 正確に言うと、そこで現在実行されているレプリカを調べることができます。

## <a name="actions"></a>アクション
Service Fabric Explorer では、クラスター内のノード、アプリケーション、サービスに対する操作を簡単に呼び出すことができます。

たとえば、アプリケーション インスタンスを削除するには、左側のツリーでアプリケーションを選択し、**[アクション]** > **[アプリケーションの削除]** の順に選択します。

![Service Fabric Explorer でのアプリケーションの削除][sfx-delete-application]

> [!TIP]
> 同じ操作を実行するには、各要素の横にある省略記号をクリックします。
>
> Service Fabric Explorer を使用して実行できる操作はすべて、PowerShell または REST API を使用して実行し、自動化することもできます。
>
>

Service Fabric Explorer を使用して、特定のアプリケーションの種類とバージョンのアプリケーション インスタンスを生成することもできます。 ツリー ビューでアプリケーションの種類を選択し、右側のウィンドウでバージョンの横にある **[Create app instance (アプリ インスタンスの作成)]** リンクをクリックします。

![Service Fabric Explorer でのアプリケーション インスタンスの作成][sfx-create-app-instance]

> [!NOTE]
> アプリケーション インスタンスを作成するとき、Service Fabric Explorer ではパラメーターはサポートされません。 アプリケーション インスタンスは、既定のパラメーター値を使います。
>
>

## <a name="next-steps"></a>次の手順
* [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)
* [PowerShell を使用した Service Fabric アプリケーションのデプロイメント](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/SfxClusterDashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/SfxClusterMap.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/SfxApplicationTree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/SfxServiceEssentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/SfxDeleteApplication.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/SfxCreateAppInstance.png
