
<properties
   pageTitle="アプリケーションのアップグレード: アップグレードのパラメーター | Microsoft Azure"
   description="Service Fabric アプリケーションのアップグレードに関連するパラメーターについて説明します。正常性確認やアップグレードを自動的に取り消すポリシーなどが含まれます。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>



# アプリケーション アップグレードのパラメーター

この記事では、Service Fabric アプリケーションのアップグレード中に適用されるさまざまなパラメーターについて説明します。パラメーターには、アプリケーションの名前とバージョン、アップグレード中に適用されるタイムアウトと正常性チェックをコントロールするノブが含まれ、アップグレードが失敗したときに適用されるポリシーを指定します。


<br>

| パラメーター | 説明 |
| --- | --- |
| ApplicationName | アップグレードするアプリケーションの名前です。例: ファブリック:/VisualObjects、ファブリック:/ClusterMonitor |
| TargetApplicationTypeVersion | ターゲットをアップグレードするアプリケーションの種類のバージョンです。 |
| FailureAction | FailureAction プロパティは、アップグレードが失敗した場合に、Service Fabric で実行されるアクションを記述します。アプリケーションは、更新前の最後のバージョンにロールバック (Rollback) されることがあります。または、現在のアップグレード ドメインでアプリケーションのアップグレードを停止してアップグレード モードを手動に変更します。使用できる値は、ロールバックと手動です。 |
| HealthCheckWaitDurationSec | Service Fabric でアプリケーションの正常性を評価する前に、アップグレード ドメインでのアップグレード完了後の待機時間を秒単位で指定します。この期間は、アプリケーションが正常と見なされる前に、アプリケーションが実行されている必要がある時間としても考慮されます。正常性チェックに合格すると、アップグレード プロセスは次のアップグレード ドメインに進みます。正常性チェックに合格しない場合、Service Fabric は HealthCheckRetryTimeout に達するまで、一定期間 (UpgradeHealthCheckInterval) 待機してから、正常性チェックをもう一度実行します。既定値と推奨値は、0 秒です。 |
| HealthCheckRetryTimeoutSec | アップグレードを失敗として宣言する前に、Service Fabric が正常性評価を実行し続ける期間 (秒) です。既定値は 600 秒です。この期間は HealthCheckWaitDuration に達した後に開始します。Service Fabric は、この HealthCheckRetryTimeout 内で、アプリケーションの正常性についての複数のチェックを実行することがあります。既定値は 10 分です。アプリケーション用に適切にカスタマイズすることをお勧めします。 |
| HealthCheckStableDurationSec | 次のアップグレード ドメインへの移行またはアップグレードが完了する前に、アプリケーションが安定していることを確認するための待機時間 (秒)。この待機期間を使用して、正常性チェックが実行された直後に、正常性の変更が未検出にならないようにします。既定値は 0 秒です。アプリケーション用に適切にカスタマイズすることをお勧めします。 |
| UpgradeDomainTimeoutSec | 1 つのアップグレード ドメインをアップグレードするための最大時間 (秒)。このタイムアウトに達すると、アップグレードは停止され、UpgradeFailureAction で指定したアクションが実行されます。既定値は、なし (無限) です。アプリケーション用に適切にカスタマイズすることをお勧めします。 |
| UpgradeTimeout | 全体のアップグレードに適用されるタイムアウト (秒)。このタイムアウトに達すると、アップグレードは停止され、UpgradeFailureAction がトリガーされます。既定値は、なし (無限) です。アプリケーション用に適切にカスタマイズすることをお勧めします。 |
| UpgradeHealthCheckInterval | _クラスター_ _マニフェスト_の ClusterManager セクションで指定されています (これはアップグレード コマンドレットの一部としては指定されません)。この設定では、正常性状態をチェックする頻度を指定します (既定値は 60 秒)。 |






<br>
## アプリケーション アップグレード中のサービス正常性評価

<br>正常性の評価基準は省略可能です。アップグレードを開始するときに、正常性評価の基準が指定されていない場合、Service Fabric はアップグレード中のアプリケーション インスタンスの ApplicationManifest.xml で指定されているアプリケーションの正常性ポリシーを使用します。


<br>

