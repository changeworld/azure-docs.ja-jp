
---
title: "Azure Service Fabric クラスター設定の変更 | Microsoft Docs"
description: "この記事では、カスタマイズ可能な Fabric の設定と Fabric アップグレード ポリシーについて説明します。"
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 2f4911d012b0e63f90ad1567a6fc3ff429516d75
ms.openlocfilehash: 8981d9149f925e003652a16d308c14b7e105a8d5


---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Service Fabric クラスターの設定と Fabric アップグレード ポリシーのカスタマイズ
このドキュメントでは、Service Fabric クラスターのさまざまな Fabric 設定と Fabric アップグレード ポリシーをカスタマイズする方法について説明します。 この設定やポリシーは、ポータルまたは Azure Resource Manager テンプレートを使用してカスタマイズできます。

## <a name="fabric-settings-that-you-can-customize"></a>カスタマイズできる Fabric の設定
カスタマイズできる Fabric の設定を次に示します。

### <a name="section-name-diagnostics"></a>セクション名: Diagnostics
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| ConsumerInstances |String |DCA コンシューマー インスタンスのリスト。 |
| ProducerInstances |string |DCA プロデューサー インスタンスのリスト。 |
| AppEtwTraceDeletionAgeInDays |int、既定値は 3 |アプリケーションの ETW トレースが含まれた古い ETL ファイルを削除するまでの日数。 |
| AppDiagnosticStoreAccessRequiresImpersonation |ブール値、既定値は true |アプリケーションの代わりに診断ストアにアクセスするときに偽装が必要かどうかを示します。 |
| MaxDiskQuotaInMB |int、既定値は 65536 |Windows Fabric のログ ファイルのディスク クォータ (MB)。 |
| DiskFullSafetySpaceInMB |int、既定値は 1024 |DCA によって使用されないようにする残りのディスク領域 (MB)。 |
| ApplicationLogsFormatVersion |int、既定値は 0 |アプリケーション ログの形式のバージョン。 サポートされる値は 0 と 1 です。 バージョン 1 には、バージョン 0 よりも多くの ETW イベント レコードのフィールドが含まれます。 |
| ClusterId |String |クラスターの一意の ID。 これはクラスターの作成時に生成されます。 |
| EnableTelemetry |ブール値、既定値は true |テレメトリを有効または無効にします。 |
| EnableCircularTraceSession |ブール値、既定値は false |循環トレース セッションを使用する必要があるかどうかを示すフラグ。 |

### <a name="section-name-traceetw"></a>セクション名: Trace/Etw
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| Level |int、既定値は 4 |トレースの ETW レベル。 |

### <a name="section-name-performancecounterlocalstore"></a>セクション名: PerformanceCounterLocalStore
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| IsEnabled |ブール値、既定値は true |ローカル ノードでのパフォーマンス カウンターの収集が有効になっているかどうかを示すフラグ。 |
| SamplingIntervalInSeconds |int、既定値は 60 |収集するパフォーマンス カウンターのサンプリング間隔。 |
| Counters |String |収集するパフォーマンス カウンターのコンマ区切りリスト。 |
| MaxCounterBinaryFileSizeInMB |int、既定値は 1 |各パフォーマンス カウンター バイナリ ファイルの最大サイズ (MB)。 |
| NewCounterBinaryFileCreationIntervalInMinutes |int、既定値は 10 |新しいパフォーマンス カウンター バイナリ ファイルを作成するまでの最大間隔 (秒単位)。 |

### <a name="section-name-setup"></a>セクション名: Setup
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| FabricDataRoot |String |Windows Fabric のデータ ルート ディレクトリ。 |
| FabricLogRoot |string |Windows Fabric のログ ルート ディレクトリ。 |
| ServiceRunAsAccountName |String |Fabric ホスト サービスを実行するアカウント名。 |
| ServiceStartupType |String |Fabric ホスト サービスのスタートアップの種類。 |
| SkipFirewallConfiguration |ブール値、既定値は false |ファイアウォール設定をスキップするかどうかを示します。 |

### <a name="section-name-transactionalreplicator"></a>セクション名: TransactionalReplicator
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| MaxCopyQueueSize |uint、既定値は 16384 |レプリケーション操作を保持するキューの初期サイズの最大値を定義します。 この値は 2 の累乗にする必要があります。 ランタイムにキューがこのサイズに達すると、プライマリ レプリケーターとセカンダリ レプリケーターの間で操作が調整されます。 |
| BatchAcknowledgementInterval | 時間 (秒単位)、既定値は 0.015 | timespan を秒単位で指定します。 操作を受信してから受信確認を返すまで、レプリケーターが待機する時間を指定します。 この期間に受信した他の操作の受信確認は&1; つのメッセージで返されます。これにより、ネットワーク トラフィックは減少しますが、レプリケーターのスループットが低下する可能性があります。 |
| MaxReplicationMessageSize |uint、既定値は 52428800 | レプリケーション操作の最大メッセージ サイズ。 既定値は 50 MB です。 |
| ReplicatorAddress |wstring、既定値は "localhost:0" | 文字列 "IP:Port" の形式のエンドポイント。この文字列は、Windows Fabric Replicator が操作を送受信するために、他のレプリカとの接続を確立する際に使用されます。 |
| InitialPrimaryReplicationQueueSize |uint、既定値は 64 | この値は、プライマリでレプリケーション操作を保持するキューの初期サイズを定義します。 この値は 2 の累乗にする必要があります。|
| MaxPrimaryReplicationQueueSize |uint、既定値は 8192 |プライマリ レプリケーション キューに存在する可能性がある操作の最大数。 この値は 2 の累乗にする必要があります。 |
| MaxPrimaryReplicationQueueMemorySize |uint、既定値は 0 |プライマリ レプリケーション キューの最大値 (バイト単位)。 |
| InitialSecondaryReplicationQueueSize |uint、既定値は 64 |この値は、セカンダリでレプリケーション操作を保持するキューの初期サイズを定義します。 この値は 2 の累乗にする必要があります。 |
| MaxSecondaryReplicationQueueSize |uint、既定値は 16384 |セカンダリ レプリケーション キューに存在する可能性がある操作の最大数。 この値は 2 の累乗にする必要があります。 |
| MaxSecondaryReplicationQueueMemorySize |uint、既定値は 0 |セカンダリ レプリケーション キューの最大値 (バイト単位)。 |
| SecondaryClearAcknowledgedOperations |ブール値、既定値は false |セカンダリ レプリケーターでの操作の受信確認がプライマリに返された後 (ディスクにフラッシュされた後)、それらの操作をクリアするかどうかを制御するブール値。 この値を true に設定すると、フェールオーバー後にレプリカが取得され、新しいプライマリで追加のディスク読み取りが発生する可能性があります。 |
| MaxMetadataSizeInKB |int、既定値は 4 |ログ ストリーム メタデータの最大サイズ。 |
| MaxRecordSizeInKB |uint、既定値は 1024 | ログ ストリーム レコードの最大サイズ。 |
| CheckpointThresholdInMB |int、既定値は 50 |ログの使用量がこの値を超えると、チェックポイントが開始されます。 |
| MaxAccumulatedBackupLogSizeInMB |int、既定値は 800 |1 つのバックアップ ログ チェーンに含まれるバックアップ ログの最大累積サイズ (MB)。 増分バックアップを実行してバックアップ ログが生成されると関連する完全バックアップ以降の累積バックアップ ログがこのサイズを超える場合は、増分バックアップ要求が失敗します。 そのような場合には、ユーザーは完全バックアップを取得する必要があります。 |
| MaxWriteQueueDepthInKB |int、既定値は 0 | コア ロガーがこのレプリカに関連付けられているログに使用できる、キロバイト単位で指定された書き込みキューの最大深さを表す int。 この値は、コア ロガーの更新時に未処理になる可能性がある最大バイト数です。 0 の場合、コア ロガーは適切な値または 4 の倍数を計算します。 |
| SharedLogId |String |共有ログの識別子。 これは GUID であり、共有ログごとに一意である必要があります。 |
| SharedLogPath |String |共有ログのパス。 この値が空の場合、既定の共有ログが使用されます。 |
| SlowApiMonitoringDuration |秒単位。既定値は 300 | 警告正常性イベントが発生するまでの API の期間を指定します。|
| MinLogSizeInMB |int、既定値は 0 |トランザクション ログの最小サイズ。 この設定を下回るサイズまでログを切り捨てることはできません。 0 は、レプリケーターが他の設定に従って最小ログ サイズを決定することを示します。 この値を大きくすると、関連するログ レコードが切り捨てられる可能性が低くなるため、部分的なコピーと増分バックアップが実行される可能性が高くなります。 |

### <a name="section-name-fabricclient"></a>セクション名: FabricClient
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| NodeAddresses |wstring、既定値は L"" |ネーム サービスとの通信に使用できるさまざまなノード上のアドレス (接続文字列) のコレクション。 最初に、クライアントはアドレスのいずれかをランダムに選択して接続します。 複数の接続文字列が指定されている場合、通信エラーやタイムアウト エラーによって接続が失敗すると、クライアントは次のアドレスに順次切り替えて使用します。 再試行セマンティクスの詳細については、ネーム サービス アドレスの再試行のセクションを参照してください。 |
| ConnectionInitializationTimeout |時間 (秒単位)、既定値は 2 |timespan を秒単位で指定します。 クライアントがゲートウェイへの接続を開こうとするときの接続タイムアウト間隔。 |
| PartitionLocationCacheLimit |int、既定値は 100000 |サービスの解決用にキャッシュされるパーティションの数 (無制限にするには、0 に設定します)。 |
| ServiceChangePollInterval |時間 (秒単位)、既定値は 120 |timespan を秒単位で指定します。 クライアントからゲートウェイに登録済みのサービス変更通知をコールバックするための、サービスの変更の連続するポーリングの間隔。 |
| KeepAliveIntervalInSeconds |int、既定値は 20 |FabricClient トランスポートが、ゲートウェイにキープアライブ メッセージを送信する間隔。 0 の場合、keepAlive は無効になります。 正の数を指定する必要があります。 |
| HealthOperationTimeout |時間 (秒単位)、既定値は 120 |timespan を秒単位で指定します。 Health Manager に送信されるレポート メッセージのタイムアウト。 |
| HealthReportSendInterval |秒単位。既定値は 30 |timespan を秒単位で指定します。 レポート コンポーネントが、累積した正常性レポートを Health Manager に送信する間隔。 |
| HealthReportRetrySendInterval |秒単位。既定値は 30 |timespan を秒単位で指定します。 レポート コンポーネントが、累積した正常性レポートを Health Manager に再送信する間隔。 |
| RetryBackoffInterval |時間 (秒単位)、既定値は 3 |timespan を秒単位で指定します。 操作を再試行するまでのバックオフ間隔。 |
| MaxFileSenderThreads |uint、既定値は 10 |並列転送されるファイルの最大数。 |

