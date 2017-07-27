---
title: "Service Fabric プログラミング モデルの概要 | Microsoft Docs"
description: "Service Fabric では、アクター フレームワークとサービス フレームワークという、サービスを構築するための 2 つのフレームワークが提供されています。 それぞれ、シンプル性とコントロールという、両極端の特性を持っています。"
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 102ede8d2aafaf485a5212faad47de6781d84578
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---
# <a name="service-fabric-programming-model-overview"></a>Service Fabric プログラミング モデルの概要
Service Fabric には、サービスの記述と管理に使用できる複数の方法が用意されています。 サービスでは、Service Fabric API を使用するように選択して、プラットフォームの機能とアプリケーション フレームワークを最大限に活用できます。 サービスはまた、Service Fabric クラスターでホストされているだけのコンテナーで実行される任意の言語またはコードで記述されたコンパイル済み実行可能プログラムにすることもできます。

### <a name="guest-executables"></a>ゲスト実行可能ファイル
[ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)は、Service Fabric クラスターで他のサービスとともにホストされる既存の任意の実行可能ファイル (任意の言語で記述されたもの) です。 ゲスト実行可能ファイルは、Service Fabric API に直接統合されません。 ただし、カスタムの正常性と負荷のレポート、REST API を呼び出すことによるサービスの検出可能性など、プラットフォームに備わった機能からメリットを得られます。 また、完全なアプリケーションのライフサイクルのサポートも備えています。

ゲスト実行可能ファイルから始める場合は、最初の [ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)をデプロイしてください。

### <a name="containers"></a>コンテナー
既定では、Service Fabric はサービスをプロセスとしてデプロイし、アクティブ化します。 また、Service Fabric では[コンテナー](service-fabric-containers-overview.md)内のサービスもデプロイできます。 Service Fabric では、Linux コンテナーのデプロイと、Windows Server 2016 での Windows コンテナーのデプロイをサポートしています。 既存のアプリケーション、ステートレス サービス、またはステートフル サービスをコンテナーにデプロイでき、プロセスとしてのサービスとコンテナー内のサービスを同じアプリケーション内で混在させることができます。

## <a name="reliable-services"></a>Reliable Service
Reliable Services は、Service Fabric プラットフォームと統合されたサービスを作成できる軽量のフレームワークです。プラットフォームの全機能を活用できます。 Reliable Services には、Service Fabric ランタイムがサービスのライフサイクルを管理し、サービスがランタイムとやり取りすることができる最小限の API のセットが用意されています。 アプリケーション フレームワークは最小限で、設計と実装の選択を細かく制御できるので、ASP.NET Core など、他のアプリケーション フレームワークのホストに使用できます。

Reliable Services は、Web サーバーなどのほとんどのサービス プラットフォームと同様にステートレス (サービスの各インスタンスは同様に作成され、状態は Azure DB や Azure Table Storage など、外部のソリューションに維持される) にすることができます。

Reliable Services は Service Fabric と排他的でステートフルの場合もあります。Service Fabric は Reliable Collection を使用してサービスに直接維持されます。 状態はレプリケーションによって高可用になり、パーティションによって分散されます。いずれも Service Fabric で自動的に管理されます。

[Reliable Services の詳細](service-fabric-reliable-services-introduction.md)、または[最初の Reliable Services を作成する](service-fabric-reliable-services-quick-start.md)場合の概要を参照してください。

## <a name="reliable-actors"></a>Reliable Actor
Reliable Actors フレームワークは Reliable Services 上に構築され、アクター設計パターンに基づいて、Virtual Actor パターンを実装するアプリケーション フレームワークです。 Reliable Actors フレームワークは、独立したコンピューティングのユニットと、アクターという単一スレッドの実行を含む状態を使用します。 Reliable Actors フレームワークには、アクターとプリセットされた状態の永続性とスケールアウト構成に対応する組み込みの通信が用意されています。

Reliable Actors 自体が Reliable Services 上に構築されたアプリケーション フレームワークなので、Service Fabric プラットフォームと完全に統合され、プラットフォームに用意されているすべての機能を利用できます。

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric は、Web アプリケーションと API アプリケーションを構築するために [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) と連携します。 

## <a name="next-steps"></a>次のステップ
[Reliable Actors の詳細](service-fabric-reliable-actors-introduction.md)と[最初の Reliable Actors サービスを作成する](service-fabric-reliable-actors-get-started.md)
場合の概要を参照してください。[Windows または Linux でのサービスのコンテナー化の詳細](service-fabric-deploy-container.md)
や [ASP.NET Core を使用したフロント エンド サービスの作成](service-fabric-add-a-web-frontend.md)の詳細を参照してください。



