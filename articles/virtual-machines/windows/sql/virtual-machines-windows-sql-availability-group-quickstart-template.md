---
title: WSFC、リスナーを作成し、Azure クイック スタート テンプレートを使用して、SQL Server VM に Always On 可用性グループ用の ILB を構成する
description: Azure クイック スタート テンプレートを使用すれば、Azure で SQL Server VM 用の可用性グループを作成するプロセスを簡略化できます。その場合、テンプレートを使用して、クラスターを作成し、SQL VM をそのクラスターに参加させ、リスナーを作成して、ILB を構成します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 9be8717bc9b1d15a59486edf206dd0657a711c06
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360557"
---
# <a name="create-wsfc-listener-and-configure-ilb-for-an-always-on-availability-group-on-a-sql-server-vm-with-azure-quickstart-template"></a>WSFC、リスナーを作成し、Azure クイック スタート テンプレートを使用して、SQL Server VM に Always On 可用性グループ用の ILB を構成する
この記事では、Azure クイック スタート テンプレートを使用して、Azure での SQL Server Virtual Machines 用の Always On 可用性グループ構成のデプロイを部分的に自動化する方法について説明します。 このプロセスでは、2 つの Azure クイック スタート テンプレートが使用されます。 

   | テンプレート | 説明 |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Windows フェールオーバー クラスターを作成し、それに SQL Server VM を参加させます。 |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 可用性グループ リスナーを作成し、内部ロード バランサーを構成します。 |
   | &nbsp; | &nbsp; |

可用性グループの作成や、内部ロード バランサーの作成など、可用性グループ構成の他の部分は手動で行う必要があります。 この記事では、一連の自動および手動の手順を示します。
 

