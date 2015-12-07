<properties
   pageTitle="Service Fabric プロジェクトの作成の次の手順 | Microsoft Azure"
   description="この記事には、Service Fabric の中心的な開発タスク セットへのリンクが含まれています"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/16/2015"
   ms.author="seanmck"/>

# Service Fabric アプリケーションと次の手順
Service Fabric アプリケーションが作成されました。この記事では、プロジェクトの構成と潜在的な次の手順について説明します。

## アプリケーション
すべての新しいアプリケーションには、アプリケーション プロジェクトが含まれています。選択したサービスのタイプに応じて、さらに 1 つか 2 つのプロジェクトがある場合があります。

### アプリケーション プロジェクト
アプリケーション プロジェクトは次で構成されます。

- アプリケーションを構成するサービスへの一連の参照。

- クラスター エンドポイントおよび既定でのアップグレード デプロイメントの実行の可否などの基本設定を維持するために使用する異なる環境の操作用の (ローカルとクラウドの) 2 つの発行プロファイル。

- サービス用に作成するパーティション数などの環境に固有のアプリケーション構成を維持するために使用する (ローカルとクラウドの) 2 つのアプリケーション パラメーター ファイル。

- コマンドラインまたは自動化された継続的インテグレーション パイプラインの一環としての、アプリケーションを展開するために使用するデプロイメント スクリプト。

- アプリケーションを説明するアプリケーション マニフェスト。

### Reliable Service
新しい Reliable Service を追加すると、Visual Studio は、ソリューションにサービス プロジェクトを追加します。サービス プロジェクトには、選択したタイプに応じて `StatelessService` または `StatefulService` から拡張するクラスが含まれています。

### Reliable Actor
新しい Reliable Actor を追加すると、Visual Studio はソリューションに次の 2 つのプロジェクトを追加します。アクター プロジェクトとインターフェイス プロジェクトです。

アクター プロジェクトは、アクターのタイプと状態 (ステートフル アクターの場合) を定義します。インターフェイス プロジェクトは、その他のサービスでアクターを呼び出すために使用できるインターフェイスを提供します。

アクターは他のサービスによってアクティブ化される必要があるため、アクター プロジェクトには既定のスタートアップ動作が含まれていないことに注意してください。アクターを作成し、対話するためには、Reliable Service または ASP.NET プロジェクトの追加を検討してください。

### ASP.NET 5
Service Fabric アプリケーションで使用するために提供される ASP.NET 5 テンプレートは、個別に作成される ASP.NET 5 プロジェクトで使用するものとほぼ同じです。違いは次の点のみです。

- プロジェクトに、Data および Config パッケージと共に ServiceManifest を格納する **PackageRoot** フォルダーが含まれます。

- プロジェクトは、DNX と Service Fabric 間のブリッジとして機能する追加の NuGet パッケージ (Microsoft.ServiceFabric.AspNet.Hosting) を参照しています。

## 次のステップ
### アプリケーションへの Web フロント エンドの追加
Service Fabric は、アプリケーションへの Web ベースのエントリ ポイントを作成するための ASP.NET 5 との統合を提供します。ASP.NET WebAPI に基づく REST インターフェイスの作成方法については、「[アプリケーションへの Web フロント エンドの追加][add-web-frontend]」を参照してください。

### Azure クラスターの作成
Service Fabric SDK には、開発およびテスト用のローカル クラスターが用意されています。Azure でのクラスターの作成については、「[Azure ポータルからの Service Fabric クラスターのセットアップ][create-cluster-in-portal]」を参照してください。

### Azure へのアプリケーションの発行
Visual Studio から Azure クラスターに直接アプリケーションを発行することができます。方法については、「[Azure へのアプリケーションの発行][publish-app-to-azure]」を参照してください。

### Service Fabric Explorer を使用したクラスターの視覚化
Service Fabric Explorer には、デプロイ済みのアプリケーションや物理的なレイアウトなど、クラスターを視覚化するための簡単な方法が用意されています。詳細については、「[Service Fabric Explorer を使用したクラスターの視覚化][visualize-with-sfx]」を参照してください。

### サービスのアップグレードとバージョン管理
Service Fabric では、アプリケーションにおいて、独立したサービスの個別のバージョン管理とアップグレードを実行できます。詳細については、「[サービスのバージョン管理とアップグレード][app-upgrade-tutorial]」を参照してください。

### Visual Studio Online を使用した継続的な統合の構成
Service Fabric アプリケーション向けに継続的な統合プロセスを設定する方法については、「[Visual Studio Online を使用した継続的な統合の構成][ci-with-vso]」を参照してください。


<!-- Links -->
[add-web-frontend]: ./service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: ./service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: ./service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: ./service-fabric-visualizing-your-cluster.md
[ci-with-vso]: ./service-fabric-configure-continuous-integration-with-vso.md
[reliable-services-webapi]: ./service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: ./service-fabric-application-upgrade-tutorial.md

<!---HONumber=AcomDC_1125_2015-->