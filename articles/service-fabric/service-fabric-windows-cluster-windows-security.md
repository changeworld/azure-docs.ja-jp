<properties
   pageTitle="Windows 上で実行されるクラスターをセキュリティで保護する | Microsoft Azure"
   description="Windows セキュリティを使用して、Windows 上で実行されるスタンドアロン クラスター上でノード間またはクライアントとノードの間のセキュリティを構成する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する

Service Fabric クラスターは、特に運用ワークロードが実行されている場合などに、許可なくアクセスされるのを防ぐためにセキュリティで保護する必要があります。この記事では、*ClusterConfig.JSON* ファイルの Windows セキュリティを使用して、ノード間またはクライアントとノードの間のセキュリティを構成する方法について説明しています。また、この記事は [Windows 上で実行されるスタンドアロン クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関する記事の、セキュリティ構成の手順に対応しています。Service Fabric における Windows セキュリティの使用の詳細については、[クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事を参照してください。

>[AZURE.NOTE]
ノード間のセキュリティは慎重に選ぶ必要があります。1 つのセキュリティを選んだ後で、別のセキュリティを使えるようにクラスターをアップグレードすることはできないからです。セキュリティの選択を変更するには、クラスターの完全な再構築が必要です。

## Windows セキュリティの構成
[Microsoft.Azure.ServiceFabric.WindowsServer.<バージョン>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) スタンドアロン クラスター パッケージに含まれるサンプル構成ファイルである *ClusterConfig.Windows.JSON* には、Windows セキュリティを構成するためのテンプレートが含まれています。Windows セキュリティは **Properties** セクション内で構成します。

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
		"ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**構成設定**|**説明**|
|-----------------------|--------------------------|
|ClusterCredentialType|**ClusterCredentialType** パラメーターを *Windows* に設定すると、Windows セキュリティが有効になります。|
|ServerCredentialType|**ServerCredentialType** パラメーターを *Windows* に設定すると、クライアントの Windows セキュリティが有効になります。これは、クラスターのクライアントおよびクラスター自体が Active Directory ドメイン内で実行されていることを示します。|
|WindowsIdentities|クラスターとクライアントの ID が含まれます。|
|ClusterIdentity|ノード間のセキュリティを構成します。グループ管理サービス アカウントまたはコンピューター名のコンマ区切りのリストです。|
|ClientIdentities|クライアントとノードの間のセキュリティを構成します。クライアントのユーザー アカウントの配列です。|
|ID|ドメイン ユーザーであるクライアントの ID。|
|IsAdmin|true の場合は、ドメイン ユーザーが管理者クライアント アクセスを持つことを示し、false の場合は、ユーザー クライアント アクセスを持つことを示します。|

[ノード間のセキュリティ](service-fabric-cluster-security.md#node-to-node-security)は **ClusterIdentity** を使って構成します。ノード間の信頼関係を構築するには、各ノードが互いを認識する必要があります。これを行うには 2 つの方法があります。クラスター内のすべてのノードを含むグループ管理サービス アカウントを指定する方法と、クラスター内のすべてのノードのドメイン ノード ID を指定する方法です。強くお勧めするのは、[グループ管理サービス アカウント (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) を使用する方法です。特に、クラスターが大きい場合 (ノードが 10 個以上) またはクラスターの拡大と縮小が予想される場合には、この方法が推奨されます。この方法では、クラスター マニフェストを変更せずに、gMSA へのノードの追加と削除を行うことが可能です。この方法なら、メンバーの追加と削除に必要なアクセス権がクラスター管理者から付与されたドメイン グループを作成する必要がありません。詳細については、「[グループの管理されたサービス アカウントの概要](http://technet.microsoft.com/library/jj128431.aspx)」を参照してください。

[クライアントとノードの間のセキュリティ](service-fabric-cluster-security.md#client-to-node-security)は **ClientIdentities** を使用して構成します。クライアントとクラスターの間の信頼を確立するためには、どのクライアントの ID なら信頼できるのかを認識できるようにクラスターを構成する必要があります。これを行うには 2 つの方法があります。接続可能なドメイン グループ ユーザーを指定する方法と、接続可能なドメイン ノード ユーザーを指定する方法です。Service Fabric では、Service Fabric クラスターに接続されるクライアントのために、管理者用とユーザー用の 2 つの異なるアクセス コントロールの種類がサポートされています。アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定の種類のクラスター操作へのアクセスを制限し、クラスターのセキュリティを強化できます。管理者には、管理機能へのフル アクセス権 (読み取り/書き込み機能など) があります。ユーザーには、既定で管理機能 (クエリ機能など) と、アプリケーションとサービスを解決する機能への読み取りアクセス権のみがあります。

次の **security** セクションの例では、Windows セキュリティを構成し、*ServiceFabric/clusterA.contoso.com* 内のコンピューターがクラスターに属することと、*CONTOSO\\usera* が管理者クライアント アクセスを持つことを指定しています。

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
		"ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## 次のステップ

*ClusterConfig.JSON* ファイルで Windows セキュリティを構成したら、[Windows 上で実行されるスタンドアロン クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関する記事で説明されているクラスター作成処理を再開します。

ノード間のセキュリティ、クライアントとノードの間のセキュリティ、ロールベースのアクセス制御の詳細については、[クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事を参照してください。

PowerShell または FabricClient を使用した接続の例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md)」を参照してください。

<!---HONumber=AcomDC_0831_2016-->