<properties
   pageTitle="アプリケーションのアップグレードのトラブルシューティング | Microsoft Azure"
   description="この記事では、Service Fabric アプリケーションのアップグレードに関する一般的な問題とその解決方法のいくつかについて説明します。"
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
   ms.date="04/14/2016"
   ms.author="subramar"/>

# アプリケーションのアップグレードのトラブルシューティング

この記事では、Azure Service Fabric アプリケーションのアップグレードに関する一般的な問題とその解決方法のいくつかについて説明します。

## アプリケーションのアップグレードで障害が発生した場合のトラブルシューティング

アップグレードに失敗したとき、**Get-ServiceFabricApplicationUpgrade** コマンドの出力に、エラーのデバッグの追加情報が含まれています。この情報を次の目的に使用できます。

1. 失敗の種類を識別する。
2. 失敗の理由を識別する。
3. 詳細な調査のために、障害が発生したコンポーネントを分離する。

**FailureAction** をロールバックするか、またはアップグレードを中断するかどうかに関係なく、Service Fabric がエラーを検出すると、すぐにこの情報を利用できるようになります。

### 失敗の種類を識別する

**Get-ServiceFabricApplicationUpgrade** の出力で、**FailureTimestampUtc** は Service Fabric によってアップグレードの失敗が検出されたタイムスタンプ (UTC) を識別し、**FailureAction** がトリガーされました。**FailureReason** は、エラーの 3 つの潜在的で大まかな原因の 1 つを識別します。

1. UpgradeDomainTimeout - 特定のアップグレード ドメインの完了に時間がかかりすぎ、**UpgradeDomainTimeout** の有効期限が切れていることを識別します。
2. OverallUpgradeTimeout - アップグレード全体の完了に時間がかかりすぎ、**UpgradeTimeout** の有効期限が切れていることを識別します。
3. HealthCheck - アップグレード ドメインをアップグレードした後に、アプリケーションが指定された正常性ポリシーに従って正常でないこと、および **HealthCheckRetryTimeout** 有効期限が切れていることを識別します。

これらのエントリは、アップグレードが失敗し、ロールバックを開始する場合にのみ出力に表示されます。エラーの種類に応じて、詳細な情報が表示されます。

### アップグレードのタイムアウトを調査する

アップグレードのタイムアウト エラーは、サービスの可用性の問題に起因することが最も一般的です。次に示す出力は、サービスのレプリカまたはインスタンスのアップグレードが新しいコードのバージョンで起動に失敗する一般的な例です。**UpgradeDomainProgressAtFailure** フィールドは、エラーの発生時に保留中のアップグレード作業のスナップショットをキャプチャします。

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

この例では、アップグレード ドメイン *MYUD1* でアップグレードが失敗し、2 つのパーティション (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* と *4b43f4d8-b26b-424e-9307-7a7a62e79750*) がスタックし、ターゲット ノード *Node1* と *Node4* でプライマリ レプリカ (*WaitForPrimaryPlacement*) を配置することができないことがわかります。

**Get-ServiceFabricNode** コマンドを使用して、これら 2 つのノードがアップグレード ドメイン *MYUD1* にあることを確認できます。*UpgradePhase* は *PostUpgradeSafetyCheck* を出力していますが、アップグレード ドメイン内のすべてのノードがアップグレードを完了した後にこれらの安全性チェックがなされていることを意味します。これらすべての情報は、潜在的な問題と新しいバージョンのアプリケーション コードを示します。最も一般的な問題は、プライマリ コード パスのオープンまたは昇格でのサービスのエラーです。

*PreUpgradeSafetyCheck* の *UpgradePhase* は、実際にアップグレードを実行する前のアップグレード ドメインの準備で問題があることを意味します。この場合の最も一般的な問題は、プライマリ コード パスのクローズまたは降格でのサービス エラーです。