### <a name="section-name-common"></a>セクション名: Common
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| PerfMonitorInterval |時間 (秒単位)、既定値は 1 |timespan を秒単位で指定します。 パフォーマンスの監視間隔。 0 または負の値に設定すると、監視が無効になります。 |

### <a name="section-name-healthmanager"></a>セクション名: HealthManager
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |ブール値、既定値は false |クラスターの正常性評価ポリシー: アプリケーションの種類ごとの正常性評価を有効にします。 |

### <a name="section-name-fabricnode"></a>セクション名: FabricNode
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| StateTraceInterval |秒単位。既定値は 300 |timespan を秒単位で指定します。 FM/FMM で各ノードおよび実行中のノードの状態をトレースする間隔。 |

### <a name="section-name-nodedomainids"></a>セクション名: NodeDomainIds
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| UpgradeDomainId |wstring、既定値は L"" |ノードが属するアップグレード ドメインを示します。 |
| PropertyGroup |NodeFaultDomainIdCollection |ノードが属する障害ドメインを示します。 障害ドメインは、データセンター内のノードの場所を示す URI によって定義されます。  障害ドメインの URI は、fd:/fd/ の後に URI パス セグメントが続く形式になります。|

### <a name="section-name-nodeproperties"></a>セクション名: NodeProperties
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |ノードのプロパティの文字列キーと値のペアのコレクション。 |

### <a name="section-name-nodecapacities"></a>セクション名: NodeCapacities
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |さまざまなメトリックのノード容量のコレクション。 |

### <a name="section-name-fabricnode"></a>セクション名: FabricNode
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| StartApplicationPortRange |int、既定値は 0 |ホスティング サブシステムによって管理されるアプリケーション ポートの開始。 Hosting で EndpointFilteringEnabled が true の場合は必須です。 |
| EndApplicationPortRange |int、既定値は 0 |ホスティング サブシステムによって管理されるアプリケーション ポートの終了 (この値を含まない)。 Hosting で EndpointFilteringEnabled が true の場合は必須です。 |
| ClusterX509StoreName |wstring、既定値は L"My" |クラスター内通信をセキュリティで保護するためのクラスター証明書を格納する X.509 証明書ストアの名前。 |
| ClusterX509FindType |wstring、既定値は L"FindByThumbprint" |ClusterX509StoreName で指定されたストア内でのクラスター証明書の検索方法を示します。サポートされる値は "FindByThumbprint" と "FindBySubjectName" です。"FindBySubjectName" を指定すると、一致するものが複数ある場合に、有効期限が最も長いものが使用されます。 |
| ClusterX509FindValue |wstring、既定値は L"" |クラスター証明書の検索に使用する検索フィルター値。 |
| ClusterX509FindValueSecondary |wstring、既定値は L"" |クラスター証明書の検索に使用する検索フィルター値。 |
| ServerAuthX509StoreName |wstring、既定値は L"My" |エントリ サービスのサーバー証明書を格納する X.509 証明書ストアの名前。 |
| ServerAuthX509FindType |wstring、既定値は L"FindByThumbprint" |ServerAuthX509StoreName で指定されたストア内でのサーバー証明書の検索方法を示します。サポートされる値は、FindByThumbprint と FindBySubjectName です。 |
| ServerAuthX509FindValue |wstring、既定値は L"" |サーバー証明書の検索に使用する検索フィルター値。 |
| ServerAuthX509FindValueSecondary |wstring、既定値は L"" |サーバー証明書の検索に使用する検索フィルター値。 |
| ClientAuthX509StoreName |wstring、既定値は L"My" |既定の管理者ロールの FabricClient の証明書を格納する X.509 証明書ストアの名前。 |
| ClientAuthX509FindType |wstring、既定値は L"FindByThumbprint" |ClientAuthX509StoreName で指定されたストア内での証明書の検索方法を示します。サポートされる値は、FindByThumbprint と FindBySubjectName です。 |
| ClientAuthX509FindValue |wstring、既定値は L"" | 既定の管理者ロールの FabricClient の証明書を検索する際に使用する検索フィルター値。 |
| ClientAuthX509FindValueSecondary |wstring、既定値は L"" |既定の管理者ロールの FabricClient の証明書を検索する際に使用する検索フィルター値。 |
| UserRoleClientX509StoreName |wstring、既定値は L"My" |既定のユーザー ロールの FabricClient の証明書を格納する X.509 証明書ストアの名前。 |
| UserRoleClientX509FindType |wstring、既定値は L"FindByThumbprint" |UserRoleClientX509StoreName で指定されたストア内での証明書の検索方法を示します。サポートされる値は、FindByThumbprint と FindBySubjectName です。 |
| UserRoleClientX509FindValue |wstring、既定値は L"" |既定のユーザー ロールの FabricClient の証明書を検索する際に使用する検索フィルター値。 |
| UserRoleClientX509FindValueSecondary |wstring、既定値は L"" |既定のユーザー ロールの FabricClient の証明書を検索する際に使用する検索フィルター値。 |

### <a name="section-name-paas"></a>セクション名: Paas
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| ClusterId |wstring、既定値は L"" |ファブリックで構成の保護に使用される X509 証明書ストア。 |

### <a name="section-name-fabrichost"></a>セクション名: FabricHost
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| StopTimeout |秒単位。既定値は 300 |timespan を秒単位で指定します。 ホストされるサービスのアクティブ化、非アクティブ化、アップグレードのタイムアウト。 |
| StartTimeout |秒単位。既定値は 300 |timespan を秒単位で指定します。 fabricactivationmanager の起動のタイムアウト。 |
| ActivationRetryBackoffInterval |時間 (秒単位)、既定値は 5 |timespan を秒単位で指定します。 アクティブ化失敗時のバックオフ間隔。アクティブ化が連続して失敗するたびに、MaxActivationFailureCount で指定された回数までアクティブ化が再試行されます。 各試行の再試行間隔は、継続的なアクティブ化失敗とアクティブ化バックオフ間隔の積になります。 |
| ActivationMaxRetryInterval |秒単位。既定値は 300 |timespan を秒単位で指定します。 アクティブ化の最大再試行間隔。 連続して失敗するたびに、再試行間隔が Min(ActivationMaxRetryInterval; Continuous Failure Count * ActivationRetryBackoffInterval) として計算されます。 |
| ActivationMaxFailureCount |int、既定値は 10 |システムが失敗したアクティブ化を再試行する最大回数。この回数に達すると、再試行が停止されます。 |
| EnableServiceFabricAutomaticUpdates |ブール値、既定値は false |Windows Update によるファブリックの自動更新を有効にします。 |
| EnableServiceFabricBaseUpgrade |ブール値、既定値は false |サーバーの基本的な更新を有効にします。 |
| EnableRestartManagement |ブール値、既定値は false |サーバーの再起動を有効します。 |

### <a name="section-name-votes"></a>セクション名: Votes
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| PropertyGroup |VoteConfig |投票権は&1; 票としてクラスターのクォーラムに反映されます。 投票権は、投票機関によって投票権所有者 (ノード) に割り当てられます。 通常、投票機関はクラスター内のノード (シード ノード) です。 クラスターが正常で稼働状態を維持できることを保証するには、クォーラムの投票が存在する必要があります。  クォーラムの投票が失われると、クラスターはダウンします。 シード ノードに代わるものが SQL 投票です。SQL 投票では、投票機関はクラスター内のノードではなく、SQL Server インスタンスになります。 この場合、SQL 投票 ID に最も近い ID を持つノードがプロキシとして機能します。 構成によって選択された投票数はすべてのノードで同じである必要があります。 ID は長整数に解析される文字列であり、クラスター内での投票の ID を表します。 クラスターで使用される投票機関に応じて、型には SeedNode または SqlServer のいずれかを指定できます。 接続文字列の形式は型によって異なります。 SeedNode の接続文字列は、同じ NodeID を持つノードの NodeEndpoint です。 型が SqlServer の場合、SQL Server 2008 以降への接続文字列になります。 SeedNode の例: '0 = SeedNode;10.0.0.1:10000'。 SQL 投票の例: 'sqlvote1 = SqlServer;Provider=SQLNCLI10;Server=.\SQLEXPRESS;Database=master;Integrated Security=SSPI'。 |

### <a name="section-name-failovermanager"></a>セクション名: FailoverManager
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| UserReplicaRestartWaitDuration |時間 (秒単位)、既定値は 60.0 * 30 |timespan を秒単位で指定します。 永続化されたレプリカがダウンすると、Windows Fabric はレプリカが復帰するまでこの期間待機します。この期間が過ぎると、(状態のコピーを必要とする) 新しい代替レプリカが作成されます。 |
| QuorumLossWaitDuration |時間 (秒単位)、既定値は MaxValue |timespan を秒単位で指定します。 パーティションがクォーラム損失の状態であることを許可する最長期間。 この期間を過ぎてもパーティションが引き続きクォーラム損失の状態である場合、ダウンしたレプリカは失われたと見なされ、パーティションをクォーラム損失から回復させます。 この場合、データ損失が発生する可能性があります。 |
| UserStandByReplicaKeepDuration |時間 (秒単位)、既定値は 3600.0 * 24 * 7 |timespan を秒単位で指定します。 永続化されたレプリカがダウン状態から復帰したときに、既に置き換えられている場合があります。 このタイマーは、FM がスタンバイ レプリカを破棄するまでに保持する時間を決定します。 |
| UserMaxStandByReplicaCount |int、既定値は 1 |ユーザー サービス用にシステムが保持するスタンバイ レプリカの既定の最大数。 |

