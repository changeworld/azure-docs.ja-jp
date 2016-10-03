<properties
   pageTitle="Azure Service Fabric の Reliable Services の構成の概要 | Microsoft Azure"
   description="Azure Service Fabric のステートフル Reliable Services を構成する方法について説明します。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# ステートフル Reliable Services の構成

Reliable Services の構成設定には 2 つのセットがあります。1 つはクラスター内のすべての Reliable Services 用のグローバルな設定、もう 1 つは特定の Reliable Services に固有の設定です。

## グローバル構成

Reliable Services のグローバル構成は、クラスターのクラスター マニフェストの KtlLogger セクションで指定されています。この構成を使用すると、共有ログの場所とサイズに加えて、ロガーによって使用されるグローバル メモリ制限を構成できます。クラスター マニフェストは、クラスター内のすべてのノードとサービスに適用される設定と構成を保持する単一の XML ファイルです。通常、このファイルは ClusterManifest.xml という名前です。Get-ServiceFabricClusterManifest PowerShell コマンドを使用して、クラスターのクラスター マニフェストを確認できます。

### 構成名

|名前|単位|既定値|解説|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|キロバイト|8388608|ロガー書き込みバッファー メモリ プールに対してカーネル モードで割り当てる最小 KB 数。このメモリ プールは、ディスクに書き込む前の状態情報のキャッシュに使用されます。|
|WriteBufferMemoryPoolMaximumInKB|キロバイト|制限なし|ロガー書き込みバッファー メモリ プールを拡張できる最大サイズ。|
|SharedLogId|GUID|""|クラスター内のすべてのノードの Reliable Services のうち、サービス固有の構成で SharedLogId が指定されていないすべてのサービスによって使用される既定の共有ログ ファイルの指定に使用される、一意の GUID を指定します。SharedLogId を指定する場合は、SharedLogPath も指定する必要があります。|
|SharedLogPath|完全修飾パス名|""|クラスター内のすべてのノードの Reliable Services のうち、サービス固有の構成で SharedLogPath が指定されていないすべてのサービスによって使用される共有ログ ファイルの完全修飾パスを指定します。SharedLogPath を指定した場合は、SharedLogId も指定する必要があります。|
|SharedLogSizeInMB|メガバイト|8192|共有ログ用に静的に割り当てるディスク領域の MB 数を指定します。2048 以上の値を指定する必要があります。|

### クラスター マニフェストのセクションの例
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### 解説
ロガーには、非ページ カーネル メモリから割り当てられるメモリのグローバル プールがあります。ノード上のすべての Reliable Services は、Reliable Services レプリカに関連付けられている専用ログに書き込まれる前の状態データのキャッシュに、このプールを使用できます。プールのサイズは、WriteBufferMemoryPoolMinimumInKB および WriteBufferMemoryPoolMaximumInKB の設定によって制御されます。WriteBufferMemoryPoolMinimumInKB は、このメモリ プールの初期サイズと、メモリ プールを縮小できる最小サイズの両方を指定します。WriteBufferMemoryPoolMaximumInKB は、メモリ プールが拡大できる最大サイズです。開かれている各 Reliable Services レプリカは、WriteBufferMemoryPoolMaximumInKB 以下のシステムによって決定される大きさまで、メモリ プールのサイズを増やすことができます。メモリ プールのメモリをさらに使用する必要がある場合、メモリの要求はメモリが使用可能になるまで遅延されます。そのため、書き込みバッファー メモリ プールが特定の構成に対して小さすぎる場合、パフォーマンスが低下します。

SharedLogId と SharedLogPath の設定は常に一緒に使用されて、クラスター内のすべてのノードに対する既定の共有ログの GUID と場所を定義します。既定の共有ログは、settings.xml でサービス固有の設定が指定されていないすべての Reliable Services に使用されます。最善のパフォーマンスを得るには、競合が減るように、共有ログ ファイルを専用のディスクに配置する必要があります。

SharedLogSizeInMB では、すべてのノードで既定の共有ログに前もって割り当てるディスク領域の量を指定します。SharedLogSizeInMB を指定するために SharedLogId と SharedLogPath を指定する必要はありません。


## サービス固有の構成
ステートフル Reliable Services の既定の構成は、構成パッケージ (Config) か、サービス実装 (コード) を通じて変更できます。

+ **Config** - 構成パッケージを使用して構成する場合は、アプリケーションの各サービスの Config フォルダーの下にある Microsoft Visual Studio パッケージ ルートに生成された Settings.xml ファイルを変更します。
+ **コード** - コードを使用して構成する場合は、適切なオプションが設定された ReliableStateManagerConfiguration オブジェクトを使用して ReliableStateManager を作成します。

既定では、Azure Service Fabric ランタイムは settings.xml ファイルで定義済みのセクション名を検索し、基になるランタイム コンポーネントの作成中に構成値を使用します。

>[AZURE.NOTE] コードを使用してサービスを構成する予定でない限り、Visual Studio ソリューションで生成された Settings.xml ファイルの次の構成のセクション名は削除**しない**でください。構成パッケージまたはセクションの名前を変更するには、ReliableStateManager の構成時にコードを変更する必要があります。


### レプリケーターのセキュリティ構成
レプリケーション時に使用される通信チャネルをセキュリティ保護する場合は、レプリケーターのセキュリティ構成を使用します。これは、サービスは互いのレプリケーション トラフィックを表示できないため、高可用性データもセキュリティ保護されることを意味します。既定では、セキュリティ構成セクションが空の場合、レプリケーション セキュリティは有効になりません。

