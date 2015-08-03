<properties
   pageTitle="Sevice Fabric サービス マニフェストのリソース"
   description="サービス マニフェストにリソースを記述する方法"
   services="service-fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="sumukhs"/>

# サービス マニフェストのリソース

## 概要

サービス マニフェストを使用すると、コンパイルしたコードを変更することなく、サービスで使用するリソースを宣言/変更できます。Service Fabric は、サービスで使用するエンドポイント リソースの構成をサポートします。サービス マニフェストで指定したリソースへのアクセスは、SecurityGroup を使用してアプリケーション マニフェスト内で制御できます。リソースを宣言すると、宣言したリソースをデプロイメント時に変更できるため、サービスに新しい構成メカニズムを導入する必要がありません。

## エンドポイント

サービス マニフェストにエンドポイント リソースが定義されている場合、Service Fabric は、予約済みのアプリケーション ポートの範囲からポートを割り当てます。さらに、サービスでリソースの特定のポートを要求することもできます。別のクラスター ノードで実行しているサービスのレプリカに異なるポート番号を割り当てることができます。一方、同じノードで実行している同じサービスのレプリカは、同じポートを共有します。このようなポートは、レプリケーションやクライアント要求のリッスンなどのさまざまな目的のために、サービスのレプリカで使用できます。

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

構成パッケージ設定ファイル (settings.xml) からエンドポイントを参照する方法の詳細については、「[信頼性の高いステートフル サービスの構成](../Service-Fabric/service-fabric-reliable-services-configuration.md)」を参照してください。

## サンプル
以下のサービス マニフェストは、1 個の TCP エンドポイント リソースと 2 個の HTTP エンドポイント リソース を &lt;Resources&gt; 要素内に定義しています。

HTTP エンドポイントは Service Fabric によって自動的に ACL に登録されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=July15_HO4-->