### <a name="section-name-namingservice"></a>セクション名: NamingService
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| TargetReplicaSetSize |int、既定値は 7 |ネーム サービス ストアのパーティションごとのレプリカ セットの数。 レプリカ セットの数が増えると、ノードの障害によって情報が失われる可能性が低減されるため、ネーム サービス ストア内の情報の信頼性レベルが高まります。ただし、その代償として Windows Fabric の負荷が増加し、名前付けデータの更新の実行に時間がかかるようになります。|

|MinReplicaSetSize | int、既定値は 3 | 更新を完了するために書き込みを行う必要があるネーム サービスのレプリカの最小数。 システム内のアクティブなレプリカがこの数よりも少ない場合、信頼性システムはレプリカが復元されるまでネーム サービス ストアの更新を拒否します。 この値は、TargetReplicaSetSize の値を超えることはできません。 | |ReplicaRestartWaitDuration | 時間 (秒単位)、既定値は (60.0 * 30) | timespan を秒単位で指定します。ネーム サービスのレプリカがダウンすると、このタイマーが開始されます。タイマーの有効期限が切れると、FM はダウンしているレプリカの置き換えを開始します (ダウンしているレプリカは、まだ失われたとは見なされません)。 | |QuorumLossWaitDuration | 時間 (秒単位)、既定値は MaxValue | timespan を秒単位で指定します。ネーム サービスがクォーラム損失の状態になると、このタイマーが開始されます。タイマーの有効期限が切れると、FM はダウンしたレプリカを失われたと見なし、クォーラムの回復を試みます。この場合、データ損失が発生する可能性があります。 | |StandByReplicaKeepDuration | 時間 (秒単位)、既定値は 3600.0 * 2 | timespan を秒単位で指定します。 ネーム サービスのレプリカがダウン状態から復帰したときに、既に置き換えられている場合があります。  このタイマーは、FM がスタンバイ レプリカを破棄するまでに保持する時間を決定します。 | |PlacementConstraints | wstring、既定値は L"" | ネーム サービスの配置の制約。 | |ServiceDescriptionCacheLimit | int、既定値は 0 | ネーム ストア サービスの LRU サービス記述のキャッシュに保持するエントリの最大数 (無制限の場合は 0 に設定します)。 | |RepairInterval | 時間 (秒単位)、既定値は 5 | timespan を秒単位で指定します。 機関所有者と名前所有者間での名前付けの不一致の修復が開始される間隔。 | |MaxNamingServiceHealthReports | int、既定値は 10 | ネーム ストア サービスが異常として一度に報告する低速の操作の最大数。 0 の場合、低速の操作がすべて送信されます。 | | MaxMessageSize |int、既定値は 4*1024*1024 |名前付け使用時のクライアント ノードの通信の最大メッセージ サイズ。 DOS 攻撃を軽減します。既定値は 4 MB です。 | |MaxFileOperationTimeout |時間 (秒単位)、既定値は 30 |timespan を秒単位で指定します。 ファイル ストア サービス操作で許容される最大タイムアウト。 これよりも長いタイムアウトを指定した要求は拒否されます。 | |MaxOperationTimeout |時間 (秒単位)、既定値は 600 |timespan を秒単位で指定します。 クライアント操作で許容される最大タイムアウト。 これよりも長いタイムアウトを指定した要求は拒否されます。 | | MaxClientConnections |int、既定値は 1000 |ゲートウェイごとのクライアント接続の最大許容数。 | |ServiceNotificationTimeout |時間 (秒単位)、既定値は 30 |timespan を秒単位で指定します。 サービス通知をクライアントに配信するときに使用されるタイムアウト。 | | MaxOutstandingNotificationsPerClient |int、既定値は 1000 |クライアント登録がゲートウェイによって強制終了されるまでの未処理の通知の最大数。 | | MaxIndexedEmptyPartitions |int、既定値は 1000 |再接続するクライアントを同期するために、インデックスが設定された状態で通知キャッシュ内に残される空のパーティションの最大数。 空のパーティションがこの数を超えた場合、参照バージョンの昇順でインデックスから削除されます。 再接続するクライアントは引き続き同期され、欠落した空のパーティションの最新情報を受け取ることができますが、同期プロトコルのコストが高くなります。 | |GatewayServiceDescriptionCacheLimit | int、既定値は 0 | Naming Gateway の LRU サービス記述のキャッシュに保持するエントリの最大数 (無制限の場合は 0 に設定します)。 | |PartitionCount |int、既定値は 3 |作成するネーム サービス ストアのパーティションの数。 各パーティションは、インデックスに対応する&1; つのパーティション キーを所有します。そのため、パーティション キー [0; PartitionCount) が存在します。 ネーム サービスのパーティション数が増えると、バックアップ レプリカ セットで保持される平均データ量が減るため、ネーム サービスが実行できるスケールが増加します。ただし、その代償として (PartitionCount*ReplicaSetSize のサービス レプリカを保持する必要があるため) リソース使用率が増加します。

### <a name="section-name-runas"></a>セクション名: RunAs
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| RunAsAccountName |wstring、既定値は L"" |RunAs アカウントの名前を示します。 これはアカウントの種類が "DomainUser" または "ManagedServiceAccount" の場合にのみ必要です。 有効な値は、"domain\user" または "user@domain" です。 |
|RunAsAccountType|wstring、既定値は L"" |RunAs アカウントの種類を示します。 これはすべての RunAs セクションに必要です。有効な値は、"DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" です。|
|RunAsPassword|wstring、既定値は L"" |RunAs アカウントのパスワードを示します。 これはアカウントの種類が "DomainUser" の場合にのみ必要です。 |

### <a name="section-name-runasfabric"></a>セクション名: RunAs_Fabric
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| RunAsAccountName |wstring、既定値は L"" |RunAs アカウントの名前を示します。 これはアカウントの種類が "DomainUser" または "ManagedServiceAccount" の場合にのみ必要です。 有効な値は、"domain\user" または "user@domain" です。 |
|RunAsAccountType|wstring、既定値は L"" |RunAs アカウントの種類を示します。 これはすべての RunAs セクションに必要です。有効な値は、"LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" です。 |
|RunAsPassword|wstring、既定値は L"" |RunAs アカウントのパスワードを示します。 これはアカウントの種類が "DomainUser" の場合にのみ必要です。 |

### <a name="section-name-runashttpgateway"></a>セクション名: RunAs_HttpGateway
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| RunAsAccountName |wstring、既定値は L"" |RunAs アカウントの名前を示します。 これはアカウントの種類が "DomainUser" または "ManagedServiceAccount" の場合にのみ必要です。 有効な値は、"domain\user" または "user@domain" です。 |
|RunAsAccountType|wstring、既定値は L"" |RunAs アカウントの種類を示します。 これはすべての RunAs セクションに必要です。有効な値は、"LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" です。 |
|RunAsPassword|wstring、既定値は L"" |RunAs アカウントのパスワードを示します。 これはアカウントの種類が "DomainUser" の場合にのみ必要です。 |

### <a name="section-name-runasdca"></a>セクション名: RunAs_DCA
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| RunAsAccountName |wstring、既定値は L"" |RunAs アカウントの名前を示します。 これはアカウントの種類が "DomainUser" または "ManagedServiceAccount" の場合にのみ必要です。 有効な値は、"domain\user" または "user@domain" です。 |
|RunAsAccountType|wstring、既定値は L"" |RunAs アカウントの種類を示します。 これはすべての RunAs セクションに必要です。有効な値は、"LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem" です。 |
|RunAsPassword|wstring、既定値は L"" |RunAs アカウントのパスワードを示します。 これはアカウントの種類が "DomainUser" の場合にのみ必要です。 |

### <a name="section-name-httpgateway"></a>セクション名: HttpGateway
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
|IsEnabled|ブール値、既定値は false | HttpGateway を有効または無効にします。 HttpGateway は既定で無効になっています。有効にするには、この構成を設定する必要があります。 |
|ActiveListeners |uint、既定値は 50 | HTTP サーバー キューに送信する読み取りの数。 HttpGateway が対応できる同時要求の数を制御します。 |
|MaxEntityBodySize |uint、既定値は 4194304 |  HTTP 要求の予想される本文の最大サイズを指定します。 既定値は 4 MB です。 本文のサイズがこの値を超えている場合、HttpGateway は要求を失敗させます。 最小読み取りチャンク サイズは 4096 バイトです。 そのため、この値は 4096 以上にする必要があります。 |

### <a name="section-name-ktllogger"></a>セクション名: KtlLogger
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
|AutomaticMemoryConfiguration |int、既定値は 1 | メモリ設定を自動的に構成するか、動的に構成するかを示すフラグ。 0 の場合は、メモリ構成設定が使用され、システムの状態に基づいて変更されることはありません。 1 の場合は、メモリ設定が自動的に構成され、システムの状態に基づいて変更される場合があります。 |
|WriteBufferMemoryPoolMinimumInKB |int、既定値は 8388608 |書き込みバッファー メモリ プールに最初に割り当てる KB 数。 無制限であることを示すには 0 を使用します。既定値は、後述の SharedLogSizeInMB と一致する必要があります。 |
|WriteBufferMemoryPoolMaximumInKB | int、既定値は 0 |書き込みバッファー メモリ プールを拡張できる最大 KB 数。 無制限であることを示すには 0 を使用します。 |
|MaximumDestagingWriteOutstandingInKB | int、既定値は 0 | 専用ログの前に共有ログを使用できる KB 数。 無制限であることを示すには 0 を使用します。
|SharedLogPath |wstring、既定値は L"" | 共有ログ コンテナーを配置する場所のパスとファイル名。 ファブリック データ ルート下の既定のパスを使用する場合は、L"" を使用します。 |
|SharedLogId |wstring、既定値は L"" |共有ログ コンテナーの一意の GUID。 ファブリック データ ルート下の既定のパスを使用する場合は、L"" を使用します。 |
|SharedLogSizeInMB |int、既定値は 8192 | 共有ログ コンテナーに割り当てる MB 数。 |

