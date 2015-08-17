<properties
   pageTitle="Service Fabric の信頼性の高いアクター 'ReliableDictionaryActorStateProvider' の構成の概要"
   description="Service Fabric の 'ReliableDictionaryActorStateProvider' という型のステートフル アクターの構成について"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/20/2015"
   ms.author="sumukhs"/>

# 信頼性の高いアクターの構成 - ReliableDictionaryActorStateProvider
ReliableDictionaryActorStateProvider の既定の構成は、対象の指定されたアクターの "Config" フォルダーの下にある Visual Studio パッケージ ルートで生成された "settings.xml" ファイルを変更することで変更できます。

Service Fabric ランタイムは "settings.xml" ファイルで定義済みのセクション名を検索し、基になるランタイム コンポーネントの作成中に構成値を使用します。

> [AZURE.NOTE]Visual Studio ソリューションで生成される "settings.xml" ファイルでは、次の構成のセクション名を削除/変更**しない**でください。

## レプリケーターのセキュリティ構成
レプリケーション時に使用される通信チャネルをセキュリティ保護する場合は、レプリケーターのセキュリティ構成を使用します。これは、サービスは互いのレプリケーション トラフィックを表示できないため、高可用性データもセキュリティ保護されることを意味します。既定では、空のセキュリティ構成セクションでレプリケーション セキュリティは有効になりません。
### セクション名
&lt;ActorName&gt;ServiceReplicatorSecurityConfig
### 構成名
「[レプリケーションのセキュリティ](../service-fabric/service-fabric-replication-security.md)」を参照してください。

## レプリケーター構成
状態をローカルにレプリケートして永続化することでアクターの状態プロバイダーの状態の信頼性を高める必要があるレプリケーターを構成する場合は、レプリケーター構成を使用します。既定の構成は Visual Studio テンプレートによって生成され、これで十分なはずです。このセクションでは、レプリケーターのチューニングに使用できる追加の構成について説明します。
### セクション名
&lt;ActorName&gt;ServiceReplicatorConfig
### 構成名

|名前|単位|既定値|解説|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|秒|0\.05|操作を受信してからプライマリに受信確認を返すまで、セカンダリでレプリケーターが待機する期間です。この期間内で処理された操作に対して送信される他の受信確認は、1 つの応答として送信されます。||
|ReplicatorEndpoint|該当なし|該当なし - RequiredParameter|プライマリとセカンダリのレプリケーターがレプリカ セットの他のレプリケーターと通信するために使用する IP アドレスとポートです。これは、サービス マニフェストの TCP リソース エンドポイントを参照する必要があります。サービス マニフェストでのエンドポイント リソース定義の詳細については、「[サービス マニフェストのリソース](service-fabric-service-manifest-resources.md)」を参照してください。 |
|RetryInterval|秒|5|レプリケーターが操作の受信確認を受信しなかった場合に、メッセージを再送信するまでの期間です。|
|MaxReplicationMessageSize|バイト|50 MB|1 つのメッセージで送信できるレプリケーション データの最大サイズです。|
|MaxPrimaryReplicationQueueSize|操作数|1024|プライマリ キューの操作の最大数です。プライマリ レプリケーターがすべてのセカンダリ レプリケーターから受信確認を受信した後に、操作は解放されます。この値は 64 より大きく、2 のべき乗である必要があります。|
|MaxSecondaryReplicationQueueSize|操作数|2048|セカンダリ キューの操作の最大数です。操作は、永続性によってその状態の高可用性が実現されてから解放されます。この値は 64 より大きく、2 のべき乗である必要があります。|
|MaxStreamSizeInMB|MB|1024|予約されているログ ファイル領域の量。このサイズは MaxRecordSize (バイト単位) の 16 倍を超えている必要があります。|
|MaxRecordSizeInKB|KB|1024|レプリケーターがログに書き込むことができるレコードの最大サイズです。この値は 4 の倍数で 16 より大きい必要があります。|
|OptimizeForLocalSSD|ブール値|false|true の場合、状態情報がレプリカの専用ログ ファイルに直接書き込まれるように、ログが構成されます。これにより、ログ ファイルはソリッド ステート ディスク メディアにあるか、VM ディスクの IO 率が高度に調整されるときに最適なパフォーマンスを発揮します。false の場合、状態情報は最初に共有ログ ファイルにステージングされた後、専用ログ ファイルにステージング解除されます。|
|OptimizeLogForLowerDiskUsage|ブール値|false|true の場合、NTFS スパース ファイルを使用してレプリカの専用ログ ファイルが作成されるように、ログが構成されます。これにより、ファイルで使用される実際のディスク領域が削減されます。false の場合、最適な書き込みパフォーマンスを提供する固定の割り当てがファイルに作成されます。|
|SharedLogId|guid|""|このレプリカで使用される共有ログ ファイルの識別に使用する一意の guid を指定します。一般にサービスはこの設定を使用しないはずですが、SharedLogId を指定した場合は、SharedLogPath も指定する必要があります。|
|SharedLogPath|完全修飾パス名|""|このレプリカの共有ログ ファイルが作成される完全修飾パスを指定します。一般にサービスはこの設定を使用しないはずですが、SharedLogPath を指定した場合は、SharedLogId も指定する必要があります。|


