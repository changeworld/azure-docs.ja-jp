---
title: "Azure Service Fabric クラスターのセットアップ | Microsoft Docs"
description: "クイック スタート - Azure で Windows または Linux の Service Fabric クラスターを作成します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 813217567c7e1fa5fc9ac11492933ad0c34553d1
ms.lasthandoff: 04/19/2017


---

# <a name="create-your-first-service-fabric-cluster-on-azure"></a>Azure で初めての Service Fabric クラスターを作成する
[Service Fabric クラスター](service-fabric-deploy-anywhere.md)は、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 このクイック スタートでは、Windows または Linux 上で実行される 5 ノード クラスターを [Azure Portal](http://portal.azure.com) を通じてほんの数分で作成できるようにします。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="log-in-to-azure"></a>Azure へのログイン
Azure Portal ([http://portal.azure.com](http://portal.azure.com)) にログインします。

## <a name="create-the-cluster"></a>クラスターを作成する

1. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。
2. **[新規]** ブレードで **[Compute]** を選択し、**[Compute]** ブレードで **[Service Fabric クラスター]** を選択します。
3. Service Fabric の **[基本]** フォームに入力します。 **[オペレーティング システム]** では、クラスター ノードを実行する Windows または Linux のバージョンを選択します。 ここに入力したユーザー名とパスワードが、仮想マシンへのログインに使用されます。 **[リソース グループ]** では、新しいグループを作成します。 リソース グループとは、Azure リソースの作成と一括管理に使用する論理コンテナーです。 完了したら、**[OK]** をクリックします。

    ![クラスターのセットアップに関する出力][cluster-setup-basics]

4. **[クラスター構成]** フォームに入力します。  **[ノード タイプ数]** では「1」と入力し、[[持続性層]](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) では「Bronze」と入力します。

5. **[各ノード タイプの構成]** を選択し、**[ノード タイプの構成]** フォームに入力します。 ノード タイプは、VM のサイズ、VM の数、カスタム エンドポイント、そのタイプの VM のその他の設定を定義します。 定義した各ノード タイプは、個別の仮想マシン スケール セットとしてセットアップされ、仮想マシンをセットとしてデプロイおよび管理するために使用されます。 各ノード タイプは、個別にスケールアップまたはスケールダウンしたり、異なるポートのセットを開いたり、別の容量メトリックを持ったりすることができます。  第 1 (プライマリ) ノード タイプは、Service Fabric システム サービスをホストし、5 個以上の VM を持つ必要があります。

    運用環境デプロイメントでは、[容量計画](service-fabric-cluster-capacity.md)が重要なステップです。  しかし、このクイック スタートではアプリケーションを実行していないので、*DS1_v2 Standard* VM サイズを選択します。  [[信頼性層]](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) には "Silver" を選択し、初期の仮想マシン スケール セット容量を 5 にします。  

    クラスターで実行されているアプリケーションに接続できるように、カスタム エンドポイントは、Azure Load Balancer でポートを開きます。  「80, 8172」と入力して、ポート 80 と 8172 を開きます。

    **[詳細設定の構成]** チェック ボックスはオンにしません。この構成は、TCP/HTTP 管理エンドポイント、アプリケーションのポート範囲、[配置の制約](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints)、および[容量プロパティ](service-fabric-cluster-resource-manager-metrics.md)をカスタマイズするために使用します。    

    **[OK]**を選択します。

6. **[クラスター構成]** フォームで、**[診断]** を **[有効]** に設定します。  このクイック スタートでは、どの[ファブリック設定](service-fabric-cluster-fabric-settings.md)プロパティも入力する必要はありません。  **[Fabric バージョン]** で **[自動]** アップグレード モードを選択し、クラスターを実行しているファブリック コードのバージョンが Microsoft によって自動的に更新されるようにします。  [サポートされているバージョンを選択](service-fabric-cluster-upgrade.md)して、そのバージョンにアップグレードする場合は、モードを **[手動]** に設定します。 

    ![ノード タイプの構成][node-type-config]

    **[OK]**を選択します。

7. **[セキュリティ]** フォームに入力します。  このクイック スタートでは、**[セキュリティ保護なし]** を選択します。  ただし、セキュリティで保護されていないクラスターでは、だれでも匿名で接続して管理操作を実行できるため、運用環境のワークロード用にはセキュリティで保護されたクラスターを作成することを強くお勧めします。  

    Service Fabric では証明書を使用して、クラスターとそのアプリケーションのさまざまな側面をセキュリティで保護するための認証および暗号化を指定します。 Service Fabric での証明書の使用方法の詳細については、「[Service Fabric クラスターのセキュリティに関するシナリオ](service-fabric-cluster-security.md)」を参照してください。  Azure Active Directory を使用したユーザー認証を有効にするか、アプリケーション セキュリティ用の証明書をセットアップするには、[Resource Manager テンプレートからクラスターを作成](service-fabric-cluster-creation-via-arm.md)します。

    **[OK]**を選択します。

8. 概要を確認します。  自分で入力した設定で構築された Resource Manager テンプレートをダウンロードする場合は、**[テンプレートとパラメーターのダウンロード]** を選択します。  **[作成]** を選択して、クラスターを作成します。

    通知には作成の進行状況が表示されます (画面の右上にあるステータス バーの近くの "ベル" アイコンをクリックします)。クラスターの作成中に **[スタート画面にピン留めする]** をクリックした場合、**[Deploying Service Fabric Cluster (Service Fabric クラスターのデプロイ)]** が **[スタート]** 画面にピン留めされます。

## <a name="view-cluster-status"></a>クラスターの状態を表示する
クラスターの作成後、ポータルの **[概要]** ブレードでクラスターを検査できます。 これにより、クラスターのパブリック エンドポイント、Service Fabric Explorer へのリンクなどのクラスターに関する詳細が、ダッシュボードに表示されます。

![クラスターの状態][cluster-status]

## <a name="visualize-the-cluster-using-service-fabric-explorer"></a>Service Fabric Explorer を使用したクラスターの視覚化
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) は、クラスターを視覚化してアプリケーションを管理するための最適なツールです。  Service Fabric Explorer は、クラスター内で実行されるサービスです。  アクセスするには Web ブラウザーを使用し、ポータルでクラスターの **[概要]** ページの **[Service Fabric Explorer]** リンクをクリックします。  ブラウザーにアドレスを直接入力することもできます。アドレスは [http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer](http://quickstartcluster.westus.cloudapp.azure.com:19080/Explorer) です。

クラスター ダッシュボードにクラスターの概要 (アプリケーションとノードの正常性の概要など) が表示されます。 ノード ビューには、クラスターの物理的なレイアウトが表示されます。 特定のノードについて、そのノードでコードがデプロイされているアプリケーション、

![Service Fabric Explorer][service-fabric-explorer]

## <a name="connect-to-the-cluster-using-powershell"></a>PowerShell を使用してクラスターに接続する
PowerShell を使用して接続することで、クラスターが実行されていることを確認します。  ServiceFabric PowerShell モジュールは、[Service Fabric SDK](service-fabric-get-started.md) と共にインストールされます。  クラスターへの接続は、[Connect-ServiceFabricCluster](/powershell/module/ServiceFabric/Connect-ServiceFabricCluster) コマンドレットで確立します。   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint localhost:19000
```
その他の方法でクラスターに接続する例については、「[セキュリティ保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md)」を参照してください。 クラスターに接続した後、[Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) コマンドレットを使って、クラスターに含まれている一連のノードとノードごとの状態情報を表示します。 すべてのノードで **HealthState** が *OK* になっている必要があります。

```powershell
PS C:\> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  ----------- ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.5.216.0   1                     Up 00:59:04   00:00:00              Ok
```

## <a name="remove-the-cluster"></a>クラスターの削除
Service Fabric クラスターは、クラスター リソース自体に加え、その他の Azure リソースで構成されます。 そのため、Service Fabric クラスターを完全に削除するには、構成されるすべてのリソースも削除する必要があります。 クラスターとそのすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。 クラスターを削除したり、リソース グループ内の (すべてではなく) 一部のリソースを削除したりする他の方法については、[クラスターの削除](service-fabric-cluster-delete.md)に関するページを参照してください。

Azure Portal でリソース グループを削除するには:
1. 削除する Service Fabric クラスターに移動します。
2. クラスターの [要点] ページで**リソース グループ**の名前をクリックします。
3. リソース グループの **[要点]** ページで **[削除]** をクリックし、そのページの指示に従ってリソース グループの削除を完了します。
    ![リソース グループの削除][cluster-delete]

## <a name="next-steps"></a>次のステップ
開発用のスタンドアロン クラスターをセットアップしたら、次の作業に挑戦してみてください。
* [ポータルでセキュリティで保護されたクラスターを作成します](service-fabric-cluster-creation-via-portal.md)
* [テンプレートからクラスターを作成します](service-fabric-cluster-creation-via-arm.md) 
* [PowerShell を使ってアプリをデプロイ](service-fabric-deploy-remove-applications.md)します。


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
