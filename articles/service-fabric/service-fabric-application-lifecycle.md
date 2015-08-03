<properties
   pageTitle="Azure Service Fabric アプリケーションのライフサイクル | Microsoft Azure"
   description="Service Fabric アプリケーションの開発、デプロイ、テスト、アップグレード、保守、および削除について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="ryanwi; mani-ramaswamy"/>


# Service Fabric アプリケーションの ライフサイクル
通常は、その他のプラットフォームと同様に、Service Fabric のアプリケーションは、デザイン、開発、テスト、デプロイメント、アップグレード、保守、削除のフェーズを通過します。Service Fabric は、開発からデプロイメント、日常的な管理、保守、最終的な使用停止に至るまで、クラウド アプリケーションの完全なアプリケーション ライフサイクル管理 (ALM) に対して高度なサポートを提供します。そのサービス モデルにより、アプリケーションのライフサイクルで個別に関与するさまざまな役割が有効になります。この記事では、API の概要と、Service Fabric アプリケーション ライフサイクルのフェーズでさまざまなロールがその API をどのように使用するかを示します。

## サービス モデルのロール
サービス モデルのロールは、次のとおりです。

- **サービス開発者** -同じ種類や別の種類の複数のアプリケーションで再利用、使用できるモジュールと汎用サービスを開発します。たとえば、チケット発行のアプリケーション (ヘルプ デスク) または電子商取引アプリケーション (ショッピング カート) を作成するために Queue サービスを使用できます。

- **アプリケーション開発者** - 特定の要件またはシナリオを満たすために、サービスのコレクションを統合してアプリケーションを作成します。たとえば、電子商取引 Web サイトには、"JSON ステートレス フロントエンド サービス"、"オークション ステートフル サービス"、"ステートフル キュー サービス" を統合して、オークション ソリューションが構築されることがあります。

- **アプリケーション管理者** -アプリケーションの構成 (テンプレートの構成パラメーターを入力)、デプロイメント (使用可能なリソースへのマッピング)、サービスの品質を決定します。たとえば、アプリケーション管理者は、アプリケーションの言語ロケール (米国には英語、日本には日本語) を決定します。別のデプロイされたアプリケーションには、さまざまな設定を含めることができます。

- **オペレーター** - アプリケーションの構成に基づくアプリケーションと、アプリケーション管理者が指定する要件をデプロイします。たとえば、オペレーターはアプリケーションをプロビジョニングしてデプロイし、Azure で実行されていることを確認します。オペレーターは、アプリケーションの正常性とパフォーマンスの情報を監視し、必要に応じて、物理インフラストラクチャを管理します。


## 開発
1. *サービス開発者*は、[信頼性の高いアクター](service-fabric-reliable-actors-introduction.md)または[信頼性の高いサービス](../Service-Fabric/service-fabric-reliable-services-introduction.md) プログラミング モデルを使用してさまざまな種類のサービスを開発します。
2. *サービス開発者* は、1 つ以上のコード、構成、データのパッケージで構成されるサービス マニフェスト ファイルで、開発したサービスの種類について宣言によって記述します。
3. *アプリケーション開発者*は、異なる種類のサービスを使用するアプリケーションを構築します。
4. *アプリケーション開発者*は、構成サービスのサービス マニフェストを参照し、異なる構成と構成サービスのデプロイメント設定を適切にオーバーライドしてパラメーター化し、アプリケーション マニフェストでアプリケーションの種類を宣言によって記述します。

例については、「[信頼できるアクターの使用](service-fabric-reliable-actors-get-started.md)」と「[信頼性の高いサービスの使用](service-fabric-reliable-services-quick-start.md)」をご覧ください。

## デプロイ
1. *アプリケーション管理者*は、アプリケーション マニフェストで **ApplicationType** 要素を指定することで、Service Fabric クラスターにデプロイされるよう、特定のアプリケーションに合わせてアプリケーションの種類を調整します。