## <a name="prerequisites"></a>前提条件 
クイック スタート テンプレートを使用して Always On 可用性グループのセットアップを自動化するには、次の前提条件が必要です。 
- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [ドメイン コントローラー](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-forest)を含むリソース グループ。 
- [SQL VM リソース プロバイダーに登録](#register-existing-sql-vm-with-new-resource-provider)されているのと同じ可用性セットまたは可用性ゾーンにある、1 つ以上のドメイン参加済みの、[SQL Server 2016 (またはそれ以上の) Enterprise エディションを実行する Azure の VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。  

## <a name="register-existing-sql-vm-with-new-resource-provider"></a>既存の SQL VM を新しいリソース プロバイダーに登録する
これらの可用性グループの Azure クイック スタート テンプレートは SQL VM リソース プロバイダー (Microsoft.SqlVirtualMachine) に依存するため、既存の SQL Server VM を SQL VM リソース プロバイダーに登録する必要があります。 2018 年 12 月以降に SQL Server VM を作成した場合は、この手順をスキップします。この日付以降に作成された SQL Server VM はすべて自動的に登録されるためです。 このセクションでは、Azure portal を使用してプロバイダーに登録する手順について説明しますが、[PowerShell](virtual-machines-windows-sql-ahb.md#powershell) を使用することもできます。 

  >[!IMPORTANT]
  > SQL Server VM リソースを削除する場合は、イメージのハード コーディングされたライセンス設定に戻ります。 

1. Azure portal を開き、**すべてのサービス**に移動します。 
1. **サブスクリプション**に移動し、関心のあるサブスクリプションを選択します。  
1. **サブスクリプション** ブレード内で、**リソースプロバイダー**に移動します。 
1. フィルター内に`sql`と入力し、SQL 関連のリソース プロバイダーを表示します。 
1. 目的となるアクションに応じて、*登録*、*再登録*、または*登録解除*のいずれかを **Microsoft.SqlVirtualMachine** プロバイダーで選択します。 

  ![プロバイダーの変更](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>手順 1 - WSFC を作成し、クイック スタート テンプレートを使用して SQL Server VM をクラスターに参加させる 
SQL Server VM が SQL VM の新しいリソース プロバイダーに登録されたら、SQL Server VM を *SqlVirtualMachineGroup* に参加させることができます。 このリソースでは、バージョン、エディション、完全修飾ドメイン名、クラスターを管理するための AD アカウント、およびクラウド監視としてのストレージ アカウントなど、Windows フェールオーバー クラスターのメタデータを定義します。 SQL Server VM を *SqlVirtualMachineGroup* に追加することで、Windows フェールオーバー クラスターをブートストラップし、SQL Server VM をクラスターに参加させます。 この手順は **101-sql-vm-ag-setup** クイック スタート テンプレートで自動化されますが、以下の手順を使用して実装することができます。

1. [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) クイック スタート テンプレートに移動し、**[Azure へのデプロイ]** を選択して、Azure portal 内でクイック スタート テンプレートを起動します。
1. 必須フィールドに入力し、Windows フェールオーバー クラスターのメタデータを構成します。 省略可能なフィールドは空白のままでかまいません。

    次の表には、テンプレートに必要な値が示されています。 

   | **フィールド** | 値 |
   | --- | --- |
   | **サブスクリプション** |  SQL Server VM が存在するサブスクリプション。 |
   |**リソース グループ** | SQL Server VM が属するリソース グループ。 | 
   |**フェールオーバー クラスター名** | 新しい Windows フェールオーバー クラスターの任意の名前。 |
   | **既存の VM のリスト** | 可用性グループに参加させるため、この新しいクラスターの一部となる、SQL Server VM。 これらの値はコンマとスペースで区切ります (例:SQLVM1, SQLVM2)。 |
   | **SQL Server のバージョン** | ドロップダウンから、SQL Server VM の SQL Server のバージョンを選択します。 現在サポートされているのは、SQL 2016 と SQL 2017 のイメージのみです。 |
   | **既存の完全修飾ドメイン名** | SQL Server VM が属するドメインの既存の FQDN。 |
   | **既存のドメイン アカウント** | SQL Server への sysadmin アクセス権を持つ既存のドメイン アカウント。 | 
   | **ドメイン アカウントのパスワード** | 前述のドメイン アカウントのパスワード。 | 
   | **既存の SQL サービス アカウント** | SQL Server サービスを制御するために使用されているドメイン ユーザー アカウント。 この情報は、[**SQL Server 構成マネージャー**](https://docs.microsoft.com/sql/relational-databases/sql-server-configuration-manager?view=sql-server-2017)を使用して見つけることができます。 |
   | **SQL サービス パスワード** | SQL Server サービスを制御するドメイン ユーザー アカウントで使用されるパスワード。 |
   | &nbsp; | &nbsp; |

1. 使用条件に同意する場合は、**[上記の使用条件に同意する]** の横のチェック ボックスをオンにし、**[購入]** を選択してクイック スタート テンプレートのデプロイを完了します。 
1. デプロイを監視するには、上部のナビゲーション バナーにある **[通知]** ベル アイコンからデプロイを選択するか、Azure portal で **[リソース グループ]** に移動し、**[設定]** フィールドの **[デプロイ]** を選択して、'Microsoft.Template' デプロイを選びます。 

## <a name="step-2---manually-create-the-availability-group"></a>手順 2 - 可用性グループを手動で作成する 
[PowerShell](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell?view=sql-server-2017)、[SQL Server Management Studio](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio?view=sql-server-2017) または [Transact-SQL](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql?view=sql-server-2017) を使用して、通常どおり、可用性グループを手動で作成します。 

  >[!IMPORTANT]
  > 手順 4 で **101-sql-vm-aglistener-setup** クイック スタート テンプレートによって自動化されるため、この時点ではリスナーを作成**しない**でください。 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>手順 3 - 内部ロード バランサー (ILB) を手動で作成する
Always On 可用性グループ (AG) リスナーには、内部 Azure ロード バランサー (ILB) が必要です。 ILB では、高速フェールオーバーおよび再接続を可能にする AG リスナー用の "フローティング" IP アドレスが提供されます。 可用性グループ内の SQL Server VM が同じ可用性セットの一部である場合は、Basic Load Balancer を使用できます。それ以外の場合は、Standard Load Balancer を使用する必要があります。  **ILB は、SQL Server VM インスタンスと同じ vNet に存在する必要があります。** 作成する必要があるのは ILB のみです。構成の残りの部分 (バックエンド プール、正常性プローブ、負荷分散規則など) は手順 4 で **101-sql-vm-aglistener-setup** クイック スタート テンプレートによって処理されます。 

1. Azure ポータルで、SQL Server の仮想マシンを含んだリソース グループを開きます。 
2. リソース グループで **[追加]** をクリックします。
3. **ロード バランサー**を探し、検索結果から **[ロード バランサー]** (**Microsoft** によって発行されたもの) を選択します。
4. **[ロード バランサー]** ブレードで **[作成]** をクリックします。
5. **[ロード バランサーの作成]** ダイアログボックスで、次のようにロード バランサーを構成します。

   | Setting | 値 |
   | --- | --- |
   | **Name** |ロード バランサーを表すテキスト名  (例: **sqlLB**)。 |
   | **Type** |**内部**:ほとんどの実装では、内部ロード バランサーを使います。この場合、同じ仮想ネットワーク内のアプリケーションを可用性グループに接続できます。  </br> **外部**:アプリケーションをパブリック インターネット接続経由で可用性グループに接続できます。 |
   | **Virtual Network** |SQL Server インスタンスが存在する仮想ネットワークを選択します。 |
   | **サブネット** |SQL Server インスタンスが存在するサブネットを選択します。 |
   | **IP アドレスの割り当て** |**静的** |
   | **プライベート IP アドレス** |サブネット内の利用可能な IP アドレスを指定します。 この IP アドレスは、クラスターにリスナーを作成するときに使用します。|
   | **サブスクリプション** |複数のサブスクリプションを所有している場合、このフィールドが表示されます。 このリソースに関連付けられているサブスクリプションを選択します。 通常は、可用性グループのすべてのリソースについて同じサブスクリプションを選択してください。 |
   | **リソース グループ** |SQL Server インスタンスが存在するリソース グループを選択します。 |
   | **場所** |Azure において SQL Server インスタンスが存在する場所を選択します。 |
   | &nbsp; | &nbsp; |

6. **作成**を選択します。 


  >[!NOTE]
  > 各 SQL Server VM 用のパブリック IP リソースには、Standard Load Balancer と互換性のある Standard SKU が必要です。 VM のパブリック IP リソースの SKU を決定するには、**[リソース グループ]** に移動し、目的の SQL Server VM 用の **[パブリック IP アドレス]** リソースを選択し、**[概要]** ウィンドウの **[SKU]** で値を見つけます。 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>手順 4 - AG リスナーを作成し、クイック スタート テンプレートを使用して ILB を構成する

可用性グループ リスナーを作成し、Microsoft.SqlVirtualMachine/SQL 仮想マシン グループ/可用性グループ リスナー リソースのプロビジョニング時に、**101-sql-vm-aglistener-setup** クイック スタート テンプレートを使用して自動的に内部ロード バランサー (ILB) を構成します。 **101-sql-vm-aglistener-setup** クイック スタート テンプレートでは、SQL VM リソース プロバイダーを介して、以下の操作を行います。

 - クラスターと ILB のネットワーク設定を構成する。 
 - ILB バックエンド プール、正常性プローブ、および負荷分散規則を構成する。
 - 特定の IP アドレスと名前を使用して、AG リスナーを作成する。

ILB を構成し、AG リスナーを作成するには、次の操作を行います。
1. [**101-sql-vm-aglistener-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) クイック スタート テンプレートに移動し、**[Azure へのデプロイ]** を選択して、Azure portal 内でクイック スタート テンプレートを起動します。
1. 必須フィールドに入力して ILB を構成し、可用性グループ リスナーを作成します。 省略可能なフィールドは空白のままでかまいません。 

    次の表には、テンプレートに必要な値が示されています。 

   | **フィールド** | 値 |
   | --- | --- |
   |**リソース グループ** | SQL Server VM と可用性グループが存在する、リソース グループ。 | 
   |**既存のフェールオーバー クラスター名** | SQL Server VM が参加しているクラスターの名前。 |
   | **既存の SQL 可用性グループ**| SQL Server VM が属する可用性グループの名前。 |
   | **既存の VM のリスト** | 前述の可用性グループに属する SQL Server VM の名前。 名前はコンマとスペースで区切る必要があります (例:SQLVM1, SQLVM2)。 |
   | **既存の完全修飾ドメイン名** | SQL Server VM が属するドメインの既存の FQDN。 |
   | **リスナー** | リスナーに割り当てたい DNS 名。 既定では、このテンプレートで 'aglistener' という名前が指定されますが、これは変更できます。 |
   | **リスナー ポート** | リスナーで使用するポート。 通常、このポートは既定のポートである 1433 にする必要があります。そのため、これはこのテンプレートで指定されたポート番号となります。 しかし、既定のポートが変更された場合、リスナー ポートでは代わりにその値を使用する必要があります。 | 
   | **既存の VNet** | SQL Server VM、および ILB が属する vNet の名前。 |
   | **既存のサブネット** | SQL Server VM の内部サブネットの*名前* (例: 既定値)。 この値は、**[リソース グループ]** に移動し、**[vNet]** を選択し、**[設定]** ウィンドウで **[サブネット]** を選び、**[名前]** で値をコピーすることで、決定できます。 |
   | **既存の内部ロード バランサー** | 手順 3 で作成した ILB の名前。 |
   | **プローブ ポート** | ILB で使用するプローブ ポート。 テンプレートでは既定で 59999 を使用しますが、この値は変更できます。 |
   | &nbsp; | &nbsp; |

1. 使用条件に同意する場合は、**[上記の使用条件に同意する]** の横のチェック ボックスをオンにし、**[購入]** を選択してクイック スタート テンプレートのデプロイを完了します。 
1. デプロイを監視するには、上部のナビゲーション バナーにある **[通知]** ベル アイコンからデプロイを選択するか、Azure portal で **[リソース グループ]** に移動し、**[設定]** フィールドの **[デプロイ]** を選択して、'Microsoft.Template' デプロイを選びます。 

  >[!NOTE]
  >デプロイが途中で失敗した場合は、**101-sql-vm-aglistener-setup** クイック スタート テンプレートを再デプロイする前に、PowerShell を使用して、手動で[新たに作成されたリスナーを削除する](#remove-availability-group-listener)必要があります。 

## <a name="remove-availability-group-listener"></a>可用性グループ リスナーを削除する
テンプレートで構成された可用性グループ リスナーを後で削除する必要がある場合は、SQL VM リソース プロバイダーを経由する必要があります。 リスナーは SQL VM リソース プロバイダーを介して登録されるため、SQL Server Management Studio を使用して削除するだけでは十分ではありません。 実際には、PowerShell を使用し、SQL VM リソース プロバイダーを介して削除する必要があります。 そうすることで、SQL VM リソース プロバイダーから AG リスナー メタデータが削除され、可用性グループから物理的にリスナーが削除されます。 

次のコード スニペットでは、SQL リソース プロバイダーと、可用性グループの両方から SQL 可用性グループ リスナーを削除します。 

```PowerShell
# Remove the AG listener
# example: Remove-AzureRmResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzureRmResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="known-issues-and-errors"></a>既知の問題とエラー
このセクションでは、いくつかの既知の問題とその考えられる解決策について説明します。 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>可用性グループ '\<AG 名>' の可用性グループ リスナーが既に存在する
AG リスナーの Azure クイック スタート テンプレートで使用されている、選択された可用性グループには、可用性グループ内に物理的に、または SQL VM リソース プロバイダー内のメタデータとして、リスナーが既に含まれています。  **101-sql-vm-aglistener-setup** クイック スタート テンプレートを再デプロイする前に、[PowerShell](#remove-availability-group-listener) を使用してリスナーを削除します。 

### <a name="connection-only-works-from-primary-replica"></a>プライマリ レプリカからの接続のみが機能する
**101-sql-vm-aglistener-setup** テンプレートのデプロイに失敗し、ILB 構成が不整合な状態のままになっている場合にこのような動作になる可能性があります。 バックエンド プールで可用性セットがリストされ、正常性プローブの規則と負荷分散規則が存在することを確認します。 何かが不足している場合、ILB 構成は不整合状態になっています。 

この動作を解決するには、[PowerShell](#remove-availability-group-listener) を使用してリスナーを削除し、Azure portal を介して内部ロード バランサーを削除し、再び、[手順 3](#step-3---manually-create-the-internal-load-balanced-ilb) から始めます。 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - SQL 仮想マシン リストしか更新できない
このエラーは、リスナーが SQL Server Management Studio (SSMS) を介して削除されたが、SQL VM リソース プロバイダーからは削除されなかった場合に **101-sql-vm-aglistener-setup** テンプレートをデプロイしたときに発生する可能性があります。 SSMS を介してリスナーを削除しても、SQL VM リソース プロバイダーからはリスナーのメタデータは削除されません。[PowerShell](#remove-availability-group-listener) を使用して、リソース プロバイダーからリスナーを削除する必要があります。 


## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [SQL Server VM の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server VM の FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server VM の料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server VM のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM のライセンス モデルの切り替え](virtual-machines-windows-sql-ahb.md)



