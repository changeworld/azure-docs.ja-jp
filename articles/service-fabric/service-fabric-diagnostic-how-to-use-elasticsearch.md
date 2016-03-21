<properties
   pageTitle="Service Fabric アプリケーション トレース ストアとして Elasticsearch を使用する | Microsoft Azure"
   description="Service Fabric アプリケーションで Elasticsearch と Kibana を使用してアプリケーション トレース (ログ) の格納、インデックス作成、および検索を行う方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="karolz@microsoft.com"/>

# Service Fabric アプリケーション トレース ストアとして ElasticSearch を使用する
## はじめに
この記事では、[Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric/) アプリケーションがアプリケーション トレース ストレージ、インデックス作成、および検索に **Elasticsearch** と **Kibana** を使用する方法について説明します。[Elasticsearch](https://www.elastic.co/guide/index.html) は、オープンソースの分散型でスケーラブルなリアルタイム検索および分析エンジンで、このタスクに適しています。Microsoft Azure で実行されている Windows および Linux 仮想マシンにインストールできます。Elasticsearch は、**Event Tracing for Windows (ETW)** などのテクノロジを使用して生成された*構造化*トレースを高い効率で処理できます。

ETW は、Service Fabric ランタイムが診断情報 (トレース) をソースにするために使用します。Service Fabric アプリケーションに対しても、診断情報をソースにする場合に推奨される方法です。この方法ではランタイムで提供されたトレースとアプリケーションから提供されたトレースを関連付けることができるので、トラブルシューティングが簡単になります。Visual Studio の Service Fabric プロジェクト テンプレートには、(.NET **EventSource** クラスに基づいて) 既定で ETW トレースを出力するロギング API が含まれています。ETW を使用した Service Fabric アプリケーション トレースの一般的な概要については、「[ローカル コンピューターの開発のセットアップでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)」を参照してください。

Elasticsearch に出現するトレースの場合、リアルタイムで (アプリケーションの実行中に) Service Fabric クラスター ノードでキャプチャし、Elasticsearch エンドポイントに送信する必要があります。トレースのキャプチャには、主に 2 つのオプションがあります。

+ **プロセス内のトレース キャプチャ**
アプリケーション (より正確にはサービス プロセス) が、診断データをトレース ストア (Elasticsearch) に送信する役割を担います。

+ **プロセス外のトレース キャプチャ** 
別のエージェントが 1 つ以上のサービス プロセスからトレースをキャプチャし、トレース ストアに送信します。

以下では、Azure で Elasticsearch を設定する方法、2 つのキャプチャ オプションの長所と短所、Elasticsearch にデータを送信する Service Fabric サービスを構成する方法について説明します。


## Azure で Elasticsearch を設定する
Azure で Elasticsearch サービスを設定するには、[**Azure リソース マネージャー テンプレート**](../resource-group-overview.md)を使用する方法が最も簡単です。Azure クイックスタート テンプレート リポジトリから、[Elasticsearch 用の包括的なクイックスタート Azure リソース マネージャー テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch)を入手できます。このテンプレートでは、スケール ユニット (ノードのグループ) ごとに異なるストレージ アカウントを使用します。構成と接続されているデータ ディスクの数が異なるクライアント ノードとサーバー ノードを個別にプロビジョニングすることもできます。

ここでは、[Microsoft patterns & practices ELK ブランチ](https://github.com/mspnp/semantic-logging/tree/elk/)の **ES-MultiNode** という別のテンプレートを使用します。このテンプレートは使い方が簡単で、既定で HTTP 基本認証で保護された Elasticsearch クラスターを簡単に作成できます。続行する前に、GitHub から [Microsoft patterns & practices ELK リポジトリ](https://github.com/mspnp/semantic-logging/tree/elk/)をコンピューターにダウンロードしてください (リポジトリを複製するか、zip ファイルをダウンロードします)。ES-MultiNode テンプレートは、同じ名前のフォルダーに格納されています。
>[AZURE.NOTE] 現在、ES-MultiNode テンプレートと関連するスクリプトは、Elasticsearch 1.7 リリースをサポートしています。今後、Elasticsearch 2.0 のサポートが追加される予定です。

### コンピューターで ElasticSearch のインストール スクリプトを実行するための準備をする
ES-MultiNode テンプレートを使用するには、提供されている `CreateElasticSearchCluster`という Azure PowerShell スクリプトを使用するのが最も簡単な方法です。このスクリプトを使用するには、PowerShell モジュールと、**openssl** というツールをインストールする必要があります。openssl は、Elasticsearch クラスターのリモート管理に使用できる SSH キーを作成するために必要です。

`CreateElasticSearchCluster` スクリプトは、Windows コンピューターから ES-MultiNode テンプレートを簡単に使用できるように設計されていることに注意してください。Windows 以外のコンピューターでもこのテンプレートを使用できますが、そのシナリオについては、この記事では説明しません。

1. まだインストールしていない場合は、[**Azure PowerShell モジュール**](http://go.microsoft.com/fwlink/p/?linkid=320376)をインストールします。メッセージが表示されたら、**[実行]**、**[インストール]** の順にクリックします。
>[AZURE.NOTE] Azure PowerShell は Azure PowerShell 1.0 リリースで大規模な変更が行われています。現在、CreateElasticSearchCluster は、Azure PowerShell 0.9.8 で動作するように設計されており、Azure PowerShell 1.0 プレビューをサポートしていません。今後、Azure PowerShell 1.0 互換のスクリプトが提供される予定です。

2. **openssl** ツールは、[**Git for Windows**](http://www.git-scm.com/downloads) のディストリビューションに含まれています。まだインストールしていない場合は、今すぐ [Git for Windows](http://www.git-scm.com/downloads) をインストールしてください (既定のインストール オプションで問題ありません)。

3. Git がインストール済みでも、システム パスに含まれていない場合は、Microsoft Azure PowerShell ウィンドウを開き、次のコマンドを実行します。

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    `<Git installation folder>` をコンピューターの Git の場所に置き換えます。既定値は **"C:\\Program Files\\Git"** です。最初のパスの先頭にセミコロン (;) がある点に注意してください。

4. ([**Add-AzureAccount**](https://msdn.microsoft.com/library/azure/dn790372.aspx) コマンドレットで) Azure にログオンし、Elasticsearch クラスターの作成に使用するサブスクリプションを選択します ([**Select-AzureSubscription**](https://msdn.microsoft.com/library/azure/dn790367.aspx))。

5. まだの場合は、現在のディレクトリを ES-MultiNode フォルダーに変更します。

### CreateElasticSearchCluster スクリプトを実行する
スクリプトを実行する前に、`azuredeploy-parameters.json` ファイルを開き、スクリプト パラメーターの値を確認するか指定します。次のパラメーターがあります。

|パラメーター名 |説明|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Elasticsearch クラスターのパブリックに参照できるドメイン ネーム システム (DNS) 名を作成するために使用される名前です (指定した名前に Azure リージョン ドメインを付加して作成します)。たとえば、このパラメーター値が "myBigCluster" で、選択した Azure リージョンが米国西部の場合、クラスターの DNS 名は **myBigCluster.westus.cloudapp.azure.com** になります。<br /><br />この名前は、Elasticsearch クラスターに関連付けられた多数のアーティファクトの名前のルートとしても使用されます (データ ノード名など)。|
|storageAccountPrefix |Elasticsearch クラスター用に作成されるストレージ アカウントのプレフィックス。<br /><br />最新バージョンのテンプレートは 1 つの共有ストレージ アカウントを使用しますが、今後は変更される可能性があります。|
|adminUsername |Elasticsearch クラスターを管理する管理者アカウントの名前です (対応する SSH キーは自動的に生成されます)。|
|dataNodeCount |Elasticsearch クラスター内のノード数。最新バージョンのスクリプトは、データとクエリ ノードを区別していません。すべてのノードが両方の役割を担います。|
|dataDiskSize |各データ ノードに割り当てられるデータ ディスクのサイズ (GB 単位)。各ノードは、Elasticsearch サービス専用の 4 個のデータ ディスクを受け取ります。|
|region |Elasticsearch クラスターが配置される Azure リージョンの名前。|
|esClusterName |Elasticsearch クラスターの内部名。<br /><br />同じ仮想ネットワークで複数の Elasticsearch クラスターを実行する予定がない場合は、この値を既定値から変更する必要があります。これは、現在の ES-MultiNode テンプレートではサポートされていません。|
|esUserName esPassword |Elasticsearch クラスターへのアクセス権を持つように構成されるユーザーの資格情報 (HTTP 基本認証の場合)。|

これで、スクリプトを実行する準備が整いました。次のコマンドを発行します。```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name>
``` この `<es-group-name>` は、Azure リソース グループ名です。すべてのクラスター リソースが含まれます。

>[AZURE.NOTE] Test-AzureResourceGroup コマンドレットで NullReferenceException が発生する場合、Azure にログオン (`Add-AzureAccount`) し忘れていることを示します。

スクリプトを実行してエラーが発生し、エラーの原因が誤ったテンプレート パラメーター値であると判断した場合は、パラメーター ファイルを修正し、別のリソース グループ名でスクリプトを再実行します。`-RemoveExistingResourceGroup` パラメーターをスクリプトの呼び出しに追加すると、同じリソース グループ名を再利用して、古いリソース グループ名を消去することもできます。

### CreateElasticSearchCluster スクリプトの実行結果
`CreateElasticSearchCluster` スクリプトを実行すると、次のメイン アーティファクトが作成されます。わかりやすくするために、`dnsNameForLoadBalancerIP` パラメーターの値に "myBigCluster" を使用し、クラスターを作成したリージョンが米国西部だとします。

|アーティファクト|名前、場所、備考|
|----------------------------------|----------------------------------|
|リモート管理用の SSH キー |myBigCluster.key ファイル (CreateElasticSearchCluster が実行されたディレクトリ内)。<br /><br />管理ノードと (管理ノード経由で) クラスター内のデータ ノードに接続するために使用できるキーです。|
|管理者ノード |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />Elasticsearch クラスターのリモート管理専用の VM です。外部 SSH 接続が可能な唯一の VM です。すべての Elasticsearch クラスター ノードと同じ仮想ネットワークで実行されますが、Elasticsearch サービスは実行されません。|
|データ ノード |myBigCluster1 … myBigCluster*N* <br /><br />Elasticsearch サービスと Kibana サービスを実行しているデータ ノードです。SSH で各ノードに接続できますが、管理者ノードを経由する必要があります。|
|Elasticsearch クラスター |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />上記の URL は、Elasticsearch クラスターのプライマリ エンドポイントです (/es サフィックスに注意してください)。基本 HTTP 認証で保護されます (資格情報は、ES-MultiNode テンプレートの esUserName/esPassword パラメーターで指定されました)。また、基本的なクラスター管理のために、head プラグイン (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) もインストールされています。|
|Kibana サービス |http://myBigCluster.westus.cloudapp.azure.com <br /><br />Kibana サービスは、作成された Elasticsearch クラスターのデータを表示するために設定されています。クラスター自体と同じ認証資格情報によって保護されます。|

## プロセス内とプロセス外のトレース キャプチャ
「はじめに」では、診断データを収集する 2 つの基本的な方法として、プロセス内とプロセス外の方法について触れました。それぞれに長所と短所があります。

**プロセス内のトレース キャプチャ**の長所は次のとおりです。

1. *構成とデプロイメントが容易*

    * 診断データ コレクションの構成は、アプリケーション構成のごく一部に過ぎません。アプリケーションのその他の部分と常に "同期を保つ" ことは簡単です。

    * アプリケーションごと、またはサービスごとの構成を簡単に実現できます。

    * 通常、プロセス外のトレース キャプチャの場合、診断エージェントの別のデプロイメントと構成が必要です。そのため、管理者の作業とエラーの潜在的な原因が増えます。多くの場合、特定のエージェント テクノロジは、仮想マシン (ノード) ごとにエージェントのインスタンスを 1 つだけ許容しています。つまり、診断構成のコレクションの構成は、そのノードで実行されているすべてのアプリケーションとサービスで共有されます。

2. *柔軟性*

    * 対象データ ストレージ システムをサポートするクライアント ライブラリがある場合、必要なときにいつでもアプリケーションからデータを送信できます。必要に応じて、新しいシンクを追加できます。

    * 複雑なキャプチャ、フィルター処理、データ集計ルールを実装できます。

    * 多くの場合、プロセス外のトレース キャプチャは、エージェントがサポートするデータ シンクによって制限されます。一部のエージェントは拡張可能です。

3. *内部アプリケーション データおよびコンテキストへのアクセス*

    * アプリケーション/サービス プロセス内で実行される診断サブシステムは、コンテキスト情報を使用してトレースを簡単に強化できます。

    * プロセス外のアプローチでは、Event Tracing for Windows などのなんらかのプロセス内通信メカニズムを使用してデータをエージェントに送信する必要があります。その結果、制限が増える可能性があります。

**プロセス外のトレース キャプチャ**の長所は次のとおりです。

1. *アプリケーションを監視し、クラッシュ ダンプを収集できる*

    * アプリケーションの起動に失敗するか、アプリケーションがクラッシュした場合、プロセス内のトレース キャプチャに成功しないことがあります。独立したエージェントの方が、重要なトラブルシューティング情報をキャプチャできる可能性が高くなります。<br /><br />

2. *成熟度、堅牢性、実証済みのパフォーマンス*

    * プラットフォーム ベンダーが開発したエージェント (Microsoft Azure 診断エージェントなど) は厳格なテストを受け、実際の環境で鍛えられています。

    * プロセス内のトレース キャプチャでは、アプリケーション プロセスから診断データを送信する処理がアプリケーションのメイン タスクを邪魔したり、タイミングやパフォーマンスの問題を起こしたりしないように気を付ける必要があります。独立して実行されるエージェントは、このような問題を起こす可能性が低いので、通常は特にシステムへの影響を軽減するために設計されます。

当然ながら、両方のアプローチを組み合わせて活用することもできます。実際、それが多くのアプリケーションに最適なソリューションです。

ここでは、**Microsoft.Diagnostic.Listeners library** とプロセス内のトレース キャプチャを使用して、Service Fabric アプリケーションから Elasticsearch クラスターにデータを送信します。

## Listeners ライブラリを使用して診断データを Elasticsearch に送信する
Microsoft.Diagnostic.Listeners ライブラリは、PartyCluster サンプル Service Fabric アプリケーションの一部です。使用方法:

1. GitHub から [PartyCluster サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster)をダウンロードします。

2. PartyCluster サンプル ディレクトリの Microsoft.Diagnostics.Listeners および Microsoft.Diagnostics.Listeners.Fabric プロジェクト (フォルダー全体) を、Elasticsearch にデータを送信する予定のアプリケーションのソリューション フォルダーにコピーします。

3. 対象のソリューションを開き、ソリューション エクスプローラーでソリューション ノードを右クリックし、**[既存プロジェクトの追加]** を選択します。Microsoft.Diagnostics.Listeners プロジェクトをソリューションに追加します。Microsoft.Diagnostics.Listeners.Fabric プロジェクトでも同じ手順を繰り返します。

4. サービス プロジェクトから 2 つの追加したプロジェクトへのプロジェクト参照を追加します (Elasticsearch にデータを送信する予定の各サービスは、Microsoft.Diagnostics.EventListeners と Microsoft.Diagnostics.EventListeners.Fabric を参照する必要があります)。

    ![Microsoft.Diagnostics.EventListeners および Microsoft.Diagnostics.EventListeners.Fabric ライブラリのプロジェクト参照][1]

### Service Fabric と Microsoft.Diagnostics.Tracing NuGet パッケージの 2015 年 11 月プレビュー
Service Fabric の 2015 年 11 月プレビューで作成されたアプリケーションは、**.NET Framework 4.5.1** を対象とします。これは、プレビュー リリースの時点で Azure がサポートする最新バージョンの .NET Framework です。残念ながら、このバージョンの .NET Framework には、Microsoft.Diagnostics.Listeners ライブラリに必要な EventListener API の一部が含まれていません。EventSource (Fabric アプリケーションのロギング API の基盤となるコンポーネント) と EventListener は密接に関連しているため、Microsoft.Diagnostics.Listeners ライブラリを使用するすべてのプロジェクトは、EventSource の代替の実装を使用する必要があります。この実装は、Microsoft が作成した **Microsoft.Diagnostics.Tracing NuGet パッケージ**に用意されています。このパッケージは、この .NET Framework に含まれている EventSource と完全に下位互換性があります。そのため、参照される名前空間を変更する以外に、コードの変更は必要ありません。

EventSource クラスの Microsoft.Diagnostics.Tracing 実装を使用する場合、データを Elasticsearch に送信する必要があるサービス プロジェクトごとに、次の手順を実行します。

1. サービス プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。

2. まだ選択していない場合は nuget.org パッケージ ソースに切り替え、"**Microsoft.Diagnostics.Tracing**" を検索します。

3. `Microsoft.Diagnostics.Tracing.EventSource` パッケージ (とその依存関係) をインストールします。

4. サービス プロジェクトで **ServiceEventSource.cs** または **ActorEventSource.cs** ファイルを開き、ファイルの先頭にある `using System.Diagnostics.Tracing` ディレクティブを `using Microsoft.Diagnostics.Tracing` ディレクティブに置き換えます。

Microsoft Azure で **.NET Framework 4.6** がサポートされると、これらの手順は不要になります。

### Elasticsearch リスナーのインストールと構成
診断データを Elasticsearch に送信するための最後の手順は、`ElasticSearchListener` のインスタンスを作成し、Elasticsearch 接続データで構成することです。リスナーは、サービス プロジェクトに定義されている EventSource クラスで発生したすべてのイベントを自動的にキャプチャします。サービスの有効期間中はリスナーが有効である必要があるので、サービス初期化コードで作成することをお勧めします。ステートレス サービスの初期化コードに必要な変更を加えた後の例を次に示します (追加部分のコメントには、先頭に `****` が付いています)。

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    public class Program
    {
        public static void Main(string[] args)
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {

                    // **** Instantiate ElasticSearchListener
                    var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                    ElasticSearchListener esListener = null;
                    if (configProvider.HasConfiguration)
                    {
                        esListener = new ElasticSearchListener(configProvider);
                    }

                    // This is the name of the ServiceType that is registered with FabricRuntime.
                    // This name must match the name defined in the ServiceManifest. If you change
                    // this name, please change the name of the ServiceType in the ServiceManifest.
                    fabricRuntime.RegisterServiceType("Stateless1Type", typeof(Stateless1));

                    ServiceEventSource.Current.ServiceTypeRegistered(
						Process.GetCurrentProcess().Id,
						typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);

                    // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                    GC.KeepAlive(esListener);

                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Elasticsearch 接続データは、サービス構成ファイル (**PackageRoot\\Config\\Settings.xml**) の別のセクションで指定する必要があります。セクション名は、`FabricConfigurationProvider` コンストラクターに渡される値に対応する必要があります。次に例を示します。

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
`serviceUri`、`userName`、`password` の値はそれぞれ、Elasticsearch クラスター エンドポイント アドレス、Elasticsearch ユーザー名、パスワードに対応します。`indexNamePrefix` は、Elasticsearch インデックスのプレフィックスです。Microsoft.Diagnostics.Listeners ライブラリは、1 日に 1 回、データの新しいインデックスを作成します。

### 確認
これで完了です。 これで、サービスを実行するたびに、構成に指定した Elasticsearch サービスにトレースが送信されるようになります。対象の Elasticsearch インスタンス (この例では、ページ アドレスは http://myBigCluster.westus.cloudapp.azure.com/) に関連付けられた Kibana UI を開き、`ElasticSearchListener` 用に選択した名前のプレフィックスが指定されたインデックスが実際に作成され、データが設定されていることを確認します。

![PartyCluster アプリケーション イベントを表示する Kibana][2]

## 次のステップ
- [Service Fabric サービスの診断と監視](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_0204_2016-->