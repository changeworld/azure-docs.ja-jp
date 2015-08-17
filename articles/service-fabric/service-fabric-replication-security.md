<properties
   pageTitle="Azure Service Fabric のステートフル サービスのセキュリティで保護されたレプリケーション トラフィック"
   description="レプリケーションが有効な場合、ステートフル サービスは、プライマリ レプリカからセカンダリ レプリカに状態をレプリケートします。このようなトラフィックは、傍受や改ざんから保護する必要があります。"
   services="service-fabric"
   documentationCenter=".net"
   authors="leikong"
   manager="vipulm"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="leikong"/>

# セキュリティで保護されたレプリケーション トラフィック

レプリケーションが有効な場合、ステートフルなサービスは、レプリカの間で状態をレプリケートします。このページでは、このようなトラフィックの保護を構成する方法について説明します。

サポートされているセキュリティ設定には、次の 2 種類があります。

- X 509: 通信チャネルをセキュリティで保護するには、X 509 証明書を使用します。アクター/サービスのホスト プロセスが証明書の資格情報を使用できるように、秘密キーを ACL で認証することが求められます。
- Windows: 通信チャネルをセキュリティで保護するには、Windows セキュリティを使用します (Active Directory が必要です)。

次のセクションでは、上記の 2 種類の設定を構成する方法を示します。"CredentialType"の構成は、使用している種類を決定します。

## CredentialType=X509

### 構成名

|名前|解説|
|----|-------|
|StoreLocation|ストアの証明書を検索する場所: CurrentUser または LocalMachine|
|StoreName|証明書を検索するストアの名前|
|FindType|FindValue パラメーター (FindBySubjectName または FindByThumbPrint) で指定された値の型を識別します。|
|FindValue|証明書を検索する対象|
|AllowedCommonNames|レプリケーターによって使用される証明書の共通名/DNA 名のコンマ区切りリスト。|
|IssuerThumbprints|発行者の証明書に対する拇印のコンマ区切りリスト。指定した場合、このリスト内のエントリのいずれかによって発行されていれば、受信証明書が検証されます。チェーンの検証は、常に実行されます。|
|RemoteCertThumbprints|レプリケーターによって使用される証明書の拇印のコンマ区切りリスト。|
|ProtectionLevel|データを保護する方法を示します (Sign または EncryptAndSign または None)。|

## CredentialType=Windows

### 構成名

|名前|解説|
|----|-------|
|ServicePrincipalName|サービスに登録されるサービス プリンシパル名サービス/アクターのホスト プロセスが、コンピューター アカウント (NetworkService、LocalSystem など) として実行される場合は、空にすることができます。|
|WindowsIdentities|すべてのサービスとアクターのホスト プロセスに対する Windows ID のコンマ区切りリスト。
|ProtectionLevel|データを保護する方法を示します (Sign または EncryptAndSign または None)。|

## サンプル

### 例 1: CredentialType=X509

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="X509" />
  <Parameter Name="FindType" Value="FindByThumbprint" />
  <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
  <Parameter Name="StoreLocation" Value="LocalMachine" />
  <Parameter Name="StoreName" Value="My" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
  <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
</Section>
```

### 例 2: CredentialType=Windows
このスニペットは、サービス/アクターのすべてのホスト プロセスが NetworkService または LocalSystem として実行される場合の例を示しています。ServicePrincipalName は空です。

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="" />
  <!--This machine group contains all machines in a cluster-->
  <Parameter Name="WindowsIdentities" Value="redmond\ClusterMachineGroup" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```

### 例 3: CredentialType = Windows
このスニペットは、サービス/アクターのすべてのホスト プロセスが、有効な ServicePrincipalName を持つグループ管理サービス アカウントとして実行される場合の例を示します。

```xml
<Section Name="SecurityConfig">
  <Parameter Name="CredentialType" Value="Windows" />
  <Parameter Name="ServicePrincipalName" Value="servicefabric/cluster.microsoft.com" />
  <--All actor/service host processes run as redmond\GroupManagedServiceAccount-->
  <Parameter Name="WindowsIdentities" Value="redmond\GroupManagedServiceAccount" />
  <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
</Section>
```
 

<!---HONumber=August15_HO6-->