## サンプル構成ファイル

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="MaxStreamSizeInMB" Value="512" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
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

## 解説
BatchAcknowledgementInterval は、レプリケーションの待機時間を制御します。値が '0' の場合、待機時間は最短になりますが、スループットに影響します (送信および処理が必要な受信確認メッセージが増え、それぞれに含まれる受信確認が少なくなります)。BatchAcknowledgementInterval の値が大きいほど、全体的なレプリケーションのスループットが高くなり、操作の待機時間が長くなります。これは、トランザクションのコミットの待機時間に直結します。

MaxStreamSizeInMB の値により、レプリケーターが、レプリカの専用ログ ファイルの状態情報の保存に使用できるディスク領域の量が決定されます。保存されている情報の状態を使用して、別のレプリカがプライマリ レプリカの状態と照合できるため、通常は大きいログ ファイルを使用することをお勧めします。これによって、他のレプリカがプライマリの状態の照合にかかる時間が短縮されるためです。ただし、ログ ファイルが大きくなると、使用されるディスク領域が増えるため、特定のノードでホストできるレプリカの数が少なくなる場合があります。

OptimizeForLowerDiskUsage の設定により、ログ ファイルの領域が "過剰プロビジョニング" されるため、アクティブなレプリカはログ ファイルにより多くの状態情報を保存できますが、非アクティブなレプリカで使用されるディスク領域は少なくなります。これにより、ディスク領域の不足が原因で発生するよりも多くのレプリカをノードにホストできるようになりますが、OptimizeForLowerDiskUsage を false に設定すれば状態情報がログ ファイルにより迅速に書き込まれます。

OptimizeForLocalSSD 設定は状態情報を専用ログ ファイルにステージング解除する前に、共有ログ ファイルへの書き込みを無効にするために使用されます。これは通常、ディスク ヘッドの移動の最小化が問題ではなく、専用ログ ファイルのローカル ディスク ストレージがソリッド ステート メディアになる場合に設定する必要があります。また、VM が大量のローカル ディスク IO を実行しており、ローカル ディスク IO 率が VM ホストによって大幅に調整されている場合にも使用できます。

MaxRecordSizeInKB は、レプリケーターがログ ファイルに書き込むことのできるレコードの最大サイズを定義します。ほとんどすべてのケースで、最適なレコードのサイズは既定の 1,024 KB ですが、サービスで大きなデータ項目を状態情報に含めようとしている場合、この値を増やさなければならない場合があります。小さいレコードは小さいレコードに必要な領域しか使用しないため、MaxRecordSizeInKB を 1024 より小さくしてもほとんど効果はありません。これを変更する必要があるのは、まれなケースだけであると予想されます。

SharedLogId と SharedLogPath の設定は、常に一緒に使用して、サービスがノードの既定の共有ログとは別の共有ログを使用できるようにします。最適な効率を得るため、できるだけ多くのサービスで同じ共有ログを指定してください。共有ログ ファイルは、ヘッドの移動の競合が減るように、共有ログ ファイル専用に使用されるディスクに配置する必要があります。これを変更する必要があるのは、まれなケースだけであると予想されます。
 

<!---HONumber=August15_HO6-->