現在の **UpgradeState** は *RollingBackCompleted* であるため、元のアップグレードは、ロールバック **FailureAction** (障害発生時にアップグレードを自動的にロールバックする) を使用して実行済みのはずです。元のアップグレードが手動の **FailureAction** を使用して実行されている場合は、アプリケーションのライブ デバッグを実行できるように、アップグレードが代わりに中断状態になります。

### 正常性チェックの障害を調査する

正常性チェックの障害は、アップグレード ドメインのすべてのノードがアップグレードを終了し、すべての安全性チェックを渡した後で、さまざまなその他の問題によりトリガーされる可能性があります。次に示す出力は、正常性チェックの失敗により、アップグレードに障害が発生する一般的な例です。**UnhealthyEvaluations** フィールドは、ユーザー指定の[正常性ポリシー](service-fabric-health-introduction.md)に従い、アップグレードの障害発生時に失敗したすべての正常性チェックのスナップショットを取得します。

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

正常性チェックの障害を調査するには、まず Service Fabric の正常性モデルを理解する必要があります。ただし、詳しく理解していなくても、*fabric:/DemoApp/Svc3* と *fabric:/DemoApp/Svc2* という 2 つのサービスに問題が発生し、エラーの正常性レポート (この例では "InjectedFault") が生成されていることがわかります。この例では、4 つのサービスのうち 2 つは異常な状態で、既定のターゲットの 0% の異常な状態を下回っています (*MaxPercentUnhealthyServices*)。

アップグレードを開始するときに、**FailureAction** を手動で指定することで、アップグレードが中断されたため、追加のアクションを実行する前に、必要に応じて障害が発生している状態のライブ システムを調査できます。

### 中断されたアップグレードから回復する

ロールバック **FailureAction** を使用すると、障害が発生したときにアップグレードが自動的にロールバックされるため、復旧は必要ありません。手動の **FailureAction** を使用する場合、いくつかの回復オプションがあります。

1. ロールバックを手動でトリガーする
2. アップグレードの残りの項目を手動で続行する
3. 監視対象のアップグレードを再開する

**Start-ServiceFabricApplicationRollback** コマンドを使用すると、アプリケーションのロールバックをいつでも開始できます。コマンドが正常に返されると、ロールバック要求が、システムに登録されてすぐに開始されます。

**Resume-ServiceFabricApplicationUpgrade** コマンドを使用すると、アップグレードの残りの項目を一度にアップグレード ドメインを 1 つずつ手動で続行できます。このモードでは、安全性チェックのみが自動実行されます。その他の正常性チェックは実行されません。このコマンドは、*UpgradeState* が *RollingForwardPending* を示している場合、つまり、現在のアップグレード ドメインがアップグレードを完了したが、次のアップグレード ドメインがまだ開始されていない (保留中) の場合にのみ使用できます。

**Update-ServiceFabricApplicationUpgrade** コマンドを使用すると、安全性チェックと正常性チェックの両方が実行されている、監視対象のアップグレードを再開できます。

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

アップグレードは最後に中断したアップグレード ドメインから続行し、以前と同じアップグレード パラメーターと正常性ポリシーを使用します。必要に応じて、上記の出力に示すアップグレード パラメーターと正常性ポリシーを、アップグレードを再開するときと同じコマンドで変更できます。この例では、アップグレードが、その他すべてのパラメーターは変更されないまま監視対象モードで再開されたため、以前と同じパラメーターと正常性ポリシーを使用しています。

## 詳細なトラブルシューティング

### Service Fabric が指定された正常性ポリシーに従っていない

考えられる原因 1:

Service Fabric は、正常性評価のためのエンティティ (レプリカ、パーティション、サービスなど) の実際の数値にすべてのパーセンテージを変換し、常に全体のエンティティ数を最も近い値に切り上げします。たとえば、最大の _MaxPercentUnhealthyReplicasPerPartition_ が 21% で、5 個のレプリカがある場合、Service Fabric はパーティションの正常性を評価するときに最大 2 個のレプリカ (つまり `Math.Ceiling (5*0.21)`) が異常であることを許可します。正常性ポリシーは、この点を考慮して設定してください。

