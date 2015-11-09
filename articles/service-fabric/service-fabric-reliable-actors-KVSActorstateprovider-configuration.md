<properties
   pageTitle="Service Fabric の信頼性の高いアクター KVSActorStateProvider の構成の概要"
   description="Service Fabric の 'KVSActorStateProvider' という型のステートフル アクターの構成について説明します。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="sumukhs"/>

# 信頼性の高いアクター KVSActorStateProvider の構成
KVSActorStateProvider の既定の構成は、対象の指定されたアクターの "Config" フォルダーの下にある Visual Studio パッケージ ルートで生成された "settings.xml" ファイルを変更することで変更できます。

Service Fabric ランタイムは "settings.xml" ファイルで定義済みのセクション名を検索し、基になるランタイム コンポーネントの作成中に構成値を使用します。

> [AZURE.NOTE]Visual Studio ソリューションで生成される "settings.xml" ファイルでは、次の構成のセクション名を削除/変更**しない**でください。

## レプリケーターのセキュリティ構成
レプリケーション時に使用される通信チャネルをセキュリティ保護する場合は、レプリケーターのセキュリティ構成を使用します。これは、サービスは互いのレプリケーション トラフィックを表示できないため、高可用性データもセキュリティ保護されることを意味します。既定では、空のセキュリティ構成セクションでレプリケーション セキュリティは有効になりません。
### セクション名
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## レプリケーター構成
アクター状態プロバイダーの状態の信頼性を高める必要があるレプリケーターを構成する場合は、レプリケーター構成を使用します。既定の構成は Visual Studio テンプレートによって生成され、これで十分なはずです。このセクションでは、レプリケーターのチューニングに使用できる追加の構成について説明します。
### セクション名
&lt;ActorName&gt;ServiceReplicatorConfig
### 構成名

|名前|単位|既定値|解説|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|秒|0\.05|操作を受信してからプライマリに受信確認を返すまで、セカンダリでレプリケーターが待機する期間です。この期間内で処理された操作に対して送信される他の受信確認は、1 つの応答として送信されます。|
|ReplicatorEndpoint|該当なし|該当なし - RequiredParameter|プライマリとセカンダリのレプリケーターがレプリカ セットの他のレプリケーターと通信するために使用する IP アドレスとポートです。これは、サービス マニフェストの TCP リソース エンドポイントを参照する必要があります。サービス マニフェストでのエンドポイント リソース定義の詳細については、「[サービス マニフェストのリソース](service-fabric-service-manifest-resources.md)」を参照してください。 |
|RetryInterval|秒|5|レプリケーターが操作の受信確認を受信しなかった場合に、メッセージを再送信するまでの期間です。|
|MaxReplicationMessageSize|バイト|50 MB|1 つのメッセージで送信できるレプリケーション データの最大サイズです。|
|MaxPrimaryReplicationQueueSize|操作数|1024|プライマリ キューの操作の最大数です。プライマリ レプリケーターがすべてのセカンダリ レプリケーターから受信確認を受信した後に、操作は解放されます。この値は 64 より大きく、2 のべき乗である必要があります。|
|MaxSecondaryReplicationQueueSize|操作数|2048|セカンダリ キューの操作の最大数です。操作は、永続性によってその状態の高可用性が実現されてから解放されます。この値は 64 より大きく、2 のべき乗である必要があります。|

## ストア構成
レプリケート状態を保つために使用されるローカル ストアを構成する場合は、ストア構成を使用します。既定の構成は Visual Studio テンプレートによって生成され、これで十分なはずです。このセクションでは、ローカル ストアのチューニングに使用できる追加の構成について説明します。
### セクション名
&lt;ActorName&gt;ServiceLocalStoreConfig
### 構成名

|名前|単位|既定値|解説|
|----|----|-------------|-------|
|MaxAsyncCommitDelay|ミリ秒|200|持続性のあるローカル ストア コミットに、バッチ処理の最大間隔を設定します。|
|MaxVerPages|ページ数|8192|ローカル ストア データベースにおけるバージョン ページの最大数です。未処理のトランザクションの最大数を決定します。|

## サンプル構成ファイル

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
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

<!---HONumber=Nov15_HO1-->