| パラメーター | 説明 |
| --- | --- |
| ConsiderWarningAsError | 既定値は False です。アップグレード中に、アプリケーションの正常性を評価するときに、アプリケーションの警告正常性状態をエラーとして扱います。既定では、Service Fabric は警告イベントがある場合でもアップグレードを続行できるように、エラーになるイベントの警告正常性のイベントは評価しません。 |
| MaxPercentUnhealthyDeployedApplications | 既定値と推奨値は、0 です。アップグレードが正常でないとみなされ、アップグレードに失敗する前にデプロイされるアプリケーションの最大数 ([正常性のセクション](service-fabric-health-introduction.md)をご覧ください) を指定します。これは、ノード上にあるパッケージされたアプリケーションの正常性で、アップグレード中の即時の問題やノード上にデプロイされているアプリケーション パッケージの正常性がない (クラッシュするなど) ことを検出するのに役立ちます。一般的な例では、アプリケーションのレプリカは、その他のノードに負荷分散します。これにより、アプリケーションが正常に表示され、アップグレードを続行できます。厳密な MaxPercentUnhealthyDeployedApplications を指定すると、Service Fabric はアプリケーション パッケージで高速に問題を検出し、高速アップグレードに失敗します。 |
| MaxPercentUnhealthyServices | 既定値と推奨値は、0 です。アップグレードが正常でないとみなされてアップグレードに失敗する前に、正常性がなくなることを許可するアプリケーション インスタンス内のサービスの最大数を指定します。 |
| MaxPercentUnhealthyPartitionsPerService | 既定値と推奨値は、0 です。サービス内のパーティション最大数に指定すると、サービスが正常でないとみなされる前に、正常でなくなる可能性があります。 |
| MaxPercentUnhealthyReplicasPerPartition | 既定値と推奨値は、0 です。パーティションが正常でないとみなされる前に、正常でない状態になるパーティション内のレプリカの最大数を指定します。 |
| UpgradeReplicaSetCheckTimeout | ステートレス サービス - Service Fabric は単一のアップグレード ドメイン内で、サービスの追加インスタンスを確実に使用できるようにしようとします。ターゲット インスタンス数が 2 つ以上の場合、(UpgradeReplicaSetCheckTimeout プロパティで指定した) 最大タイムアウト値になるまで、2 つ以上のインスタンスが使用可能になるまで待機します。タイムアウトになると、サービス インスタンス数にかかわらず、Service Fabric はアップグレードを続行します。ターゲット インスタンス数が 1 つの場合、Service Fabric は待機せずに、すぐにアップグレードを実行します。ステートフル サービス- Service Fabric は 1 つのアップグレード ドメイン内で、レプリカ セットに確実にクォーラムが含まれるようにしようとします。Service Fabric は UpgradeReplicaSetCheckTimeout プロパティで指定した最大タイムアウト値になるまで、クォーラムが使用可能になるまで待機します。タイムアウトになると、クォーラムにかかわらずアップグレードを続行します。この設定は、ロール フォワード時には、しない (無限) に設定され (想定どおりにアップグレードが進むときに適用)、ロール バック時には 900 秒に設定されます (アップグレードがエラーになりロール バックされるときに適用)。 |
| ForceRestart | サービス コードを更新せずに構成やデータ パッケージを更新する場合は、ForceRestart プロパティが API 呼び出しの一部として true に設定されていない限り、Service Fabric はサービスを再起動しません。更新が完了すると、Service Fabric は新しい構成パッケージやデータ パッケージを使用可能なサービスを知らせます。サービスは、変更を適用する役割を担います。必要に応じて、サービス自体を再起動できます。 |



<br> <br> MaxPercentUnhealthyServices、MaxPercentUnhealthyPartitionsPerService、MaxPercentUnhealthyReplicasPerPartition の基準は、アプリケーション インスタンスのサービスの種類ごとに指定できます。これにより、さまざまなサービスの種類を含むアプリケーションに、それぞれのサービスの種類に対して異なる評価ポリシーを含めることができます。たとえば、ステートレスなゲートウェイ サービスの種類には、特定のアプリケーション インスタンスのステートフルなエンジン サービスの種類とは異なる MaxPercentUnhealthyPartitionsPerService を含めることができます。

## 次のステップ


[Visual Studio による Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)

[PowerShell を使用した Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade-powershell.md)

[手動アップグレードと差分のパッケージを使用したアップグレード](service-fabric-application-upgrade-advanced.md)

[アプリケーション アップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)
 

<!---HONumber=AcomDC_1125_2015-->