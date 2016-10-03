<properties
   pageTitle="スタンドアロン Service Fabric クラスターでノードを追加または削除する | Microsoft Azure"
   description="Windows Server を実行する物理コンピューターまたは仮想マシン上で、Azure Service Fabric クラスターにノードを追加または削除する方法について説明します。追加先または削除元は、オンプレミスでも、任意のクラウドでもかまいません。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# Windows Server を実行するスタンドアロン Service Fabric クラスターでノードを追加または削除する

[Windows Server コンピューターでスタンドアロン Service Fabric クラスターを作成](service-fabric-cluster-creation-for-windows-server.md)した後に、ビジネス ニーズが変更されて、クラスターへの複数のノードの追加または削除が必要になるかもしれません。この記事では、これを実行する詳細の手順について説明します。


## クラスターへのノードの追加

1. [クラスターのデプロイのためにマシンの前提条件を満たす準備をする](service-fabric-cluster-creation-for-windows-server.md#preparemachines)セクションの手順に従って、クラスターに追加する VM/マシンを準備します。
2. この VM/マシンをどの障害ドメインとアップグレード ドメインに追加するかを計画します。
3. クラスターに追加する VM/マシンにリモート デスクトップ (RDP) 接続します。
4. この VM/マシンに [Windows Server 用の Service Fabric のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)またはコピーし、パッケージを解凍します。
5. 管理者として Powershell を実行し、解凍したパッケージのある場所に移動します。
6. 追加する新しいノードを記述したパラメーターを指定して、*AddNode.ps1* Powershell を実行します。次の例では、名前が VM5、タイプが NodeType0、IP アドレスが 182.17.34.52 の新しいノードを UD1 と FD1 に追加します。*ExistingClusterConnectionEndPoint* は、既存のクラスターに既にあるノードの接続エンドポイントです。このエンドポイントでは、クラスター内の "*任意*" のノードの IP アドレスを選択できます。

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClusterConnectionEndPoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA true
```

## クラスターからのノードの削除

1. クラスターから削除する VM/マシンにリモート デスクトップ (RDP) 接続します。
2. [Windows Server 用の Service Fabric のスタンドアロン パッケージをダウンロード](http://go.microsoft.com/fwlink/?LinkId=730690)またはコピーし、この VM/マシンにパッケージを解凍します。
3. 管理者として Powershell を実行し、解凍したパッケージのある場所に移動します。
4. *RemoveNode.ps1* Powershell を実行します。次の例では、現在のノードをクラスターから削除します。*ExistingClusterConnectionEndPoint* は、既存のクラスターに既にあるノードの接続エンドポイントです。このエンドポイントでは、クラスター内の "*任意*" のノードの IP アドレスを選択できます。

```
.\RemoveNode.ps1 -ExistingClusterConnectionEndPoint 182.17.34.50:19000
```


## 次のステップ
- [スタンドアロン Windows クラスターの構成設定](service-fabric-cluster-manifest.md)
- [Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する](service-fabric-windows-cluster-windows-security.md)
- [X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)
- [Windows を実行する Azure VM を使用してスタンドアロン Service Fabric クラスターを作成する](service-fabric-cluster-creation-with-windows-azure-vms.md)

<!---HONumber=AcomDC_0921_2016-->