<properties
   pageTitle="Service Fabric の正常性のレポートとチェックの方法 | Microsoft Azure"
   description="この記事では、サービス コードから正常性レポートを送信し、Azure Service Fabric に用意されている正常性監視ツールを使用してサービスの正常性をチェックする方法について説明します"
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/05/2015"
   ms.author="kunalds"/>


# サービス正常性のレポートとチェック
サービスで問題が発生した場合、その結果のインシデントと停止に対処して修正する能力は、問題を迅速に検出できるかどうかに依存します。問題とエラーをサービス コードから Service Fabric Health Manager にレポートすることにより、正常性状態を確認するために Service Fabric に用意されている標準の正常性監視ツールを使用できます。このドキュメントでは、サービスに正常性レポートを追加する例について説明し、Service Fabric に用意されているツールを使用して正常性状態を確認する方法を示します。この記事は、Service Fabric の正常性監視機能を簡単に紹介するためのものであり、詳細については、このドキュメントの末尾にあるリンクで始まる、正常性についての一連の解説記事を参照してください。

## 前提条件
次のものがインストールされている必要があります: * Visual Studio 2015 * Service Fabric SDK

## アプリケーションのデプロイと正常性のチェック
アプリケーションをデプロイし、正常性をチェックするには、次の手順に従います。

1. Visual Studio を管理者として起動します。

2. ステートフル サービス用のプロジェクトを作成します。

  ![ステートフル サービスを持つ Service Fabric アプリケーションを作成](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. F5 キーを押してデバッグ モードでアプリケーションを実行します。アプリケーションは、ローカル クラスターにデプロイされます。

4. アプリケーションの実行後、ローカル クラスター マネージャーのシステム トレイ アプリを右クリックし、コンテキスト メニューで [ローカル クラスターの管理] を選択して、Service Fabric Explorer を起動します。![システム トレーからの Service Fabric Explorer]()

5. アプリケーションの正常性が、次の図のように表示されます。この時点では、アプリケーションはエラーなしで、正常です。

  ![Service Fabric Explorer での正常なアプリケーション](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. また、Powershell を使用して正常性をチェックすることもできます。アプリケーションの正常性をチェックするには ```Get-ServiceFabricApplicationHealth``` を使用し、サービス正常性を照会するには ```Get-ServiceFabricServiceHealth``` を使用します。同じアプリケーションの正常性レポートは、Powershell では次のようになります。![Powershell での正常なアプリケーション](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## サービス コードへのカスタム正常性イベントの追加
Service Fabric の Visual Studio プロジェクト テンプレートには、サンプル コードが含まれています。次の手順では、サービス コードからカスタム正常性イベントをレポートする方法を説明します。このようなレポートは、Service Fabric に用意されている正常性監視用の標準ツール (Service Fabric Explorer、Azure ポータルの正常性ビュー、Powershell など) に自動的に表示されます。

1. Visual Studio で前に作成したアプリケーションを再度開くか、ステートフル サービスを持つ新しいアプリケーションを VS テンプレートから作成します。
2. **Stateful1.cs** ファイルを開きます。`var myDictionary` の宣言を探して、`var myDictionary` の宣言のすぐ後に次のコードを追加します。ここで作成される `fabricClient` オブジェクトは、後で正常性をレポートするために使用されます。

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    この名前空間も、**Stateful1.cs** ファイルに追加します。

    ```csharp
    using System.Fabric;
    ```

4. 次に、*RunAsync* メソッド内の `myDictionary.TryGetValueAsync` 呼び出しを調べます。カウンターの現在の値を保持している `result` が返されることがわかります。これは、このアプリケーションのキー ロジックが、実行回数を保持するためです。これが実際のアプリケーションであり、結果がないとエラーになるのだとしたら、Health Manager にレポートするでしょう。
5. エラーを示す、結果がないという正常性イベントをレポートするには、`myDictionary.TryGetValueAsync` 呼び出しの後に以下のコードを追加します。ステートフル サービスからレポートされているため、イベントは `StatefulServiceReplicaHealthReport` としてレポートされます。レポート イベントに渡される PartitionId と ReplicaId は、いずれかの正常性監視ツールでレポートを見るときに、レポートのソースを識別するために役立ちます。デプロイされたステートフル サービスは複数のパーティションを持つことがあり、各パーティションは複数のレプリカを持つことがあるためです。`HealthInformation` パラメーターには、レポートされている正常性の問題についての情報が格納されます。この名前空間を、**Stateful1.cs** ファイルに追加します。

    ```csharp
    using System.Fabric.Health;
    ```

    `myDictionary.TryGetValueAsync` 呼び出しの後に、このコードを追加します。

    ```csharp
    if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
    
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. このエラーをシミュレートし、正常性監視ツールに表示されるところを見てみましょう。エラーをシミュレートするには、前に追加した正常性レポート コードの最初の行をコメントアウトします。最初の行をコメントアウトした後、コードは以下のようになります。これで、RunAsync が実行されるたびに、この正常性レポートが生成されるようになります。変更後、F5 キーを押して、アプリケーションを実行します。

    ```csharp
    //if(!result.HasValue)
    {
        var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
    
        var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

6. アプリケーションが実行されたら、Service Fabric Explorer を開いて、アプリケーションの正常性をチェックします。今度は、Service Fabric Explorer に、アプリケーションが異常であることが表示されます。これは、上で追加したコードからレポートされたエラーのためです。![Service Fabric Explorer での異常なアプリケーション](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Service Fabric Explorer のツリー ビューでプライマリ レプリカを選択すると、正常性がエラーとして表示されるのがわかります。また、コードで `HealthInformation` パラメーターに追加した正常性レポートの詳細も表示されます。同じ正常性レポートを、Powershell でも、Azure ポータルでも見ることができます。![Service Fabric Explorer でのレプリカの正常性](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

このレポートは、別のレポートで置き換えられるか、このレプリカが削除されるまで、Health Manager に残されます (*HealthInformation オブジェクト内の、この正常性レポートの TimeToLive を設定しなかったため、期限切れになることはありません*)。正常性を照会したりレポートしたりできるように、FabricClient は管理者特権で処理中である必要があります。

## 次のステップ
[Service Fabric の正常性の詳細](service-fabric-health-introduction.md)

<!---HONumber=Nov15_HO4-->