### <a name="section-name-applicationgatewayhttp"></a>セクション名: ApplicationGateway/Http
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
|IsEnabled |ブール値、既定値は false | HttpApplicationGateway を有効または無効にします。 HttpApplicationGateway は既定で無効になっています。有効にするには、この構成を設定する必要があります。 |
|NumberOfParallelOperations | uint、既定値は 1000 | HTTP サーバー キューに送信する読み取りの数。 HttpGateway が対応できる同時要求の数を制御します。 |
|DefaultHttpRequestTimeout |時間 (秒単位)、 既定値は 60 |timespan を秒単位で指定します。  HTTP アプリケーション ゲートウェイで処理される HTTP 要求の既定の要求タイムアウトを指定します。 |
|ResolveServiceBackoffInterval |時間 (秒単位)、既定値は 5 |timespan を秒単位で指定します。  失敗したサービス解決操作を再試行するまでの既定のバックオフ間隔を指定します。 |
|BodyChunkSize |uint、既定値は 4096 |  本文の読み取りに使用するチャンクのサイズをバイト単位で指定します。 |
|GatewayAuthCredentialType |wstring、既定値は L"None" | HTTP アプリケーション ゲートウェイ エンドポイントで使用するセキュリティ資格情報の種類を示します。有効な値は "None/X509 です。 |
|GatewayX509CertificateStoreName |wstring、既定値は L"My" | HTTP アプリケーション ゲートウェイの証明書を格納する X.509 証明書ストアの名前。 |
|GatewayX509CertificateFindType |wstring、既定値は L"FindByThumbprint" | GatewayX509CertificateStoreName で指定されたストア内での証明書の検索方法を示します。サポートされる値は、FindByThumbprint と FindBySubjectName です。 |
|GatewayX509CertificateFindValue | wstring、既定値は L"" | HTTP アプリケーション ゲートウェイの証明書の検索に使用する検索フィルター値。 この証明書は HTTPS エンドポイントで構成されます。サービスで必要な場合は、この証明書を使用してアプリケーションの ID を検証することもできます。 FindValue が最初に検索され、FindValue が存在しない場合は、FindValueSecondary が検索されます。 |
|GatewayX509CertificateFindValueSecondary | wstring、既定値は L"" |HTTP アプリケーション ゲートウェイの証明書の検索に使用する検索フィルター値。 この証明書は HTTPS エンドポイントで構成されます。サービスで必要な場合は、この証明書を使用してアプリケーションの ID を検証することもできます。 FindValue が最初に検索され、FindValue が存在しない場合は、FindValueSecondary が検索されます。|

### <a name="section-name-management"></a>セクション名: Management
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| ImageStoreConnectionString |SecureString | ImageStore のルートへの接続文字列。 |
| ImageStoreMinimumTransferBPS | int、既定値は 1024 |クラスターと ImageStore 間の最小転送速度。 この値を使用して、外部 ImageStore にアクセスするときのタイムアウトを決定します。 クラスターが外部 ImageStore からダウンロードする際により多くの時間を確保できるように、クラスターと ImageStore 間の待機時間が長い場合にのみ、この値を変更します。 |
|AzureStorageMaxWorkerThreads | int、既定値は 25 | 並列 worker スレッドの最大数。 |
|AzureStorageMaxConnections | int、既定値は 5000 | Azure Storage への同時接続の最大数。 |
|AzureStorageOperationTimeout | 時間 (秒単位)、既定値は 6000 | timespan を秒単位で指定します。 xstore 操作が完了するまでのタイムアウト。 |
|ImageCachingEnabled | ブール値、既定値は true | この構成により、キャッシュを有効または無効にすることができます。 |
|DisableChecksumValidation | ブール値、既定値は false | この構成により、アプリケーションのプロビジョニング時におけるチェックサムの検証を有効または無効にすることができます。 |
|DisableServerSideCopy | ブール値、既定値は false | この構成により、アプリケーションのプロビジョニング時における、ImageStore でのアプリケーション パッケージのサーバー側のコピーを有効または無効にすることができます。 |

### <a name="section-name-healthmanagerclusterhealthpolicy"></a>セクション名: HealthManager/ClusterHealthPolicy
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| ConsiderWarningAsError |ブール値、既定値は false |クラスターの正常性評価ポリシー: 警告をエラーとして処理します。 |
| MaxPercentUnhealthyNodes | int、既定値は 0 |クラスターの正常性評価ポリシー: 異常なノードの最大許容パーセンテージ。この値を超えると、クラスターは異常と見なされます。 |
| MaxPercentUnhealthyApplications | int、既定値は 0 |クラスターの正常性評価ポリシー: 異常なアプリケーションの最大許容パーセンテージ。この値を超えると、クラスターは異常と見なされます。 |
|MaxPercentDeltaUnhealthyNodes | int、既定値は 10 |クラスターのアップグレードの正常性評価ポリシー: 異常な差分ノードの最大許容パーセンテージ。この値を超えると、クラスターは異常と見なされます。 |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes | int、既定値は 15 |クラスターのアップグレードの正常性評価ポリシー: アップグレード ドメインの異常な差分ノードの最大許容パーセンテージ。この値を超えると、クラスターは異常と見なされます。|

### <a name="section-name-faultanalysisservice"></a>セクション名: FaultAnalysisService
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| TargetReplicaSetSize |int、既定値は 0 |NOT_PLATFORM_UNIX_START FaultAnalysisService の TargetReplicaSetSize。 |
| MinReplicaSetSize |int、既定値は 0 |FaultAnalysisService の MinReplicaSetSize。 |
| ReplicaRestartWaitDuration |時間 (秒単位)、既定値は 60 分|timespan を秒単位で指定します。 FaultAnalysisService の ReplicaRestartWaitDuration。 |
| QuorumLossWaitDuration | 時間 (秒単位)、既定値は MaxValue |timespan を秒単位で指定します。 FaultAnalysisService の QuorumLossWaitDuration。 |
| StandByReplicaKeepDuration| 時間 (秒単位)、既定値は (60*24*7) 分 |timespan を秒単位で指定します。 FaultAnalysisService の StandByReplicaKeepDuration。 |
| PlacementConstraints | wstring、既定値は L""| FaultAnalysisService の PlacementConstraints。 |
| StoredActionCleanupIntervalInSeconds | int、既定値は 3600 |ストアをクリーンアップする頻度。  終了状態のアクションと、少なくとも CompletedActionKeepDurationInSeconds で指定された期間よりも前に完了したアクションだけが削除されます。 |
| CompletedActionKeepDurationInSeconds | int、既定値は 604800 | 終了状態のアクションを保持するおおよその期間。  クリーンアップの処理は StoredActionCleanupIntervalInSeconds で指定された間隔でのみ実行されるため、このパラメーターは StoredActionCleanupIntervalInSeconds にも左右されます。 604800 は 7 日間です。 |
| StoredChaosEventCleanupIntervalInSeconds | int、既定値は 3600 |クリーンアップのためにストアを監査する頻度。イベント数が 30000 を超えると、クリーンアップが開始されます。 |

### <a name="section-name-filestoreservice"></a>セクション名: FileStoreService
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| NamingOperationTimeout |秒単位。既定値は 60 |timespan を秒単位で指定します。 名前付け操作を実行する際のタイムアウト。 |
| QueryOperationTimeout | 秒単位。既定値は 60 |timespan を秒単位で指定します。 クエリ操作を実行する際のタイムアウト。 |
| MaxCopyOperationThreads | uint、既定値は 0 | セカンダリがプライマリからコピーできる並列ファイルの最大数。 '0' == コア数です。 |
| MaxFileOperationThreads | uint、既定値は 100 | プライマリで FileOperations (コピー/移動) を実行する際に使用できる並列スレッドの最大数。 '0' == コア数です。 |
| MaxStoreOperations | uint、既定値は 4096 |プライマリで実行できる並列ストア トランザクション操作の最大数。 '0' == コア数です。 |
| MaxRequestProcessingThreads | uint、既定値は 200 |プライマリで要求を処理する際に使用できる並列スレッドの最大数。 '0' == コア数です。 |
| MaxSecondaryFileCopyFailureThreshold | uint、既定値は 25| セカンダリでのファイル コピーの再試行の最大回数。この回数に達すると、再試行が停止されます。 |
| AnonymousAccessEnabled | ブール値、既定値は true |FileStoreService 共有への匿名アクセスを有効または無効にします。 |
| PrimaryAccountType | wstring、既定値は L"" |FileStoreService 共有の ACL の、プリンシパルのプライマリ AccountType。 |
| PrimaryAccountUserName | wstring、既定値は L"" |FileStoreService 共有の ACL の、プリンシパルのプライマリ アカウントの Username。 |
| PrimaryAccountUserPassword | SecureString、既定値は空 |FileStoreService 共有の ACL の、プリンシパルのプライマリ アカウントのパスワード。 |
| FileStoreService | PrimaryAccountNTLMPasswordSecret | SecureString、既定値は空 | NTLM 認証を使用するときに、生成された同じパスワードに対するシードとして使用するパスワード シークレット。 |
| PrimaryAccountNTLMX509StoreLocation | wstring、既定値は L"LocalMachine"| NTLM 認証を使用するときに、PrimaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの場所。 |
| PrimaryAccountNTLMX509StoreName | wstring、既定値は L"MY"| NTLM 認証を使用するときに、PrimaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの名前。 |
| PrimaryAccountNTLMX509Thumbprint | wstring、既定値は L""|NTLM 認証を使用するときに、PrimaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書の拇印。 |
| SecondaryAccountType | wstring、既定値は L""| FileStoreService 共有の ACL の、プリンシパルのセカンダリ AccountType。 |
| SecondaryAccountUserName | wstring、既定値は L""| FileStoreService 共有の ACL の、プリンシパルのセカンダリ アカウントの Username。 |
| SecondaryAccountUserPassword | SecureString、既定値は空 |FileStoreService 共有の ACL の、プリンシパルのセカンダリ アカウントのパスワード。  |
| SecondaryAccountNTLMPasswordSecret | SecureString、既定値は空 | NTLM 認証を使用するときに、生成された同じパスワードに対するシードとして使用するパスワード シークレット。 |
| SecondaryAccountNTLMX509StoreLocation | wstring、既定値は L"LocalMachine" |NTLM 認証を使用するときに、SecondaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの場所。 |
| SecondaryAccountNTLMX509StoreName | wstring、既定値は L"MY" |NTLM 認証を使用するときに、SecondaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書のストアの名前。 |
| SecondaryAccountNTLMX509Thumbprint | wstring、既定値は L""| NTLM 認証を使用するときに、SecondaryAccountNTLMPasswordSecret で HMAC を生成するために使用する X509 証明書の拇印。 |

