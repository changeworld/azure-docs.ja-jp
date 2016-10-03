
<properties
   pageTitle="Service Fabric クラスターの設定と Fabric アップグレード ポリシーのカスタマイズ | Microsoft Azure"
   description="この記事では、カスタマイズ可能な Fabric の設定と Fabric アップグレード ポリシーについて説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="chackdan"/>

# Service Fabric クラスターの設定と Fabric アップグレード ポリシーのカスタマイズ

このドキュメントでは、Service Fabric クラスターのさまざまな Fabric 設定と Fabric アップグレード ポリシーをカスタマイズする方法について説明します。この設定やポリシーは、ポータルまたは Resource Manager テンプレートを使用してカスタマイズできます。

## カスタマイズできる Fabric の設定


カスタマイズできる Fabric の設定を次に示します。

### セクション名: セキュリティ

|**パラメーター**|**使用できる値**|**ガイダンスまたは簡単な説明**|
|-----------------------|--------------------------|--------------------------|
|ClusterProtectionLevel|None または EncryptAndSign| セキュリティで保護されていないクラスターの場合は None (既定)、セキュリティで保護されたクラスターの場合は EncryptAndSign です。 |

### セクション名: Hosting

|**パラメーター**|**使用できる値**|**ガイダンスまたは簡単な説明**|
|-----------------------|--------------------------|--------------------------|
|ServiceTypeRegistrationTimeout|秒単位。既定値は 300| Fabric に登録する ServiceType の最大許容時間|
|ServiceTypeDisableFailureThreshold|整数。既定値は 1| 失敗回数のしきい値。この回数を失敗した後、そのノード上のサービスの種類を無効にして、別のノードへの配置を試行するよう FailoverManager (FM) に通知されます。|
|ActivationRetryBackoffInterval|秒単位。既定値は 5|アクティブ化失敗のバックオフ間隔。アクティブ化が連続して失敗するたびに、MaxActivationFailureCount で指定された回数までアクティブ化が再試行されます。各試行の再試行間隔は、継続的なアクティブ化失敗とアクティブ化バックオフ間隔の積になります。|
|ActivationMaxRetryInterval|秒単位。既定値は 300| アクティブ化が連続して失敗するたびに、ActivationMaxFailureCount で指定された回数までアクティブ化が再試行されます。ActivationMaxRetryInterval は、アクティブ化が失敗してから再試行されるまでの待機時間を指定します |
|ActivationMaxFailureCount|整数。既定値は 10| 失敗したアクティブ化の再試行が行われる回数。再試行は、この回数だけ実行されてから停止します |

### セクション名: FailoverManager

|**パラメーター**|**使用できる値**|**ガイダンスまたは簡単な説明**|
|-----------------------|--------------------------|--------------------------|
|PeriodicLoadPersistInterval|秒単位。既定値は 10| これにより、FM が新しい負荷レポートを確認する頻度が決まります|

### セクション名: Federation

|**パラメーター**|**使用できる値**|**ガイダンスまたは簡単な説明**|
|-----------------------|--------------------------|--------------------------|
|LeaseDuration|秒単位。既定値は 30|ノードとその近隣ノードの間のリース期間。|
|LeaseDurationAcrossFaultDomain|秒単位。既定値は 30|障害ドメイン全体におけるノードとその近隣ノードの間のリース期間。|

### セクション名: ClusterManager

|**パラメーター**|**使用できる値**|**ガイダンスまたは簡単な説明**|
|-----------------------|--------------------------|--------------------------|
|UpgradeStatusPollInterval|秒単位。既定値は 60|アプリケーションのアップグレード状態をポーリングする頻度。この値により、GetApplicationUpgradeProgress 呼び出しの更新レートが決まります|
|UpgradeHealthCheckInterval|秒単位。既定値は 60|監視対象アプリケーションをアップグレードしているときに、正常性状態をチェックする頻度|
|FabricUpgradeStatusPollInterval|秒単位。既定値は 60|Fabric アップグレード状態をポーリングする頻度。この値により、GetFabricUpgradeProgress 呼び出しの更新レートが決まります |
|FabricUpgradeHealthCheckInterval|秒単位。既定値は 60|監視対象 Fabric をアップグレードしているときに、正常性状態をチェックする頻度|



## 次のステップ

クラスター管理の詳細については、次の記事を参照してください。

[Azure クラスターの証明書の追加、ロール オーバー、削除](service-fabric-cluster-security-update-certs-azure.md)

<!---HONumber=AcomDC_0921_2016-->