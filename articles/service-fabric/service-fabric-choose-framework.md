<properties
   pageTitle="Service Fabric プログラミング モデルの概要 | Microsoft Azure"
   description="Service Fabric では、アクター フレームワークとサービス フレームワークという、サービスを構築するための 2 つのフレームワークが提供されています。それぞれ、シンプル性とコントロールという、両極端の特性を持っています。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/18/2016"
   ms.author="seanmck"/>

# Service Fabric プログラミング モデルの概要

Service Fabric には、サービスの記述と管理に使用できる複数の方法が用意されています。サービスは、Service Fabric API を使用して、プラットフォームの機能とアプリケーション フレームワークをフルに活用することができます。また、サービスは、任意の言語で記述され、Service Fabric クラスターでホストされているだけのコンパイル済み実行可能プログラムにすることもできます。

## ゲスト実行可能ファイル

ゲスト実行可能ファイルは、任意の言語で記述された任意の実行可能ファイルです。そのため、既存のアプリケーションを利用し、Service Fabric クラスターでホストすることができます。ゲスト実行可能ファイルはアプリケーション内にパッケージ化し、他のサービスと共にホストすることができます。Service Fabric は、実行可能ファイルのオーケストレーションと簡易な実行管理を処理し、サービスの説明に従って稼働および実行する必要があります。一方、ゲスト実行ファイルは Service Fabric API と直接統合されていないため、プラットフォームに用意されているフルセットの機能 (カスタムの正常性、負荷のレポート、サービス エンドポイントの登録、ステートフル コンピューティングなど) を利用できません。

ゲスト実行可能ファイルから始める場合は、最初の[ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)をデプロイしてください。

## Reliable Service

Reliable Services は、Service Fabric プラットフォームと統合されたサービスを作成できる軽量のフレームワークです。プラットフォームの全機能を活用できます。Reliable Services には、Service Fabric ランタイムがサービスのライフサイクルを管理し、サービスがランタイムとやり取りすることができる最小限の API のセットが用意されています。アプリケーション フレームワークは最小限で、設計と実装の選択を細かく制御できるので、ASP.NET MVC、Web API など、他のアプリケーション フレームワークのホストに使用できます。

Web サーバー、Azure Cloud Services の worker ロールなど、ほとんどのサービス プラットフォームと同様に、Reliable Services はステートレスな場合があります。サービスの各インスタンスは同様に作成され、状態は Azure DB や Azure Table Storage など、外部のソリューションに維持されます。

Reliable Services は Service Fabric と排他的でステートフルの場合もあります。Service Fabric は Reliable Collection を使用してサービスに直接維持されます。状態はレプリケーションによって高可用になり、パーティションによって分散されます。いずれも Service Fabric で自動的に管理されます。

[Reliable Services の詳細](service-fabric-reliable-services-introduction.md)、または[最初の Reliable Services を作成する](service-fabric-reliable-services-quick-start.md)場合の概要を参照してください。

## Reliable Actor

Reliable Actors フレームワークは Reliable Services 上に構築され、アクター設計パターンに基づいて、Virtual Actor パターンを実装するアプリケーション フレームワークです。Reliable Actors フレームワークは、独立したコンピューティングのユニットと、アクターという単一スレッドの実行を含む状態を使用します。Reliable Actors フレームワークには、アクターとプリセットされた状態の永続性とスケールアウト構成に対応する組み込みの通信が用意されています。

Reliable Actors 自体が Reliable Services 上に構築されたアプリケーション フレームワークなので、Service Fabric プラットフォームと完全に統合され、プラットフォームに用意されているすべての機能を利用できます。

## 次のステップ
[Reliable Actors の詳細](service-fabric-reliable-actors-introduction.md)と[最初の Reliable Actors サービスを作成する](service-fabric-reliable-actors-get-started.md)場合の概要を参照してください。

<!---HONumber=AcomDC_0720_2016-->