---
title: "Azure Service Fabric パッチ オーケストレーション アプリケーション | Microsoft Docs"
description: "Service Fabric クラスター上の OS へのパッチ適用を自動化するためのアプリケーション。"
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/9/2017
ms.author: nachandr
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a3554881e7db894c602e73feb385b5b361837409
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017


---

# <a name="application-to-patch-windows-os-in-your-service-fabric-cluster"></a>Service Fabric クラスター内の Windows OS にパッチを適用するためのアプリケーション

パッチ オーケストレーション アプリケーションは、Azure またはオンプレミスの Service Fabric クラスター上の OS に対し、ダウンタイムなしで自動的にパッチを適用することができる Service Fabric アプリケーションです。

パッチ オーケストレーション アプリケーションの主な機能は次のとおりです。

1. **OS 更新プログラムの自動インストール**: パッチ オーケストレーション アプリケーションによって更新プログラムが自動的にダウンロード、インストールされ、必要に応じてノードが再起動されます。
    この作業はすべてのクラスター ノードで実行され、ダウンタイムは発生しません。

1. **クラスター対応のパッチ適用と正常性の統合**: パッチ オーケストレーション アプリケーションは、更新プログラムの適用中、1 ノードずつ、または 1 アップグレード ドメインずつ更新しながらクラスターの正常性を監視します。 
    パッチの適用処理が原因でクラスターの正常性が低下しつつあることをどこかの時点で検出した場合は、その処理を停止し、問題が一層悪化するのを防ぎます。

1. **すべての Service Fabric クラスターのサポート**: このアプリケーションは汎用性が高く、Azure Service Fabric クラスターとスタンドアロン クラスターのどちらでも動作します。
    > [!NOTE]
    > スタンドアロン クラスターには近日中に対応予定です。

## <a name="link-to-download-the-application-package"></a>アプリケーション パッケージをダウンロードするためのリンク