2. *オペレーター*は、[CopyApplicationPackage メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx)または [Copy-ServiceFabricApplicationPackage コマンドレット](https://msdn.microsoft.com/library/azure/mt125905.aspx)を使用して、クラスター ImageStore にアプリケーション パッケージをアップロードします。アプリケーション パッケージには、アプリケーション マニフェストとサービス パッケージのコレクションが含まれています。Service Fabric は、Azure Blob ストアまたは Service Fabric のシステム サービスが対象となる、ImageStore に格納されているアプリケーション パッケージからアプリケーションをデプロイします。

3. *オペレーター*は、[ProvisionApplicationAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx)、[Register-ServiceFabricApplicationType コマンドレット](https://msdn.microsoft.com/library/azure/mt125958.aspx)、[Create Application REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx) を使用してアップロードされたアプリケーション パッケージから対象のクラスターでアプリケーションの種類をプロビジョニングします。

3. アプリケーションをプロビジョニングした後、*オペレーター*は [CreateApplicationAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx)、[New-ServiceFabricApplication コマンドレット](https://msdn.microsoft.com/library/azure/mt125913.aspx)、[Create Application REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx)を使用して*アプリケーション管理者*によって提供されるパラメーターでアプリケーションを起動します。

4. アプリケーションをデプロイした後、*オペレーター*は [CreateServiceAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx)、[New-ServiceFabricService コマンドレット](https://msdn.microsoft.com/library/azure/mt125874.aspx)、[Create Application REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx) を使用して、使用可能なサービスの種類に基づいて、アプリケーションの新しいサービス インスタンスを作成します。

5. これで、Service Fabric のクラスターで、アプリケーションが実行されます。

例については、「[Deploy an application (アプリケーションをデプロイする)](service-fabric-deploy-remove-applications.md)」をご覧ください。

## テスト
1. ローカル開発クラスターまたはテスト クラスターにデプロイした後、*サービス開発者*は [FailoverTestScenarioParameters](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx)、[FailoverTestScenario](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) クラス、[Invoke-ServiceFabricFailoverTestScenario コマンドレット](https://msdn.microsoft.com/library/azure/mt125935.aspx)を使用して、組み込みのフェールオーバー テスト シナリオを実行します。フェールオーバー テスト シナリオで、重要な切り換えとフェールオーバーを通して指定したサービスを実行して、引き続き利用可能で動作していることを確認します。

2. 次に、*サービス開発者*は、[ChaosTestScenarioParametersscenarioParameters](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) と [ChaosTestScenario](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) クラスまたは [Invoke-ServiceFabricChaosTestScenario コマンドレット](https://msdn.microsoft.com/library/azure/mt126036.aspx) を使用して組み込みの chaos テスト シナリオを実行します。chaos テスト シナリオは、複数のノード、コード パッケージ、レプリカのエラーをクラスターにランダムに誘導します。

例については、「[Testability scenarios (シナリオのテストの容易性)](service-fabric-testability-scenarios.md)」をご覧ください。

## アップグレード
1. *サービス開発者*は、インスタンス化されたアプリケーションの構成サービスの更新、バグの修正を行い、サービス マニフェストの新しいバージョンを提供します。

2. *アプリケーション開発者*は、一貫性のあるサービスの構成とデプロイメント設定をオーバーライドしてパラメーター化し、新しいバージョンのアプリケーション マニフェストを提供します。アプリケーション開発者は、アプリケーションに新しいバージョンのサービス マニフェストを組み込み、更新されたアプリケーション パッケージで新しいバージョンのアプリケーションの種類を提供します。

3. *アプリケーション管理者*は、適切なパラメーターを更新することで、対象のアプリケーションに新しいバージョンのアプリケーションの種類を組み込みます。

4. *オペレーター*は、[CopyApplicationPackage メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx)または [Copy-ServiceFabricApplicationPackage コマンドレット](https://msdn.microsoft.com/library/azure/mt125905.aspx)を使用して、クラスター ImageStore に更新されたアプリケーション パッケージをアップロードします。アプリケーション パッケージには、アプリケーション マニフェストとサービス パッケージのコレクションが含まれています。

5. *オペレーター*は、[ProvisionApplicationAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx)、[Register-ServiceFabricApplicationType コマンドレット](https://msdn.microsoft.com/library/azure/mt125958.aspx)、[Provision an Application REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx)を使用して、対象のクラスターで新しいバージョンのアプリケーションをプロビジョニングします。

6. *オペレーター*は、[UpgradeApplicationAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx)、[Start-ServiceFabricApplicationUpgrade コマンドレット](https://msdn.microsoft.com/library/azure/mt125975.aspx)、[Upgrade Application by Application Type REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx)を使用して、対象のアプリケーションを新しいバージョンにアップグレードします。

7. *オペレーター*は、[GetApplicationUpgradeProgressAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx)、[Get-ServiceFabricApplicationUpgrade コマンドレット](https://msdn.microsoft.com/library/azure/mt125988.aspx)、[Get Application Upgrade Progress REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx)を使用して、アップグレードの進行状況を確認します。

8. 必要に応じて、*オペレーター* は、[UpdateApplicationUpgradeAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx)、[Update-ServiceFabricApplicationUpgrade コマンドレット](https://msdn.microsoft.com/library/azure/mt126030.aspx)、[Update Application Upgrade REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx)を使用して、現在のアプリケーション アップグレードのパラメーターを変更して再適用します。

9. 必要に応じて、*オペレーター*は、[RollbackApplicationUpgradeAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx)、[Start-ServiceFabricApplicationRollback コマンドレット](https://msdn.microsoft.com/library/azure/mt125833.aspx)、[Rollback Application Upgrade REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx)を使用して、現在のアプリケーション アップグレードをロールバックします。

10. Service Fabric はその構成サービスのいずれかの可用性を失うことなく、クラスター内で実行されている対象アプリケーションをアップグレードします。

例については、「[Application upgrade tutorial (アプリケーション アップグレードのチュートリアル)](service-fabric-application-upgrade-tutorial.md)」をご覧ください。

## 管理
1. オペレーティング システムのアップグレードと修正プログラムに対しては、Service Fabric はクラスター内で実行しているすべてのアプリケーションの可用性を保証する Azure インフラストラクチャとつなぐインターフェイスです。

2. Service Fabric プラットフォームへのアップグレードと修正プログラムに対しては、クラスターで実行されているアプリケーションの可用性が失われることなく、Service Fabric 自体でアップグレードされます。

3. *アプリケーション管理者*は、過去の容量使用率のデータと予測される将来の要求を分析した後、ノードの追加やクラスターからのノードの削除を承認します。

4. *オペレーター*は、*アプリケーション管理者*が指定したノードを追加、削除します。

5. 新しいノードをクラスターに追加する、または既存のノードをクラスターから削除するとき、Service Fabric は最適なパフォーマンスを実現するために、クラスター内のすべてのノード間でアプリケーションを実行している負荷を自動的に分散します。

## 削除
1. *オペレーター*は、[DeleteServiceAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx)、[Remove-ServiceFabricService コマンドレット](https://msdn.microsoft.com/library/azure/mt126033.aspx)、[Delete Service REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx)を使用して、アプリケーション全体を削除することなく、クラスター内の実行中のサービスの特定のインスタンスを削除できます。  

2. *オペレーター*は、[DeleteApplicationAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx)、[Remove-ServiceFabricApplication コマンドレット](https://msdn.microsoft.com/library/azure/mt125914.aspx)、[Delete Application REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx)を使用してアプリケーションのインスタンスとそのすべてのサービスも削除できます。

3. アプリケーションとサービスが停止されると、*オペレーター*は [UnprovisionApplicationAsync メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx)、[Unregister-ServiceFabricApplicationType コマンドレット](https://msdn.microsoft.com/library/azure/mt125885.aspx)、[Unprovision an Application REST 操作](https://msdn.microsoft.com/library/azure/dn707692.aspx)を使用して、アプリケーションの種類のプロビジョニングを解除できます。プロビジョニングを解除されたアプリケーションの種類の場合、ImageStore からアプリケーション パッケージが削除されないため、アプリケーション パッケージを手動で削除する必要があります。

4. *オペレーター*は、[RemoveApplicationPackage メソッド](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx)または [Remove-ServiceFabricApplicationPackage コマンドレット](https://msdn.microsoft.com/library/azure/mt163532.aspx)を使用して、ImageStore からアプリケーション パッケージを削除します。

例については、「[Deploy an application (アプリケーションをデプロイする)](service-fabric-deploy-remove-applications.md)」をご覧ください。

## 次のステップ

Service Fabric アプリケーションとサービスの開発、テスト、管理に関する詳細については、以下をご覧ください。

- [Develop a Service Fabric service (Service Fabric サービスの開発)](service-fabric-develop-your-service-index.md)
- [Manage a Service Fabric service (Service Fabric サービスの管理)](service-fabric-manage-your-service-index.md)
- [Test a Service Fabric service (Service Fabric サービスのテスト)](service-fabric-test-your-service-index.md)
- [REST-Based Application Lifecycle Sample (REST ベースのアプリケーション ライフ サイクルのサンプル)](service-fabric-rest-based-application-lifecycle-sample.md)
 

<!---HONumber=July15_HO4-->