<properties
   pageTitle="Service Fabric アプリケーションのアップグレード: 高度なトピック"
   description="この記事では、Service Fabric アプリケーションのアップグレードに関連する高度なトピックについて説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>

# Service Fabric アプリケーションのアップグレード: 高度なトピック

## 手動アップグレード モード

> [AZURE.NOTE]エラーが発生したまたは中断されたアップグレードに対してのみ、Unmonitored Manual モードも検討します。Monitored モードは、Service Fabric アプリケーションで推奨されるアップグレード モードです。

Service Fabric では開発と運用環境のクラスターをサポートする複数のアップグレード モードが提供されます。各デプロイメント オプションは、さまざまな環境に適しています。Monitored Rolling Application Upgrade は、実稼働環境で使用される最も一般的なアップグレードです。アップグレードのポリシーを指定すると、Service Fabric は、アップグレードを実行する前に、アプリケーションの正常性を確認します。より多くのカスタマイズや複雑な正常性評価ポリシーが必要とされる、または一般的ではないアップグレードがある (アプリケーションが既にデータの損失の状態にあるなど) 特定の状況では、アプリケーション管理者は、Manual Rolling Application Upgrade モードを使用して、さまざまなアップグレード ドメインからアップグレードの進行状況を全面的に制御できます。最後に、Automated Rolling Application Upgrade は、開発やテスト環境にサービス開発中の迅速な繰り返しサイクルを提供するのに役立ちます。

**手動** - 現在の UD でアプリケーションのアップグレードを停止し、アップグレード モードを Unmonitored Manual に変更します。管理者は、**MoveNextApplicationUpgradeDomainAsync** を手動で呼び出して、アップグレードを続行したり、新しいアップグレードを開始することでロールバックをトリガーしたりします。アップグレードが、手動モードに入ると、新しいアップグレードが開始されるまでは手動モードのままになります。**GetApplicationUpgradeProgressAsync**コマンドは、FABRIC_APPLICATION_UPGRADE_STATE_ROLLING_FORWARD_PENDING を返します。

## 差分のパッケージを使用したアップグレード

Service Fabric アプリケーションは、完全な自己完結型のアプリケーション パッケージで、プロビジョニングすることでアップグレードできます。更新済みのアプリケーション ファイル、更新済みのアプリケーション マニフェスト、サービス マニフェスト ファイルのみを含む差分のパッケージを使用してアプリケーションをアップグレードすることもできます。

完全なアプリケーション パッケージには、Service Fabric の起動と実行に必要なすべてのファイルが含まれます。差分のパッケージには、最後のプロビジョニングと現在のアップグレード間で変更されたファイル、完全なアプリケーション マニフェスト、サービス マニフェスト ファイルのみが含まれます。ビルドのレイアウトで Service Fabric が検出できないアプリケーション マニフェストやサービス マニフェストでの参照について、Service Fabric は ImageStore (リンク TBA) 内の参照を検索します。

完全なアプリケーション パッケージは、クラスターへのアプリケーションの最初のインストールに必要です。以降の更新は、完全なアプリケーションのパッケージまたは差分のパッケージのいずれかになります。

差分のパッケージの使用は次のような場合に適しています。

* 差分のパッケージは、複数のサービス マニフェスト ファイルや複数のコード パッケージ、構成パッケージ、データ パッケージを参照する大規模なアプリケーション パッケージがある場合に適しています。

* 差分のパッケージは、アプリケーションのビルド プロセスから直接、ビルドのレイアウトを生成する、デプロイメント システムがあるときに適しています。この場合は、コードは変更されていなくても、新しくビルドされたアセンブリにはさまざまなチェックサムがあります。完全なアプリケーション パッケージを使用すると、すべてのコード パッケージのバージョンを更新する必要があります。差分のパッケージを使用すると、変更されたファイルとバージョンが変更されているマニフェスト ファイルのみが提供されます。

## 次のステップ

[アップグレード チュートリアル](service-fabric-application-upgrade-tutorial.md)

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)

[アプリケーション アップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)
 

<!---HONumber=July15_HO4-->