考えられる原因 2:

正常性ポリシーは、特定のサービス インスタンスではなく、サービスの合計のパーセンテージにより指定されます。たとえば、アップグレードの前に、アプリケーションに A、B、C、および D の 4 つのサービス インスタンスがあり、サービス D が異常だが、アプリケーションに大きく影響がないとします。アップグレード中に、既知の問題のあるサービス D を無視するため、パラメーター *MaxPercentUnhealthyServices* を 25% になるよう設定し、正常であることが必要とされるのが A、B、および C のみとなるようにします。

ただし、アップグレード中、C が異常になり、D が正常になる可能性がありますこの場合でも、アップグレードは正常に完了します。これは、サービスの 25% のみが異常であり、D ではなく C により予期しないエラーが発生する可能性があるためです。この状態では、D は A、B、および C とは異なるサービス型としてモデル化する必要があります。正常性ポリシーはサービス型ごとに指定できるため、これによりさまざまな異常の割合のしきい値を、アプリケーションの役割に基づいて別のサービスに割り当てることができます。

### アプリケーションのアップグレードの正常性ポリシーを指定していないが、未指定のタイムアウトによりアップグレードが失敗する

正常性ポリシーがアップグレード要求に指定されていない場合、現在のアプリケーションのバージョンの *ApplicationManifest.xml* から取得されますたとえば、アプリケーション X を v1 から v2 にアップグレードしている場合、アプリケーション X の v1 に指定されたアプリケーションの正常性ポリシーが使用されます。別の正常性ポリシーをアップグレードに使用するには、アプリケーションのアップグレードの API 呼び出しの一部としてポリシーを指定する必要があります。アップグレードの実行中、API 呼び出しの一部として指定されたポリシーのみが適用されることに注意してください。アップグレードが完了すると、*ApplicationManifest.xml* で指定されたポリシーが使用されます。

### 不適切なタイムアウトが指定されている

*UpgradeTimeout* が *UpgradeDomainTimeout* より小さい場合など、タイムアウトの設定に一貫性がないとどうなるでしょうか。このような場合は、エラーが返されます。ほかにこのようなことが起こるのは、*UpgradeDomainTimeout* が *HealthCheckWaitDuration* と *HealthCheckRetryTimeout* の合計より小さいか、*UpgradeDomainTimeout* が *HealthCheckWaitDuration* と *HealthCheckStableDuration* の合計より小さい場合です。

### アップグレードの時間がかかりすぎる

アップグレードを完了するのにかかる時間は、指定されたさまざまな正常性チェックとタイムアウトにより異なります。これらは、アプリケーションのアップグレード (パッケージのコピー、デプロイ、安定化を含む) にかかる時間により異なります。タイムアウトが短すぎるとアップグレードが失敗する可能性が高くなるため、タイムアウトは長めにして開始することをお勧めします。

タイムアウトが、アップグレードの時間にどのように影響するのか、以下で簡単に確認できます。

アップグレード ドメインのアップグレードは、*HealthCheckWaitDuration* + *HealthCheckStableDuration* よりも速く完了できません。

アップグレードの失敗が、*HealthCheckWaitDuration* + *HealthCheckRetryTimeout* より速く発生することはあり得ません。

アップグレード ドメインのアップグレード時間は、*UpgradeDomainTimeout* によって制限されます。*HealthCheckRetryTimeout* と *HealthCheckStableDuration* が両方とも 0 以外であり、アプリケーションの正常性が切り替わる場合は、アップグレードが最終的に *UpgradeDomainTimeout* でタイムアウトします。*UpgradeDomainTimeout* は、現在のアップグレード ドメインのアップグレードが開始されると、カウント ダウンを開始します。

## 次のステップ

「[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md)」では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

「[Powershell によるアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md)」では、PowerShell を使用してアプリケーションをアップグレードする方法について説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

「[高度なトピック](service-fabric-application-upgrade-advanced.md)」を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。

「[アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。
 

<!---HONumber=AcomDC_0420_2016-->