このアプリケーションは、[こちらのダウンロード リンク](https://go.microsoft.com/fwlink/P/?linkid=849590)から入手できます。

## <a name="internal-details-of-the-application"></a>アプリケーション内部の詳しい情報

パッチ オーケストレーション アプリケーションは、以下のサブコンポーネントから構成されます。

- **コーディネーター サービス**: ステートフル サービス。 このサービスの役割は次のとおりです。
    - クラスター全体における Windows Update ジョブを調整する。
    - 完了した Windows Update の処理結果を保存する。
- **ノード エージェント サービス**: ステートレス サービス。Service Fabric クラスターの全ノードで実行されます。 このサービスの役割は次のとおりです。
    - ノード エージェント NTService のブートストラップ。
    - ノード エージェント NTService の監視。
- **ノード エージェント NTService**: Windows NT サービス。 ノード エージェント NTService は、相対的に高い特権レベル (SYSTEM) で実行されます。 一方、ノード エージェント サービスとコーディネーター サービスは、相対的に低い特権レベル (NETWORK SERVICE) で実行されます。 このサービスの役割は、全クラスター ノードで以下の Windows Update ジョブを実行することです。
    - ノードの自動 Windows Update を無効にする。
    - ユーザーによって指定されたポリシーに従って Windows の更新プログラムをダウンロードしてインストールする。
    - Windows 更新プログラムのインストール後にマシンを再起動する。
    - Windows Update の結果をコーディネーター サービスにアップロードする。
    - 更新処理が失敗した場合、特定の回数、再試行を行った後、正常性レポートを報告する。

> [!NOTE]
> パッチ オーケストレーション アプリケーションは、Service Fabric の **Repair Manager** というシステム サービスを使用して、正常性チェックを実行しながらノードを無効にしたり有効にしたりします。 Windows Update の進行状況は、パッチ オーケストレーション アプリケーションによって作成される修復タスクによってノードごとに追跡されます。

## <a name="prerequisites-for-using-the-application"></a>アプリケーションを使用するための前提条件

### <a name="ensure-service-fabric-version-is-55-or-above"></a>Service Fabric のバージョンが 5.5 以上であることを確認する

パッチ オーケストレーション アプリケーションは、Service Fabric ランタイム バージョン v5.5 以上がインストールされているクラスターで実行できます。

### <a name="enable-repair-manager-service-if-not-running-already"></a>Repair Manager サービスを有効にする (まだ実行されていない場合)

パッチ オーケストレーション アプリケーションを使用するためには、クラスターで Repair Manager システム サービスを有効にする必要があります。

#### <a name="service-fabric-clusters-on-azure"></a>Azure 上の Service Fabric クラスター

耐久性レベルが Silver の Azure クラスターでは、既定で Repair Manager が有効になります。 耐久性レベルが Gold の Azure クラスターでは、それらのクラスターが作成されてからどのぐらいの期間が経過しているかによって、Repair Manager が有効になっている場合となっていない場合とがあります。 Azure クラスターの耐久性レベルが Bronze の場合は、Repair Manager サービスが既定では有効になっていません。 

Service Fabric Explorer のシステム サービス セクションを見て、Repair Manager サービスがまだ実行されていないことがわかった場合は、[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)を使用して、新しい Service Fabric クラスターまたは既にある Service Fabric クラスターに対してサービスを有効にすることができます。

まず、デプロイするクラスター用テンプレートを用意します。 サンプル テンプレートを使用することも、カスタムの Resource Manager テンプレートを作成することもできます。 その後、次の手順で Repair Manager を有効にすることができます。

1. まず、次のスニペットに示すように、`Microsoft.ServiceFabric/clusters` リソースの `apiversion` が `2017-07-01-preview` に設定されていることを確認します。 違う場合は、`apiVersion` を `2017-07-01-preview` の値に更新する必要があります。

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. 次のように、`fabricSettings` セクションの後に以下の `addonFeaturres` セクションを追加して Repair Manager サービスを有効にします。

    ```json
    "fabricSettings": [
        ...      
        ],
        "addonFeatures": [
            "RepairManager"
        ],
    ```

3. これらの変更によりクラスター テンプレートを更新したら、変更を適用してアップグレードを完了します。 アップグレードの完了後、Repair Manager システム サービスがクラスターで実行されていることが確認できます。このサービスの名前は Service Fabric Explorer のシステム サービス セクションに `fabric:/System/RepairManagerService` と表示されます。 

#### <a name="standalone-on-premise-clusters"></a>スタンドアロン オンプレミス クラスター

> [!NOTE]
> スタンドアロン クラスターには近日中に対応予定です。

### <a name="disable-automatic-windows-update-on-all-nodes"></a>すべてのノードで自動 Windows Update を無効にする

Service Fabric クラスターで自動 Windows Update を有効にすると、更新を完了するために複数のノードが同時に再起動され、可用性が失われる場合があります。

特になにもしなくても、各クラスター ノードの自動 Windows Update は、パッチ オーケストレーション アプリケーションによって無効化が試みられます。

ただし、その設定が管理者やグループ ポリシーによって管理されている場合は、Windows Update ポリシーを明示的に "ダウンロードする前に通知する" ように設定することをお勧めします。


### <a name="optional-enable-windows-azure-diagnostics"></a>(省略可) Microsoft Azure 診断を有効にする

パッチ オーケストレーション アプリケーションのログは近日中に、Service Fabric のログの一部として収集されるようになる予定です。 しかしそれまでは、各クラスター ノードのローカルでログが収集されます。 Microsoft Azure 診断 (WAD) を構成して、すべてのノードからのログを中央の場所にアップロードすることをお勧めします。

WAD を有効にする手順について詳しくは、[こちら](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-how-to-setup-wad)を参照してください。

パッチ オーケストレーション アプリケーションのログは、以下に示す固定されたプロバイダー ID で生成されます。

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

Azure Resource Manager テンプレート内の "WadCfg" セクションに次のセクションを追加します。 

```json
"PatchOrchestrationApplication": \[
  {
    "provider": "e39b723c-590c-4090-abb0-11e3e6616346",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
      "eventDestination": "PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "fc0028ff-bfdc-499f-80dc-ed922c52c5e9",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "24afa313-0d3b-4c7c-b485-1047fd964b60",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
  {
    "provider": "05dc046c-60e9-4ef7-965e-91660adffa68",
    "scheduledTransferPeriod": "PT5M",
    "DefaultEvents": {
    "eventDestination": " PatchOrchestrationApplicationTable"
    }
  },
\]
```

> [!NOTE]
> Service Fabric クラスターが複数のノード タイプで構成されている場合は、上記のセクションをすべての "WadCfg" セクションに追加する必要があります。

## <a name="configuring-the-application"></a>アプリケーションの構成

以下に示したのは、ユーザーが調整できる構成です。これらの構成を行うことで、必要に応じてパッチ オーケストレーション アプリケーションの動作を調整することができます。

アプリケーション パラメーターには、アプリケーションの作成時または更新時に値を渡すことで既定値を上書きすることができます。 アプリケーションのパラメーターは、`Start-ServiceFabricApplicationUpgrade` コマンドレットまたは `New-ServiceFabricApplication` コマンドレットに `ApplicationParameter` を指定することによって渡すことができます。

|**パラメーター**        |**型**                          | **詳細**|
|:-|-|-|
|MaxResultsToCache    |long                              | 履歴としてキャッシュする Windows Update の結果の最大件数。 <br>既定値は 3000 で、次の条件を想定しています。 <br> - ノード数が 20 個である <br> - ノード上で実行される更新の回数が 1 か月あたり 5 回である <br> - 1 回の処理で 10 件程度の結果が生成される <br> - 過去 3 か月の結果を保存する |
|TaskApprovalPolicy   |列挙型 <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy は、CoordinatorService が、Service Fabric クラスターの全ノードに Windows の更新プログラムをインストールするために使用するポリシーを表します。<br>                         使用できる値は、以下のとおりです。 <br><br>                                                           <b>NodeWise</b> - Windows 更新プログラムは 1 ノードずつインストールされます。 <br>                                                           <b>UpgradeDomainWise</b> - Windows 更新プログラムは、1 アップグレード ドメインずつインストールされます (最大でアップグレード ドメインに属するすべてのノードが Windows Update の対象となる可能性があります)。
|LogsDiskQuotaInMB   |long  <br> (既定値: 1024)               |パッチ オーケストレーション アプリケーションのログの最大サイズ (MB 単位)。このサイズまでは、ノード上のローカルに保存することができます。
| WUQuery               | string<br>(既定値: "IsInstalled=0")                | Windows 更新プログラムを取得するためのクエリ。詳細については、[WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx) を参照してください。
| InstallWindowsOSOnlyUpdates | ブール値 <br> (既定値: True)                 | このフラグによって、Windows OS の更新プログラムのインストールが許可されます。            |
| WUOperationTimeOutInMinutes | int <br>(既定値: 90)                   | Windows Update の各処理 (検索/ダウンロード/インストール) のタイムアウトを指定します。 指定したタイムアウト時間内に処理が完了しなかった場合、処理が中止されます。       |
| WURescheduleCount     | int <br> (既定値: 5)                  | Windows Update が繰り返し失敗する場合に、そのスケジュールを再調整する回数の上限が、この構成によって決まります。          |
| WURescheduleTimeInMinutes | int <br>(既定値: 30) | Windows Update が繰り返し失敗する場合に、そのスケジュールを再調整する間隔が、この構成によって決まります。 |
| WUFrequency           | コンマ区切りの文字列 (既定値: "Weekly, Wednesday, 7:00:00")     | Windows 更新プログラムをインストールする頻度。 その形式と指定できる値を次に示します。 <br>-   Monthly,DD,HH:MM:SS (例: Monthly,5,12:22:32) <br> -   Weekly,DAY,HH:MM:SS (例: Weekly, Tuesday, 12:22:32)  <br> -   Daily, HH:MM:SS (例: Daily, 12:22:32)  <br> -  None - Windows Update は実行されません。  <br><br> 注: 時刻はすべて UTC 形式です。|
| AcceptWindowsUpdateEula | ブール値 <br>(既定値: true) | Windows Update の EULA への同意をコンピューターの所有者ではなくアプリケーションで行うには、このフラグを設定します。              |


## <a name="steps-to-deploy-the-application"></a>アプリケーションをデプロイする手順

1. 前提条件となる手順をすべて実行してクラスターを準備します。
1. アプリケーションをデプロイします。この作業は、[こちら](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)に記載されている手順に従って PowerShell から実行できます。
1. デプロイ時にアプリケーションを構成する場合は、`New-ServiceFabricApplication` コマンドレットに `ApplicationParamater` を指定します。

    ユーザーの負担を軽減するために、アプリケーションと併せて Deploy.ps1 スクリプトを用意しました。 使い方は次のとおりです。

    - `Connect-ServiceFabricCluster` を使用して Service Fabric クラスターに接続します。
    - 適切な `ApplicationParameter` 値を指定して PowerShell スクリプト Deploy.ps1 を実行します。

> [!NOTE]
> このスクリプトは、アプリケーション フォルダー PatchOrchestrationApplication と同じディレクトリに保存してください。

## <a name="steps-to-upgrade-the-application"></a>アプリケーションをアップグレードする手順

PowerShell を使用して既存のパッチ オーケストレーション アプリケーションをアップグレードする場合は、[こちら](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell)に記載されているアプリケーション アップグレード手順を参照してください。

単にアプリケーションのパラメーターを変更するだけであれば、`Start-ServiceFabricApplicationUpgrade` コマンドレットに、既存のアプリケーションのバージョンと合わせて `ApplicationParamater` を指定してください。

## <a name="steps-to-remove-the-application"></a>アプリケーションを削除する手順

アプリケーションを削除するには、[こちら](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications)に記載されている手順を参照してください。

ユーザーの負担を軽減するために、アプリケーションと併せて Undeploy.ps1 スクリプトを用意しました。 使い方は次のとおりです。
    - ```Connect-ServiceFabricCluster``` を使用して Service Fabric クラスターに接続します。
    - PowerShell スクリプト Undeploy.ps1 を実行します。

> [!NOTE]
> このスクリプトは、アプリケーション フォルダー PatchOrchestrationApplication と同じディレクトリに保存してください。

## <a name="viewing-the-windows-update-results"></a>Windows Update の結果の確認

パッチ オーケストレーション アプリケーションは、結果の履歴をユーザーに表示するための REST API を公開しています。

Windows Update の結果を照会するには、クラスターにログインして、コーディネーター サービスのプライマリのレプリカ アドレスを調べ、その URL をブラウザーから入力します。
(http://&lt;REPLICA-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults)。CoordinatorService の REST エンドポイントのポートは動的に変化するため、実際の URL を調べるために、Service Fabric Explorer を参照してください。
たとえば以下の例であれば、`http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults` で結果を確認できます。
![REST エンドポイントの画像](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


クラスターでリバース プロキシが有効になっている場合は、クラスターの外部から URL にアクセスすることもできます。
ブラウザーから入力すべきエンドポイントは http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults です。クラスターでリバース プロキシを有効にするには、[こちら](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)の手順に従ってください。 

> 
> [!WARNING]
> リバース プロキシの構成後は、クラスター内のマイクロ サービスのうち、HTTP エンドポイントを公開するすべてのサービスが、クラスターの外からアドレス可能となります。

## <a name="diagnostics--health-events"></a>診断/正常性イベント

### <a name="collecting-patch-orchestration-application-logs"></a>パッチ オーケストレーション アプリケーションのログの収集

パッチ オーケストレーション アプリケーションのログは近日中に、Service Fabric のログの一部として収集されるようになる予定です。
それまでは、次のいずれかの方法でログを収集してください。

#### <a name="locally-on-each-node"></a>個々のノードのローカル

Service Fabric クラスターの各ノード上のローカルでログを収集します。 ログの場所は \[Service Fabric\_Installation\_Drive\]:\\PatchOrchestrationApplication\\logs です。

たとえば、Service Fabric が "D" ドライブにインストールされている場合、このパスは D:\\PatchOrchestrationApplication\\logs になります。

#### <a name="central-location"></a>中央の場所

前提条件の手順を実行する過程で WAD を構成した場合、パッチ オーケストレーション アプリケーションのログが Azure Storage に格納されます。

### <a name="health-reports"></a>正常性レポート

パッチ オーケストレーション アプリケーションの正常性レポートは、CoordinatorService または NodeAgentService に対しても公開されます。

#### <a name="windows-update-operation-failed"></a>Windows Update の処理に失敗した場合

いずれかのノードで Windows Update の処理に失敗した場合、NodeAgentService に対する正常性レポートが生成されます。
正常性レポートの詳細情報に、問題のあるノードの名前が記録されます。

問題のあるノードに対してパッチが正常に適用されると、レポートは自動的に消去されます。

#### <a name="node-agent-ntservice-is-down"></a>ノード エージェント NTService がダウンしている場合

いずれかのノードで NodeAgentNTService がダウンしている場合、NodeAgentService に対して警告レベルの正常性レポートが生成されます。

#### <a name="repair-manager-is-not-enabled"></a>Repair Manager が有効になっていない場合

クラスターで Repair Manager サービスが検出されなかった場合、CoordinatorService に対して警告レベルの正常性レポートが生成されます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問:

Q. **パッチ オーケストレーション アプリケーションの実行中にクラスターがエラー状態として表示されます**

A. インストール処理中は、パッチ オーケストレーション アプリケーションによって、ノードが無効にされたり再起動されたりするために、クラスターの正常性が一時的に低下する場合があります。

アプリケーションのポリシーに応じて、パッチ適用処理中にいずれか 1 つのノードがダウンするか、アップグレード ドメイン全体が同時にダウンすることがあります。

Windows Update のインストールが完了するまでには、ノードが再起動された後に再度有効になります。

たとえば以下のケースでは、クラスターが一時的にエラー状態となります。ダウンしているノードが 2 つ存在し、MaxPercentageUnhealthNodes ポリシーの違反となるためです。 これはパッチ適用処理が完了するまでの一時的なエラーです。

![異常なクラスターの画像](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

問題が解消しない場合は、トラブルシューティングのセクションを参照してください。

Q. **パッチ オーケストレーション アプリケーションをスタンドアロン クラスターに使用することはできますか**

A. いいえ、まだ使用できません。スタンドアロン クラスターには近日中に対応予定です。

Q. **パッチ オーケストレーション アプリケーションが警告状態です**

A. このアプリケーションに対してポストされた正常性レポートが根本的な原因であるかどうかを確認してください。 通常、警告には問題の詳細が含まれています。
一過性の問題であれば、アプリケーションはこの状態から自動的に復旧すると考えられます。

Q. **クラスターは異常な状態です。しかしすぐに OS の更新を実行する必要があります**

A. クラスターに異常がある間は、パッチ オーケストレーション アプリケーションによって更新プログラムがインストールされません。
パッチ オーケストレーション アプリケーションのワークフローを再開させるために、クラスターを正常な状態にしてください。

Q. **クラスター全体にパッチを適用する作業は、なぜそれほど時間がかかるのでしょうか**

A. パッチ オーケストレーション アプリケーションの所要時間は多くの場合、以下の要因に左右されます。

- CoordinatorService のポリシー - 既定のポリシー (`NodeWise`) では、パッチ適用が 1 ノードずつしか実行されません。クラスター全体にパッチが適用されるまでの時間を短縮するために、特に大きなクラスターでは `UpgradeDomainWise` ポリシーの使用をお勧めします。
- ダウンロードしてインストールする更新プログラムの数 -  更新プログラムをダウンロードしてインストールするのにかかる平均時間が 2 ～ 3 時間を超えることは通常ありません。
- ネットワーク帯域幅と VM のパフォーマンス

## <a name="disclaimers"></a>免責事項

- Windows Update の EULA への同意は、ユーザーに代わってパッチ オーケストレーション アプリケーションが行います。 この設定は、アプリケーションの構成で必要に応じてオフにすることができます。

- パッチ オーケストレーション アプリケーションは、使用状況とパフォーマンスを追跡するためにテレメトリを収集します。
    アプリケーションのテレメトリの設定は、Service Fabric のランタイムのテレメトリ設定 (既定ではオン) に従います。

## <a name="troubleshooting-help"></a>トラブルシューティングのヘルプ

### <a name="node-not-coming-back-to-up-state"></a>ノードが稼働状態に戻らない

**ノードが無効状態になろうとして立ち往生している** 安全性チェックが実行待ちになっているために、処理の対象としてスケジューリングされているノードを無効にできない場合、この状況に陥る可能性があります。 この状況を改善するためには、十分な数のノードが正常状態で稼働するように対策を講じてください。

**ノードが無効状態で立ち往生する原因として考えられる理由**

- ノードが手動で無効化された。
- 進行中の Azure インフラストラクチャ ジョブによってノードが無効化された。
- ノードにパッチを適用するために、パッチ オーケストレーション アプリケーションがそのノードを一時的に無効にした。

**ノードがダウン状態で立ち往生する原因として考えられる理由**

- ノードが手動でダウン状態にされた。
- ノードの再起動が進行中である (パッチ オーケストレーション アプリケーションによって再起動がトリガーされたなど)。
- VM/マシンの障害またはネットワーク接続の問題でノードがダウンしている。

### <a name="updates-were-skipped-on-some-nodes"></a>一部のノードで更新がスキップされた

パッチ オーケストレーション アプリケーションは、再スケジュール ポリシーに従って、Windows 更新プログラムのインストールを試みます。 サービスはアプリケーション ポリシーに従って、ノードを復旧し、更新をスキップしようと試みます。

このような場合、ノード エージェント サービスに対して警告レベルの正常性レポートが生成されます。
Windows Update の結果にも、考えられるエラーの理由が記録されます。

### <a name="health-of-the-cluster-goes-to-error-while-update-was-getting-installed"></a>更新プログラムのインストール中にクラスターがエラー状態になる

Windows Update の問題で特定のノード (または特定のアップグレード ドメイン) のアプリケーション/クラスターがダウン状態に陥った場合、パッチ オーケストレーション アプリケーションは、クラスターが正常な状態に戻るまで、それ以上 Windows Update の処理を続行しません。

Windows Update がどのような理由で、アプリケーション/クラスターの正常性に悪影響を及ぼしているのかを管理者が介入して把握する必要があります。