### <a name="section-name-imagestoreservice"></a>セクション名: ImageStoreService
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| 有効 |ブール値、既定値は false |ImageStoreService の Enabled フラグ。 |
| TargetReplicaSetSize | int、既定値は 7 |ImageStoreService の TargetReplicaSetSize。 |
| MinReplicaSetSize | int、既定値は 3 |ImageStoreService の MinReplicaSetSize。 |
| ReplicaRestartWaitDuration | 時間 (秒単位)、既定値は 60.0 * 30 | timespan を秒単位で指定します。 ImageStoreService の ReplicaRestartWaitDuration。 |
| QuorumLossWaitDuration | 時間 (秒単位)、既定値は MaxValue | timespan を秒単位で指定します。 ImageStoreService の QuorumLossWaitDuration。 |
| StandByReplicaKeepDuration | 時間 (秒単位)、既定値は 3600.0 * 2 | timespan を秒単位で指定します。 ImageStoreService の StandByReplicaKeepDuration。 |
| PlacementConstraints | wstring、既定値は L"" | ImageStoreService の PlacementConstraints。 |
| ClientUploadTimeout | 時間 (秒単位)、既定値は 1800 |timespan を秒単位で指定します。 イメージ ストア サービスに対するトップレベルのアップロード要求のタイムアウト値。 |
| ClientCopyTimeout | 時間 (秒単位)、既定値は 1800 | timespan を秒単位で指定します。 イメージ ストア サービスに対するトップレベルのコピー要求のタイムアウト値。 |
| ClientDownloadTimeout | 時間 (秒単位)、既定値は 1800 | timespan を秒単位で指定します。 イメージ ストア サービスに対するトップレベルのダウンロード要求のタイムアウト値。 |
| ClientListTimeout | 時間 (秒単位)、既定値は 600 | timespan を秒単位で指定します。 イメージ ストア サービスに対するトップレベルのリスト要求のタイムアウト値。 |
| ClientDefaultTimeout | 時間 (秒単位)、既定値は 180 | timespan を秒単位で指定します。 イメージ ストア サービスに対するアップロード/ダウンロード要求以外のすべての要求 (存在確認、削除など) のタイムアウト値。 |

### <a name="section-name-imagestoreclient"></a>セクション名: ImageStoreClient
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| ClientUploadTimeout |時間 (秒単位)、既定値は 1800 | timespan を秒単位で指定します。 イメージ ストア サービスに対するトップレベルのアップロード要求のタイムアウト値。 |
| ClientCopyTimeout | 時間 (秒単位)、既定値は 1800 | timespan を秒単位で指定します。 イメージ ストア サービスに対するトップレベルのコピー要求のタイムアウト値。 |
|ClientDownloadTimeout | 時間 (秒単位)、既定値は 1800 | timespan を秒単位で指定します。 イメージ ストア サービスに対するトップレベルのダウンロード要求のタイムアウト値。 |
|ClientListTimeout | 時間 (秒単位)、既定値は 600 |timespan を秒単位で指定します。 イメージ ストア サービスに対するトップレベルのリスト要求のタイムアウト値。 |
|ClientDefaultTimeout | 時間 (秒単位)、既定値は 180 | timespan を秒単位で指定します。 イメージ ストア サービスに対するアップロード/ダウンロード要求以外のすべての要求 (存在確認、削除など) のタイムアウト値。 |

### <a name="section-name-tokenvalidationservice"></a>セクション名: TokenValidationService
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| プロバイダー |wstring、既定値は L"DSTS" |有効にするトークン検証プロバイダーのコンマ区切りリスト (有効なプロバイダー: DSTS、AAD)。 現時点では、1 つのプロバイダーだけをいつでも有効にすることができます。 |

### <a name="section-name-dststokenvalidationservice"></a>セクション名: DSTSTokenValidationService
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| DSTSDnsName | wstring、既定値は L"" | DSTS サーバーの DNS 名。 | | DSTSRealm | wstring、既定値は L"" | DSTS サーバーの領域名。 | | CloudServiceDnsName | wstring、既定値は L"" | DSTS セキュリティ トークンが要求されるクラウド サービスの DNS 名。 | | CloudServiceName | wstring、既定値は L"" | DSTS セキュリティ トークンが要求されるクラウド サービスの名前。 | | PublicCertificateFindValue | wstring、既定値は L"" | DSTS 公開証明書の X509 証明書の検索値。 | | PublicCertificateFindType | wstring、既定値は L"" | DSTS 公開証明書の X509 証明書の検索の種類 (例:  FindByThumbprint)。 | |PublicCertificateStoreName | wstring、既定値は L"My"| DSTS サーバーの公開証明書が格納されているストアの名前。 |

### <a name="section-name-upgradeorchestrationservice"></a>セクション名: UpgradeOrchestrationService
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| TargetReplicaSetSize |int、既定値は 0 |UpgradeOrchestrationService の TargetReplicaSetSize。 |
| MinReplicaSetSize |int、既定値は 0 | UpgradeOrchestrationService の MinReplicaSetSize。
| ReplicaRestartWaitDuration | 時間 (秒単位)、既定値は 60 分| timespan を秒単位で指定します。 UpgradeOrchestrationService の ReplicaRestartWaitDuration。 |
| QuorumLossWaitDuration | 時間 (秒単位)、既定値は MaxValue | timespan を秒単位で指定します。 UpgradeOrchestrationService の QuorumLossWaitDuration。 |
| StandByReplicaKeepDuration | 時間 (秒単位)、既定値は 60*24*7 分 | timespan を秒単位で指定します。 UpgradeOrchestrationService の StandByReplicaKeepDuration。 |
| PlacementConstraints | wstring、既定値は L"" | UpgradeOrchestrationService の PlacementConstraints。 |
| AutoupgradeEnabled | ブール値、既定値は true | 目標状態ファイルに基づく自動ポーリングとアップグレード アクション。 |
| UpgradeApprovalRequired | ブール値、既定値は false | コードのアップグレードを続行する前に管理者の承認を必須にするための設定。 |

### <a name="section-name-upgradeservice"></a>セクション名: UpgradeService
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| PlacementConstraints |wstring、既定値は L"" |アップグレード サービスの PlacementConstraints。 |
| TargetReplicaSetSize | int、既定値は 3 | UpgradeService の TargetReplicaSetSize。 |
| MinReplicaSetSize | int、既定値は 2 | UpgradeService の MinReplicaSetSize。 |
| CoordinatorType | wstring、既定値は L"WUTest"| UpgradeService の CoordinatorType。 |
| BaseUrl | wstring、既定値は L"" |UpgradeService の BaseUrl。 |
| ClusterId | wstring、既定値は L"" | UpgradeService の ClusterId。 |
| X509StoreName | wstring、既定値は L"My"| UpgradeService の X509StoreName。 |
| X509StoreLocation | wstring、既定値は L"" | UpgradeService の X509StoreLocation。 |
| X509FindType | wstring、既定値は L""| UpgradeService の X509FindType。 |
| X509FindValue | wstring、既定値は L"" | UpgradeService の X509FindValue。 |
| X509SecondaryFindValue | wstring、既定値は L"" | UpgradeService の X509SecondaryFindValue。 |
| OnlyBaseUpgrade | ブール値、既定値は false | UpgradeService の OnlyBaseUpgrade。 |
| TestCabFolder | wstring、既定値は L"" | UpgradeService の TestCabFolder。 |