### 既定のセクション名
ReplicatorSecurityConfig

>[AZURE.NOTE] このセクション名を変更するには、このサービスの ReliableStateManager を作成するときに replicatorSecuritySectionName パラメーターを ReliableStateManagerConfiguration コンストラクターにオーバーライドします。


### レプリケーター構成
レプリケーター構成は、状態をローカルにレプリケートして永続化することでステートフル Reliable Service の状態の信頼性を高める役割を持つレプリケーターを構成します。既定の構成は Visual Studio テンプレートによって生成され、これで十分なはずです。このセクションでは、レプリケーターのチューニングに使用できる追加の構成について説明します。

### 既定のセクション名
ReplicatorConfig

>[AZURE.NOTE] このセクション名を変更するには、このサービスの ReliableStateManager を作成するときに replicatorSettingsSectionName パラメーターを ReliableStateManagerConfiguration コンストラクターにオーバーライドします。


### 構成名
|名前|単位|既定値|解説|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Seconds|0\.015|操作を受信してからプライマリに受信確認を返すまで、セカンダリでレプリケーターが待機する期間です。この期間内で処理された操作に対して送信される他の受信確認は、1 つの応答として送信されます。|
|ReplicatorEndpoint|該当なし|既定値なし - 必須パラメーター|プライマリとセカンダリのレプリケーターがレプリカ セットの他のレプリケーターと通信するために使用する IP アドレスとポートです。これは、サービス マニフェストの TCP リソース エンドポイントを参照する必要があります。サービス マニフェストでのエンドポイント リソース定義の詳細については、[サービス マニフェストのリソース](service-fabric-service-manifest-resources.md)に関する記事を参照してください。 |
|MaxPrimaryReplicationQueueSize|操作数|8192|プライマリ キューの操作の最大数です。操作は、プライマリ レプリケーターがすべてのセカンダリ レプリケーターから受信確認を受信した後に解放されます。この値は 64 より大きく、2 のべき乗である必要があります。|
|MaxSecondaryReplicationQueueSize|操作数|16384|セカンダリ キューの操作の最大数です。操作は、永続性によってその状態の高可用性が実現されてから解放されます。この値は 64 より大きく、2 のべき乗である必要があります。|
|CheckpointThresholdInMB|MB|50|その後で状態がチェックポイントされるログ ファイル領域の量。|
|MaxRecordSizeInKB|KB|1024|レプリケーターがログに書き込むことができるレコードの最大サイズです。この値は 4 の倍数で 16 より大きい必要があります。|
|SharedLogId|GUID|""|このレプリカで使用される共有ログ ファイルの識別に使用する一意の GUID を指定します。通常、サービスではこの設定を使用しないはずですが、SharedLogId を指定した場合は、SharedLogPath も指定する必要があります。|
|SharedLogPath|完全修飾パス名|""|このレプリカの共有ログ ファイルが作成される完全修飾パスを指定します。通常、サービスではこの設定を使用しないはずですが、SharedLogPath を指定した場合は、SharedLogId も指定する必要があります。|
|SlowApiMonitoringDuration|Seconds|300|マネージ API 呼び出しの監視間隔を設定します。たとえば、バックアップのコールバック関数を用意しておき、一定時間が経過したときに、警告の状態レポートを Health Manager に送信します。|

### コードによるサンプル構成
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### サンプル構成ファイル
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### 解説
BatchAcknowledgementInterval は、レプリケーションの待機時間を制御します。値が '0' の場合、待機時間は最短になりますが、スループットに影響します (送信および処理が必要な受信確認メッセージが増え、それぞれに含まれる受信確認が少なくなります)。BatchAcknowledgementInterval の値が大きいほど、全体的なレプリケーションのスループットが高くなり、操作の待機時間が長くなります。これは、トランザクションのコミットの待機時間に直結します。

CheckpointThresholdInMB の値により、レプリケーターがレプリカの専用ログ ファイルの状態情報の保存に使用できるディスク領域の量を制御します。この値を既定値よりも大きくすると、セットに新しいレプリカを追加したときの再構成時間が短くなります。これは、ログで使用できる操作履歴が増えることで部分的な状態転送が発生するためです。ただし、クラッシュ後のレプリカの復旧時間は長くなる可能性があります。

MaxRecordSizeInKB 設定は、レプリケーターがログ ファイルに書き込むことのできるレコードの最大サイズを定義します。ほとんどすべてのケースで、最適なレコードのサイズは既定の 1,024 KB ですが、サービスで大きなデータ項目を状態情報に含めようとしている場合、この値を増やさなければならない場合があります。小さいレコードは小さいレコードに必要な領域しか使用しないため、MaxRecordSizeInKB を 1024 より小さくしてもほとんど効果はありません。これを変更する必要があるのは、まれなケースだけであると予想されます。

SharedLogId と SharedLogPath の設定は常に一緒に使用して、サービスがノードの既定の共有ログとは別の共有ログを使用できるようにします。最適な効率を得るため、できるだけ多くのサービスで同じ共有ログを指定してください。共有ログ ファイルは、ヘッドの移動の競合が減るように、共有ログ ファイル専用に使用されるディスクに配置する必要があります。これを変更する必要があるのは、まれなケースだけであると予想されます。

## 次のステップ
 - [Visual Studio での Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)
 - [Reliable Services の開発者向けリファレンス](https://msdn.microsoft.com/library/azure/dn706529.aspx)

<!---HONumber=AcomDC_0921_2016-->