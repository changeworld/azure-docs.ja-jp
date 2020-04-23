---
title: Azure Service Fabric のプローブ
description: アプリケーションとサービスのマニフェスト ファイルを使用して、Azure Service Fabric の Liveness Probe をモデル化する方法について説明します。
ms.topic: conceptual
ms.date: 3/12/2020
ms.openlocfilehash: 38f3888a29bf505b723d40bc7cd08fb0c7e29eff
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427443"
---
# <a name="liveness-probe"></a>Liveness Probe
Service Fabric 7.1 以降では、[コンテナー化された][containers-introduction-link]アプリケーションに対して Liveness Probe メカニズムがサポートされています。 Liveness Probe では、コンテナー化されたアプリケーションの活動性を通知できます。また、アプリケーションが適切なタイミングで応答しない場合は、再起動されます。
この記事では、マニフェスト ファイルを使用して Liveness Probe を定義する方法の概要を示します。

この記事を読み進める前に、[Service Fabric のアプリケーション モデル][application-model-link]と [Service Fabric のホスティング モデル][hosting-model-link]を十分に理解しておくことをお勧めします。

> [!NOTE]
> Liveness Probe は、NAT ネットワーク モードのコンテナーでのみサポートされています。

## <a name="semantics"></a>セマンティクス
指定できるのは、1 つのコンテナーにつき 1 つの Liveness Probe だけで、次のフィールドでその動作を制御できます。

* `initialDelaySeconds`:コンテナーが起動したときに、プローブの実行を開始するまでの最初の遅延 (秒)。 サポートされる値は int です。既定値は 0 です。 最小値は 0 です。

* `timeoutSeconds`:正常に完了しなかった場合に、プローブが失敗と見なされるまでの期間 (秒)。 サポートされる値は int です。既定値は 1 です。 最小値は 1 です。

* `periodSeconds`:プローブの間隔 (秒)。 サポートされる値は int です。既定値は 10 です。 最小値は 1 です。

* `failureThreshold`:FailureThreshold に達すると、コンテナーが再起動します。 サポートされる値は int です。既定値は 3 です。 最小値は 1 です。

* `successThreshold`:失敗した場合、プローブが成功と見なされるには、SuccessThreshold に対して正常に実行される必要があります。 サポートされる値は int です。既定値は 1 です。 最小値は 1 です。

コンテナーへのプローブは、一度に最大で 1 つになります。 プローブが **timeoutSeconds** で完了しない場合は、待機し続け、**failureThreshold** にカウントされます。 

さらに、ServiceFabric により、DeployedServicePackage について次のプローブ[正常性レポート][health-introduction-link]が生成されます。

* `Ok`:**successThreshold** に対してプローブが成功した場合、正常性は Ok として報告されます。

* `Error`:プローブ failureCount == **failureThreshold** の場合は、コンテナーを再起動する前に Error が報告されます。

* `Warning`: 
    1. プローブが失敗し、failureCount < **failureThreshold** の場合は、Warning が報告されます。 この正常性レポートは、failureCount が **failureThreshold** または **successThreshold** に達するまで維持されます。
    2. 失敗後に成功した場合でも、Warning が報告されますが、連続した成功が更新されます。

## <a name="specifying-liveness-probe"></a>Liveness Probe の指定

ServiceManifestImport の下の ApplicationManifest.xml でプローブを指定できます。

プローブは次のいずれかに指定できます。

1. HTTP
2. TCP
3. Exec 

## <a name="http-probe"></a>HTTP プローブ

HTTP プローブの場合、Service Fabric では、指定されたポートとパスに HTTP 要求が送信されます。 返されるコードが 200 以上、400 未満の場合は、成功が示されます。

HttpGet プローブを指定する方法の例を次に示します。

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

HttpGet プローブには、設定できる次の追加のプロパティがあります。

* `path`:HTTP 要求のアクセス パス。

* `port`:プローブのアクセス ポート。 指定できる範囲は 1 から 65535 です 必須。

* `scheme`:コード パッケージへの接続に使用するスキーム。 HTTPS に設定すると、証明書の検証はスキップされます。 既定では HTTP です。

* `httpHeader`:要求で設定するヘッダー。 これらは複数指定できます。

* `host`:接続先のホスト IP。

## <a name="tcp-probe"></a>TCP プローブ

TCP プローブの場合、Service Fabric では、指定されたポートを使用してコンテナー上のソケットを開こうとします。 接続を確立できる場合、プローブは成功と見なされます。 TCP ソケットを使用するプローブを指定する方法の例を次に示します。

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="exec-probe"></a>Exec プローブ

このプローブでは、exec をコンテナーに発行し、コマンドが完了するまで待機します。

> [!NOTE]
> Exec コマンドでは、コンマで区切られた文字列を取得します。 この例の次のコマンドは、Linux コンテナーで使用できます。
> Windows コンテナーを試す場合は、<Command>cmd</Command> を使用します。

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>次のステップ
関連情報については、次の記事を参照してください。
* [Service Fabric とコンテナー。][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