### <a name="section-name-securityclientaccess"></a>セクション名: Security/ClientAccess
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| CreateName |wstring、既定値は L"Admin" |名前付け URI を作成するためのセキュリティ構成。 |
| DeleteName |wstring、既定値は L"Admin" |名前付け URI を削除するためのセキュリティ構成。 |
| PropertyWriteBatch |wstring、既定値は L"Admin" |名前付けプロパティの書き込み操作のセキュリティ構成。 |
| CreateService |wstring、既定値は L"Admin" | サービスを作成するためのセキュリティ構成。 |
| CreateServiceFromTemplate |wstring、既定値は L"Admin" |テンプレートからサービスを作成するためのセキュリティ構成。 |
| UpdateService |wstring、既定値は L"Admin" |サービス更新のセキュリティ構成。 |
| DeleteService  |wstring、既定値は L"Admin" |サービスを削除するためのセキュリティ構成。 |
| ProvisionApplicationType |wstring、既定値は L"Admin" | アプリケーションの種類をプロビジョニングするためのセキュリティ構成。 |
| CreateApplication |wstring、既定値は L"Admin" | アプリケーションを作成するためのセキュリティ構成。 |
| DeleteApplication |wstring、既定値は L"Admin" | アプリケーションを削除するためのセキュリティ構成。 |
| UpgradeApplication |wstring、既定値は L"Admin" | アプリケーションのアップグレードを開始または中断するためのセキュリティ構成。 |
| RollbackApplicationUpgrade |wstring、既定値は L"Admin" | アプリケーションのアップグレードをロールバックするためのセキュリティ構成。 |
| UnprovisionApplicationType |wstring、既定値は L"Admin" | アプリケーションの種類のプロビジョニングを解除するためのセキュリティ構成。 |
| MoveNextUpgradeDomain |wstring、既定値は L"Admin" | 明示的なアップグレード ドメインでアプリケーションのアップグレードを再開するためのセキュリティ構成。 |
| ReportUpgradeHealth |wstring、既定値は L"Admin" | 現在のアップグレードの進行状況でアプリケーションのアップグレードを再開するためのセキュリティ構成。 |
| ReportHealth |wstring、既定値は L"Admin" | 正常性を報告するためのセキュリティ構成。 |
| ProvisionFabric |wstring、既定値は L"Admin" | MSI またはクラスター マニフェストをプロビジョニングするためのセキュリティ構成。 |
| UpgradeFabric |wstring、既定値は L"Admin" | クラスターのアップグレードを開始するためのセキュリティ構成。 |
| RollbackFabricUpgrade |wstring、既定値は L"Admin" | クラスターのアップグレードをロールバックするためのセキュリティ構成。 |
| UnprovisionFabric |wstring、既定値は L"Admin" | MSI またはクラスター マニフェストのプロビジョニングを解除するためのセキュリティ構成。 |
| MoveNextFabricUpgradeDomain |wstring、既定値は L"Admin" | 明示的なアップグレード ドメインでクラスターのアップグレードを再開するためのセキュリティ構成。 |
| ReportFabricUpgradeHealth |wstring、既定値は L"Admin" | 現在のアップグレードの進行状況でクラスターのアップグレードを再開するためのセキュリティ構成。 |
| StartInfrastructureTask |wstring、既定値は L"Admin" | インフラストラクチャ タスクを開始するためのセキュリティ構成。 |
| FinishInfrastructureTask |wstring、既定値は L"Admin" | インフラストラクチャ タスクを終了するためのセキュリティ構成。 |
| ActivateNode |wstring、既定値は L"Admin" | ノードをアクティブ化するためのセキュリティ構成。 |
| DeactivateNode |wstring、既定値は L"Admin" | ノードを非アクティブ化するためのセキュリティ構成。 |
| DeactivateNodesBatch |wstring、既定値は L"Admin" | 複数のノードを非アクティブ化するためのセキュリティ構成。 |
| RemoveNodeDeactivations |wstring、既定値は L"Admin" | 複数のノードの非アクティブ化を取り消すためのセキュリティ構成。 |
| GetNodeDeactivationStatus |wstring、既定値は L"Admin" | 非アクティブ化の状態を確認するためのセキュリティ構成。 |
| NodeStateRemoved |wstring、既定値は L"Admin" | 削除されたノードの状態を報告するためのセキュリティ構成。 |
| RecoverPartition |wstring、既定値は L"Admin" | パーティションを復旧するためのセキュリティ構成。 |
| RecoverPartitions |wstring、既定値は L"Admin" | 複数のパーティションを復旧するためのセキュリティ構成。 |
| RecoverServicePartitions |wstring、既定値は L"Admin" | サービス パーティションを復旧するためのセキュリティ構成。 |
| RecoverSystemPartitions |wstring、既定値は L"Admin" | システム サービス パーティションを復旧するためのセキュリティ構成。 |
| ReportFault |wstring、既定値は L"Admin" | 障害を報告するためのセキュリティ構成。 |
| InvokeInfrastructureCommand |wstring、既定値は L"Admin" | インフラストラクチャ タスクの管理コマンドのセキュリティ構成。 |
| FileContent |wstring、既定値は L"Admin" | イメージ ストア クライアント ファイルの転送 (クラスターの外部) のセキュリティ構成。 |
| FileDownload |wstring、既定値は L"Admin" | イメージ ストア クライアント ファイルのダウンロードの開始 (クラスターの外部) のセキュリティ構成。 |
| InternalList |wstring、既定値は L"Admin" | イメージ ストア クライアント ファイルのリスト操作 (内部) のセキュリティ構成。 |
| 削除 |wstring、既定値は L"Admin" | イメージ ストア クライアントの削除操作のセキュリティ構成。 |
| アップロード |wstring、既定値は L"Admin" | イメージ ストア クライアントのアップロード操作のセキュリティ構成。 |
| GetStagingLocation |wstring、既定値は L"Admin" | イメージ ストア クライアントのステージングの場所を取得するためのセキュリティ構成。 |
| GetStoreLocation |wstring、既定値は L"Admin" | イメージ ストア クライアントのストアの場所を取得するためのセキュリティ構成。 |
| NodeControl |wstring、既定値は L"Admin" | ノードを起動、停止、再起動するためのセキュリティ構成。 |
| CodePackageControl |wstring、既定値は L"Admin" | コード パッケージを再開するためのセキュリティ構成。 |
| UnreliableTransportControl |wstring、既定値は L"Admin" | 追加および削除動作の信頼性のないトランスポート。 |
| MoveReplicaControl |wstring、既定値は L"Admin" | レプリカを移動します。 |
| PredeployPackageToNode |wstring、既定値は L"Admin" | デプロイメント前の API。 |
| StartPartitionDataLoss |wstring、既定値は L"Admin" | パーティションでデータ損失を誘発します。 |
| StartPartitionQuorumLoss |wstring、既定値は L"Admin" | パーティションでクォーラム損失を誘発します。 |
| StartPartitionRestart |wstring、既定値は L"Admin" | パーティションのレプリカの一部またはすべてを同時に再起動します。 |
| CancelTestCommand |wstring、既定値は L"Admin" | 実行中の特定の TestCommand を取り消します。 |
| StartChaos |wstring、既定値は L"Admin" | 混乱を開始します (まだ開始されていない場合)。 |
| StopChaos |wstring、既定値は L"Admin" | 混乱を停止します (開始されている場合)。 |
| StartNodeTransition |wstring、既定値は L"Admin" | ノードの切り替えを開始するためのセキュリティ構成。 |
| StartClusterConfigurationUpgrade |wstring、既定値は L"Admin" | パーティションで StartClusterConfigurationUpgrade を誘発します。 |
| GetUpgradesPendingApproval |wstring、既定値は L"Admin" | パーティションで GetUpgradesPendingApproval を誘発します。 |
| StartApprovedUpgrades |wstring、既定値は L"Admin" | パーティションで StartApprovedUpgrades を誘発します。 |
| ping |wstring、既定値は L"Admin\|\|User" | クライアントの ping のセキュリティ構成。 |
| クエリ |wstring、既定値は L"Admin\|\|User" | クエリのセキュリティ構成。 |
| NameExists |wstring、既定値は L"Admin\|\|User" | 名前付け URI の存在確認のセキュリティ構成。 |
| EnumerateSubnames |wstring、既定値は L"Admin\|\|User" | 名前付け URI を列挙するためのセキュリティ構成。 |
| EnumerateProperties |wstring、既定値は L"Admin\|\|User" | 名前付けプロパティを列挙するためのセキュリティ構成。 |
| PropertyReadBatch |wstring、既定値は L"Admin\|\|User" | 名前付けプロパティの読み取り操作のセキュリティ構成。 |
| GetServiceDescription |wstring、既定値は L"Admin\|\|User" | 長いポーリングのサービス通知と読み取りサービスの説明のセキュリティ構成。 |
| ResolveService |wstring、既定値は L"Admin\|\|User" | クレーム ベースのサービス解決のセキュリティ構成。 |
| ResolveNameOwner |wstring、既定値は L"Admin\|\|User" | 名前付け URI の所有者を解決するためのセキュリティ構成。 |
| ResolvePartition |wstring、既定値は L"Admin\|\|User" | システム サービスを解決するためのセキュリティ構成。 |
| ServiceNotifications |wstring、既定値は L"Admin\|\|User" | イベント ベースのサービス通知のセキュリティ構成。 |
| PrefixResolveService |wstring、既定値は L"Admin\|\|User" | クレーム ベースのサービス プレフィックス解決のセキュリティ構成。 |
| GetUpgradeStatus |wstring、既定値は L"Admin\|\|User" | アプリケーションのアップグレード状態をポーリングするためのセキュリティ構成。 |
| GetFabricUpgradeStatus |wstring、既定値は L"Admin\|\|User" | クラスターのアップグレード状態をポーリングするためのセキュリティ構成。 |
| InvokeInfrastructureQuery |wstring、既定値は L"Admin\|\|User" | インフラストラクチャ タスクを照会するためのセキュリティ構成。 |
| 一覧表示 |wstring、既定値は L"Admin\|\|User" | イメージ ストア クライアント ファイルのリスト操作のセキュリティ構成。 |
| ResetPartitionLoad |wstring、既定値は L"Admin\|\|User" | failoverUnit の負荷をリセットするためのセキュリティ構成。 |
| ToggleVerboseServicePlacementHealthReporting | wstring、既定値は L"Admin\|\|User" | 詳細な ServicePlacement HealthReporting を切り替えるためのセキュリティ構成。 |
| GetPartitionDataLossProgress | wstring、既定値は L"Admin\|\|User" | データ損失を発生させる API 呼び出しの進行状況を取得します。 |
| GetPartitionQuorumLossProgress | wstring、既定値は L"Admin\|\|User" | クォーラム損失を発生させる API 呼び出しの進行状況を取得します。 |
| GetPartitionRestartProgress | wstring、既定値は L"Admin\|\|User" | パーティションを再起動する API 呼び出しの進行状況を取得します。 |
| GetChaosReport | wstring、既定値は L"Admin\|\|User" | 指定された時間範囲内の混乱の状態を取得します。 |
| GetNodeTransitionProgress | wstring、既定値は L"Admin\|\|User" | ノード切り替えコマンドの進行状況を取得するためのセキュリティ構成。 |
| GetClusterConfigurationUpgradeStatus | wstring、既定値は L"Admin\|\|User" | パーティションで GetClusterConfigurationUpgradeStatus を誘発します。 |
| GetClusterConfiguration | wstring、既定値は L"Admin\|\|User" | パーティションで GetClusterConfiguration を誘発します。 |

