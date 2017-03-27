---
title: "Windows セキュリティを使用して Windows 上で実行されるクラスターを保護する | Microsoft Docs"
description: "Windows セキュリティを使用して、Windows 上で実行されるスタンドアロン クラスターでノード間またはクライアントとノード間のセキュリティを構成する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 6294a6fac8748f70c807df76feb425cb627bf4c3
ms.lasthandoff: 03/15/2017


---
# <a name="secure-a-standalone-cluster-on-windows-by-using-windows-security"></a>Windows セキュリティを使用して Windows 上のスタンドアロン クラスターを保護する
Service Fabric クラスターへの未承認のアクセスを防ぐには、クラスターをセキュリティで保護する必要があります。 クラスターで運用環境のワークロードが実行されている場合は、セキュリティが特に重要となります。 この記事では、*ClusterConfig.JSON* ファイルで Windows セキュリティを使用して、ノード間およびクライアントとノード間のセキュリティを構成する方法について説明します。  このプロセスは、[Windows 上で実行されるスタンドアロン クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関する記事のセキュリティの構成手順に対応しています。 Service Fabric における Windows セキュリティの使用の詳細については、[クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事をご覧ください。

> [!NOTE]
> セキュリティを選択した後で別のセキュリティにクラスターをアップグレードすることはできないため、ノード間のセキュリティの選択は慎重に検討してください。 セキュリティの選択を変更するには、クラスター全体を再構築する必要があります。
>
>

## <a name="configure-windows-security"></a>Windows セキュリティの構成  
[Microsoft.Azure.ServiceFabric.WindowsServer<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) スタンドアロン クラスター パッケージと共にダウンロードされる *ClusterConfig.Windows.JSON* サンプル構成ファイルには、Windows セキュリティを構成するためのテンプレートが含まれています。 Windows セキュリティは **Properties** セクション内で構成します。

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

| **構成設定** | **説明** |
| --- | --- |
| ClusterCredentialType |Windows セキュリティを有効にするには、*Windows* に設定します。 |
| ServerCredentialType |クライアントの Windows セキュリティを有効にするには、*Windows* に設定します。<br /><br />これは、クラスターのクライアントとクラスター自体が Active Directory ドメイン内で実行されていることを示します。 |  
| WindowsIdentities |クラスターとクライアントの ID が含まれます。 |  
| ClusterIdentity |コンピューター グループ名 (domain\machinegroup) を使用して、ノード間のセキュリティを構成します。 |  
| ClientIdentities |クライアントとノードの間のセキュリティを構成します。 クライアントのユーザー アカウントの配列です。 |  
| ID |クライアント ID のドメイン ユーザー (domain\username) を追加します。 |  
| IsAdmin |true に設定すると、ドメイン ユーザーが管理者クライアント アクセスを持つことを示し、false に設定すると、ユーザー クライアント アクセスを持つことを示します。 |  

[ノード間のセキュリティ](service-fabric-cluster-security.md#node-to-node-security)は、**ClusterIdentity** を使用して構成します。 ノード間の信頼関係を構築するには、各ノードが相互に認識する必要があります。 相互に認識させるには、クラスターのすべてのノードを含むドメイン グループを作成します。 このグループの名前は **ClusterIdentity** で指定する必要があります。 詳細については、[Create a Group in Active Directory (Active Directory でのグループの作成)](https://msdn.microsoft.com/library/aa545347(v=cs.70).aspx) に関する記事をご覧ください。  

[クライアントとノード間のセキュリティ](service-fabric-cluster-security.md#client-to-node-security)は、**ClientIdentities** を使用して構成します。 クライアントとクラスター間の信頼を確立するには、クラスターが信頼できるクライアント ID を認識できるようにクラスターを構成する必要があります。 次の&2; つの方法で信頼を確立できます。

- 接続可能なドメイン グループ ユーザーを指定する。
- 接続可能なドメイン ノード ユーザーを指定する。

Service Fabric では、Service Fabric クラスターに接続されるクライアントのために、管理者用とユーザー用の&2; つの異なるアクセス コントロールの種類がサポートされています。 アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定の種類のクラスター操作へのアクセスを制限して、クラスターのセキュリティを強化できます。  管理者には、管理機能へのフル アクセス権 (読み取り/書き込み機能など) があります。 ユーザーには、既定で管理機能 (クエリ機能など) と、アプリケーションとサービスを解決する機能への読み取りアクセス権のみがあります。  

次の **security** セクションの例では、Windows セキュリティを構成し、*ServiceFabric/clusterA.contoso.com* 内のコンピューターがクラスターに属することと、*CONTOSO\usera* が管理者クライアント アクセスを持つことを指定しています。

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

> [!NOTE]
> Service Fabric は、ドメイン コントローラー上にデプロイしないでください。 コンピューター グループまたは管理されたサービス アカウント (gMSA) グループを使用する場合は、ClusterConfig.json にドメイン コントローラーの IP アドレスが含まれていないことを確認します。
>
>

## <a name="next-steps"></a>次のステップ
*ClusterConfig.JSON* ファイルで Windows セキュリティを構成したら、 [Windows 上で実行されるスタンドアロン クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関する記事で説明されているクラスター作成処理を再開します。

ノード間のセキュリティ、クライアントとノード間のセキュリティ、ロールベースのアクセス制御の詳細については、[クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事をご覧ください。

PowerShell または FabricClient を使用した接続の例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md)」をご覧ください。