### <a name="section-name-reconfigurationagent"></a>セクション名: ReconfigurationAgent
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | 時間 (秒単位)、既定値は 900 |timespan を秒単位で指定します。 レプリカが終了途中で停止しているサービス ホストを終了するまでのシステムの待機期間。 |
| ServiceApiHealthDuration | 時間 (秒単位)、既定値は 30 分 | timespan を秒単位で指定します。 ServiceApiHealthDuration は、開発者がサービス API を異常と報告するまでにサービス API の実行を待機する時間を定義します。 |
| ServiceReconfigurationApiHealthDuration | 秒単位。既定値は 30 | timespan を秒単位で指定します。 ServiceReconfigurationApiHealthDuration は、再構成中のサービスが異常と報告されるまでの時間を定義します。 |
| PeriodicApiSlowTraceInterval | 時間 (秒単位)、既定値は 5 分 | timespan を秒単位で指定します。 PeriodicApiSlowTraceInterval は、時間がかかる API 呼び出しを API モニターで再トレースする間隔を定義します。 |
| NodeDeactivationMaxReplicaCloseDuration | 時間 (秒単位)、既定値は 900 | timespan を秒単位で指定します。 ノードの非アクティブ化をブロックしているサービス ホストを終了するまでの最大待機時間。 |
| FabricUpgradeMaxReplicaCloseDuration | 時間 (秒単位)、既定値は 900 | timespan を秒単位で指定します。 終了しないままになっているレプリカのサービス ホストを終了するまでの RA の最大待機期間。 |
| IsDeactivationInfoEnabled | ブール値、既定値は true | RA がプライマリの再選出を行うために非アクティブ化情報を使用するかどうかを指定します。新しいクラスターの場合、この構成を true に設定する必要があります。アップグレード対象となる既存のクラスターの場合、これを有効にする方法については、リリース ノートをご覧ください。 |

### <a name="section-name-placementandloadbalancing"></a>セクション名: PlacementAndLoadBalancing
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| TraceCRMReasons |ブール値、既定値は true |CRM が発行した、操作イベント チャネルへの移動の理由をトレースするかどうかを指定します。 |
| ValidatePlacementConstraint | ブール値、既定値は true | サービスの ServiceDescription が更新されたときに、サービスの PlacementConstraint 式を検証するかどうかを指定します。 |
| PlacementConstraintValidationCacheSize | int、既定値は 10000 | 配置制約式の簡易検証とキャッシュに使用するテーブルのサイズを制限します。 |
|VerboseHealthReportLimit | int、既定値は 20 | レプリカが未配置の状態になった回数がここで定義した回数に達すると、正常性の警告が報告されます (詳細な正常性レポートが有効になっている場合)。 |
|ConstraintViolationHealthReportLimit | int、既定値は 50 | 制約に違反しているレプリカが永続的に未修正の状態になった回数がここで定義した回数に達すると、診断が行われ、正常性レポートが出力されます。 |
|DetailedConstraintViolationHealthReportLimit | int、既定値は 200 | 制約に違反しているレプリカが永続的に未修正の状態になった回数がここで定義した回数に達すると、診断が行われ、詳細な正常性レポートが出力されます。 |
|DetailedVerboseHealthReportLimit | int、既定値は 200 | 未配置レプリカが永続的に未配置の状態になった回数がここで定義した回数に達すると、詳細な正常性レポートが出力されます。 |
|ConsecutiveDroppedMovementsHealthReportLimit | int、既定値は 20 | ResourceBalancer が発行した移動が連続して破棄された回数がここで定義した連続回数に達すると、診断が行われ、正常性の警告が出力されます。 負の値: この条件下では警告は出力されません。 |
|DetailedNodeListLimit | int、既定値は 15 | 未配置レプリカ レポートでの切り捨て前に含める、制約ごとのノードの数を定義します。 |
|DetailedPartitionListLimit | int、既定値は 15 | 診断での切り捨て前に含める、制約の診断エントリごとのパーティションの数を定義します。 |
|DetailedDiagnosticsInfoListLimit | int、既定値は 15 | 診断での切り捨て前に含める、制約ごとの (詳細情報を含む) 診断エントリの数を定義します。|
|PLBRefreshGap | 時間 (秒単位)、既定値は 1 | timespan を秒単位で指定します。 PLB が状態を再度更新するまでに必要な最小経過時間を定義します。 |
|MinPlacementInterval | 時間 (秒単位)、既定値は 1 | timespan を秒単位で指定します。 2 つの連続する配置ラウンドまでに必要な最小経過時間を定義します。 |
|MinConstraintCheckInterval | 時間 (秒単位)、既定値は 1 | timespan を秒単位で指定します。 2 つの連続する制約チェック ラウンドまでに必要な最小経過時間を定義します。 |
|MinLoadBalancingInterval | 時間 (秒単位)、既定値は 5 | timespan を秒単位で指定します。 2 つの連続する均衡化ラウンドまでに必要な最小経過時間を定義します。 |
|BalancingDelayAfterNodeDown | 時間 (秒単位)、既定値は 120 |timespan を秒単位で指定します。 ノード ダウン イベント後、この期間内に均衡化アクティビティを開始しないでください。 |
|BalancingDelayAfterNewNode | 時間 (秒単位)、既定値は 120 |timespan を秒単位で指定します。 新しいノードの追加後、この期間内に均衡化アクティビティを開始しないでください。 |
|ConstraintFixPartialDelayAfterNodeDown | 時間 (秒単位)、既定値は 120 | timespan を秒単位で指定します。 ノード ダウン イベント後、この期間内に FaultDomain および UpgradeDomain の制約違反を修正しないでください。 |
|ConstraintFixPartialDelayAfterNewNode | 時間 (秒単位)、既定値は 120 | timespan を秒単位で指定します。 新しいノードの追加後、この期間内に FaultDomain および UpgradeDomain の制約違反を修正しないでください。 |
|GlobalMovementThrottleThreshold | uint、既定値は 1000 | 均衡化フェーズにおいて、GlobalMovementThrottleCountingInterval で示される過去の間隔での移動の最大許容数。 |
|GlobalMovementThrottleThresholdForPlacement | uint、既定値は 0 | 配置フェーズにおいて、GlobalMovementThrottleCountingInterval で示される過去の間隔での移動の最大許容数。0 は無制限であることを示します。|
|GlobalMovementThrottleThresholdForBalancing | uint、既定値は 0 | 均衡化フェーズにおいて、GlobalMovementThrottleCountingInterval で示される過去の間隔での移動の最大許容数。 0 は無制限であることを示します。 |
|GlobalMovementThrottleCountingInterval | 時間 (秒単位)、既定値は 600 | timespan を秒単位で指定します。 ドメイン レプリカの移動ごとに追跡する、過去の間隔の長さを示します (GlobalMovementThrottleThreshold と共に使用)。 0 に設定すると、グローバルな調整を完全に無視できます。 |
|MovementPerPartitionThrottleThreshold | uint、既定値は 50 | パーティションのレプリカの均衡化に関連する移動数が、MovementPerPartitionThrottleCountingInterval で示されている過去の間隔で MovementPerFailoverUnitThrottleThreshold の値に達するか、この値を超えると、そのパーティションで均衡化に関連する移動は発生しなくなります。 |
|MovementPerPartitionThrottleCountingInterval | 時間 (秒単位)、既定値は 600 | timespan を秒単位で指定します。 各パーティションのレプリカの移動を追跡する、過去の間隔の長さを示します (MovementPerPartitionThrottleThreshold と共に使用)。 |
|PlacementSearchTimeout | 時間 (秒単位)、既定値は 0.5 | timespan を秒単位で指定します。 サービスを配置するときに、結果を返すまでに最長でこの時間の間、検索します。 |
|UseMoveCostReports | ブール値、既定値は false | よりバランスの取れた配置を実現するために多数の移動が発生する可能性のある、スコア付け関数のコスト要素を無視するよう LB に指示します。 |
|PreventTransientOvercommit | ブール値、既定値は false | 開始された移動によって解放されるリソースを PLB が即座に利用するかどうかを指定します。 既定では、PLB は同じノード上で移動を開始できるので、一時的なオーバーコミットが発生する可能性があります。 このパラメーターを true に設定すると、このようなオーバーコミットを防ぐことができ、オンデマンドのデフラグ (placementWithMove) が無効になります。 |
|InBuildThrottlingEnabled | ブール値、既定値は false | 組み込みの調整を有効にするかどうかを指定します。 |
|InBuildThrottlingAssociatedMetric | wstring、既定値は L"" | この調整に関連付けられたメトリックの名前。 |
|InBuildThrottlingGlobalMaxValue | int、既定値は 0 |グローバルに使用できる組み込みレプリカの最大数。 |
|SwapPrimaryThrottlingEnabled | ブール値、既定値は false| スワップ プライマリ調整を有効にするかどうかを指定します。 |
|SwapPrimaryThrottlingAssociatedMetric | wstring、既定値は L""| この調整に関連付けられたメトリックの名前。 |
|SwapPrimaryThrottlingGlobalMaxValue | int、既定値は 0 | グローバルに使用できるスワップ プライマリ レプリカの最大数。 |
|PlacementConstraintPriority | int、既定値は 0 | 配置の制約の優先順位 (0: ハード、1: ソフト、負の値: 無視) を指定します。 |
|PreferredLocationConstraintPriority | int、既定値は 2| 優先される場所の制約の優先順位 (0: ハード、1: ソフト、2: 最適化、負の値: 無視) を指定します。 |
|CapacityConstraintPriority | int、既定値は 0 | 容量の制約の優先順位 (0: ハード、1: ソフト、負の値: 無視) を指定します。 |
|AffinityConstraintPriority | int、既定値は 0 | アフィニティの制約の優先順位 (0: ハード、1: ソフト、負の値: 無視) を指定します。 |
|FaultDomainConstraintPriority | int、既定値は 0 | 障害ドメインの制約の優先順位 (0: ハード、1: ソフト、負の値: 無視) を指定します。 |
|UpgradeDomainConstraintPriority | int、既定値は 1| アップグレード ドメインの制約の優先順位 (0: ハード、1: ソフト、負の値: 無視) を指定します。 |
|ScaleoutCountConstraintPriority | int、既定値は 0 | スケールアウト数の制約の優先順位 (0: ハード、1: ソフト、負の値: 無視) を指定します。 |
|ApplicationCapacityConstraintPriority | int、既定値は 0 | 容量の制約の優先順位 (0: ハード、1: ソフト、負の値: 無視) を指定します。 |
|MoveParentToFixAffinityViolation | ブール値、既定値は false | アフィニティの制約を修正するために親レプリカを移動できるかどうかを指定します。|
|MoveExistingReplicaForPlacement | ブール値、既定値は true |配置中に既存のレプリカを移動するかどうかを指定します。 |
|UseSeparateSecondaryLoad | ブール値、既定値は true | 別のセカンダリ負荷を使用するかどうかを指定します。 |
|PlaceChildWithoutParent | ブール値、既定値は true | 親レプリカが稼働していない場合に、子サービス レプリカを配置できるかどうかを指定します。 |
|PartiallyPlaceServices | ブール値、既定値は true | サービス レプリカに適したノードの数が限られている場合に、クラスター内のすべてのサービス レプリカを "全部かゼロか" 方式で配置するかどうかを指定します。|
|InterruptBalancingForAllFailoverUnitUpdates | ブール値、既定値は false | 各種フェールオーバー ユニットの更新時に、高速または低速の均衡化の実行を中断させる必要があるかどうかを指定します。 "false" を指定すると、FailoverUnit が作成または削除された場合、FailoverUnit でレプリカが不足している場合、プライマリ レプリカの場所が変更された場合、またはレプリカの数が変更された場合に、均衡化の実行は中断されます。 それ以外の場合は、均衡化の実行が中断されることはありません。たとえば、FailoverUnit に追加のレプリカがある場合、レプリカ フラグが変更された場合、パーティションのバージョンだけが変更された場合などです。 |

### <a name="section-name-security"></a>セクション名: セキュリティ
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| ClusterProtectionLevel |None または EncryptAndSign |セキュリティで保護されていないクラスターの場合は None (既定)、セキュリティで保護されたクラスターの場合は EncryptAndSign です。 |

### <a name="section-name-hosting"></a>セクション名: Hosting
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| ServiceTypeRegistrationTimeout |秒単位。既定値は 300 |Fabric に登録する ServiceType の最大許容時間 |
| ServiceTypeDisableFailureThreshold |整数。既定値は 1 |失敗回数のしきい値。この回数を失敗した後、そのノード上のサービスの種類を無効にして、別のノードへの配置を試行するよう FailoverManager (FM) に通知されます。 |
| ActivationRetryBackoffInterval |秒単位。既定値は 5 |アクティブ化失敗のバックオフ間隔。アクティブ化が連続して失敗するたびに、MaxActivationFailureCount で指定された回数までアクティブ化が再試行されます。 各試行の再試行間隔は、継続的なアクティブ化失敗とアクティブ化バックオフ間隔の積になります。 |
| ActivationMaxRetryInterval |秒単位。既定値は 300 |アクティブ化が連続して失敗するたびに、ActivationMaxFailureCount で指定された回数までアクティブ化が再試行されます。 ActivationMaxRetryInterval は、アクティブ化が失敗してから再試行されるまでの待機時間を指定します |
| ActivationMaxFailureCount |整数。既定値は 10 |失敗したアクティブ化の再試行が行われる回数。再試行は、この回数だけ実行されてから停止します |

### <a name="section-name-failovermanager"></a>セクション名: FailoverManager
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| PeriodicLoadPersistInterval |秒単位。既定値は 10 |これにより、FM が新しい負荷レポートを確認する頻度が決まります |

### <a name="section-name-federation"></a>セクション名: Federation
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| LeaseDuration |秒単位。既定値は 30 |ノードとその近隣ノードの間のリース期間。 |
| LeaseDurationAcrossFaultDomain |秒単位。既定値は 30 |障害ドメイン全体におけるノードとその近隣ノードの間のリース期間。 |

### <a name="section-name-clustermanager"></a>セクション名: ClusterManager
| **パラメーター** | **使用できる値** | **ガイダンスまたは簡単な説明** |
| --- | --- | --- |
| UpgradeStatusPollInterval |秒単位。既定値は 60 |アプリケーションのアップグレード状態をポーリングする頻度。 この値により、GetApplicationUpgradeProgress 呼び出しの更新レートが決まります |
| UpgradeHealthCheckInterval |秒単位。既定値は 60 |監視対象アプリケーションをアップグレードしているときに、正常性状態をチェックする頻度 |
| FabricUpgradeStatusPollInterval |秒単位。既定値は 60 |Fabric アップグレード状態をポーリングする頻度。 この値により、GetFabricUpgradeProgress 呼び出しの更新レートが決まります |
| FabricUpgradeHealthCheckInterval |秒単位。既定値は 60 |監視対象 Fabric をアップグレードしているときに、正常性状態をチェックする頻度 |
|InfrastructureTaskProcessingInterval | 秒単位。既定値は 10 |timespan を秒単位で指定します。 ステート マシンを処理するインフラストラクチャ タスクで使用する処理間隔。 |
|TargetReplicaSetSize |int、既定値は 7 |ClusterManager の TargetReplicaSetSize。 |
|MinReplicaSetSize |int、既定値は 3 |ClusterManager の MinReplicaSetSize。 |
|ReplicaRestartWaitDuration |時間 (秒単位)、既定値は (60.0 * 30)|timespan を秒単位で指定します。 ClusterManager の ReplicaRestartWaitDuration。 |
|QuorumLossWaitDuration |時間 (秒単位)、既定値は MaxValue | timespan を秒単位で指定します。 ClusterManager の QuorumLossWaitDuration。 |
|StandByReplicaKeepDuration | 時間 (秒単位)、既定値は (3600.0 * 2)|timespan を秒単位で指定します。 ClusterManager の StandByReplicaKeepDuration。 |
|PlacementConstraints | wstring、既定値は L"" |ClusterManager の PlacementConstraints。 |
|SkipRollbackUpdateDefaultService | ブール値、既定値は false |CM は、アプリケーションのアップグレードのロールバック時に、更新された既定のサービスを元に戻す操作をスキップします。 |
|EnableDefaultServicesUpgrade | ブール値、既定値は false |アプリケーションのアップグレード時に既定のサービスのアップグレードを有効にします。 アップグレード後、既定のサービスの記述が上書きされます。 |
|InfrastructureTaskHealthCheckWaitDuration |時間 (秒単位)、既定値は 0| timespan を秒単位で指定します。 インフラストラクチャ タスクの後処理の後、正常性チェックを開始するまでの待機時間。 |
|InfrastructureTaskHealthCheckStableDuration | 時間 (秒単位)、既定値は 0| timespan を秒単位で指定します。 インフラストラクチャ タスクの後処理が正常に完了するまでに、正常性チェックに連続して合格していることを監視する時間。 正常性チェックに失敗したことが確認されると、このタイマーはリセットされます。 |
|InfrastructureTaskHealthCheckRetryTimeout | 秒単位。既定値は 60 |timespan を秒単位で指定します。 インフラストラクチャ タスクの後処理中に、失敗した正常性チェックの再試行に費やす時間。 正常性チェックに合格したことが確認されると、このタイマーはリセットされます。 |
|ImageBuilderTimeoutBuffer |時間 (秒単位)、既定値は 3 |timespan を秒単位で指定します。 Image Builder の特定のタイムアウト エラーをクライアントに返すことを可能にするための時間。 このバッファーが小さすぎると、サーバーの前にクライアントがタイムアウトし、一般的なタイムアウト エラーが発生します。 |
|MinOperationTimeout | 秒単位。既定値は 60 |timespan を秒単位で指定します。 ClusterManager での操作を内部的に処理する際の最小グローバル タイムアウト。 |
|MaxOperationTimeout |時間 (秒単位)、既定値は MaxValue | timespan を秒単位で指定します。 ClusterManager での操作を内部的に処理する際の最大グローバル タイムアウト。 |
|MaxTimeoutRetryBuffer | 時間 (秒単位)、既定値は 600 |timespan を秒単位で指定します。 タイムアウトによって内部的に再試行する際の操作の最大タイムアウトは <Original Timeout> + <MaxTimeoutRetryBuffer> です。 その他のタイムアウトは、MinOperationTimeout 単位で追加されます。 |
|MaxCommunicationTimeout |時間 (秒単位)、既定値は 600 |timespan を秒単位で指定します。 ClusterManager と他のシステム サービス (ネーム サービス、Failover Manager など) 間の内部通信の最大タイムアウト。 (各クライアント操作でシステム コンポーネント間の複数の通信が発生する可能性があるので) このタイムアウトはグローバルな MaxOperationTimeout の値よりも小さくする必要があります。 |
|MaxDataMigrationTimeout |時間 (秒単位)、既定値は 600 |timespan を秒単位で指定します。 Fabric のアップグレードが実行された後のデータ移行復旧操作の最大タイムアウト。 |
|MaxOperationRetryDelay |時間 (秒単位)、既定値は 5| timespan を秒単位で指定します。 エラーが発生したときの内部再試行の最大遅延。 |
|ReplicaSetCheckTimeoutRollbackOverride |時間 (秒単位)、既定値は 1200 | timespan を秒単位で指定します。 ReplicaSetCheckTimeout が DWORD の最大値に設定されている場合、ロールバックのためにこの構成の値で上書きされます。 ロールフォワードに使用する値が上書きされることはありません。 |
|ImageBuilderJobQueueThrottle |int、既定値は 10 |アプリケーション要求での Image Builder プロキシ ジョブ キューのスレッド数のスロットル。 |

## <a name="next-steps"></a>次のステップ
クラスター管理の詳細については、次の記事を参照してください。

[Azure クラスターの証明書の追加、ロール オーバー、削除 ](service-fabric-cluster-security-update-certs-azure.md) 




<!--HONumber=Feb17_HO1-->


