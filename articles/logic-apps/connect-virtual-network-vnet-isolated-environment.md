---
title: Azure Logic Apps から Azure 仮想ネットワークに接続する
description: Azure Logic Apps から Azure 仮想ネットワークにアクセスするために、ロジック アプリや他のリソースをパブリックまたは "グローバル" な Azure とは分離するプライベートな専用の分離統合サービス環境を作成できます
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/25/2018
ms.openlocfilehash: 354c31014448b914b33d2bef5483efc78092f726
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391923"
---
# <a name="create-isolated-environments-to-access-azure-virtual-networks-from-azure-logic-apps"></a>Azure Logic Apps から Azure 仮想ネットワークにアクセスするための分離環境を作成する

> [!NOTE]
> この機能は*プライベート プレビュー*段階です。 アクセスを要求するには､[ここから参加要求を作成](https://aka.ms/iseprivatepreview)します。

論理アプリと統合アカウントが [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)にアクセスする必要がある統合シナリオでは、組織の仮想ネットワークにリンクし、組織のネットワーク内に Logic Apps サービスを展開する[*統合サービス環境* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成できます。 ロジック アプリと統合アカウントを作成するときに、この ISE を場所として選択します。 ロジック アプリと統合アカウントは、仮想ネットワーク内の仮想マシン (VM)、サーバー、システム、サービスなどのリソースに直接アクセスできます。 

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

ISE は、専用のストレージと、パブリックまたは*グローバル*の Logic Apps サービスとは別に存在する他のリソースを使用するプライベートな分離環境です。 この分離で、他の Azure テナントがご利用のアプリのパフォーマンスに与える可能性がある影響を軽減することもできます。 

この記事では、次のタスクの実行方法について説明します。

* プライベートな Logic Apps インスタンスが仮想ネットワークにアクセスできるように、Azure 仮想ネットワークに対するアクセス許可を設定します。

* 統合サービス環境 (ISE) を作成します。 

* ISE で実行できるロジック アプリを作成します。 

* ISE のロジック アプリ用に統合アカウントを作成します。

統合サービス環境の詳細については、[分離された Azure Logic Apps から Azure Virtual Network リソースにアクセスする方法](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。 

* Azure 仮想ネットワークがない場合は、[Azure 仮想ネットワークの作成](../virtual-network/quick-create-portal.md)方法について学んでください。 

  > [!IMPORTANT]
  > 環境を作成するために Azure 仮想ネットワークは必要ありませんが、その環境を作成する場合は、環境のピアとして仮想ネットワーク*のみ*を選択できます。 

* ロジック アプリに Azure 仮想ネットワークへの直接アクセスを許可するには、Logic Apps サービスに仮想ネットワークにアクセスするアクセス許可が付与されるように、[ロールベースのアクセス制御 (RBAC) アクセス許可を設定](#vnet-access)します。 

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

<a name="vnet-access"></a>

## <a name="set-virtual-network-permissions"></a>仮想ネットワークのアクセス許可を設定する

統合サービス環境を作成する場合、Azure Virtual Network をご利用の環境の*ピア*として選択することができます。 ただし、環境を作成するときにこの手順のみを実行することや、*ピアリング*を実行することができます。 この関係により、Logic Apps サービスはその仮想ネットワーク内のリソースに直接接続し、環境にそれらのリソースに対するアクセス権を付与できます。 

仮想ネットワークを選択する前に、仮想ネットワークで ロールベースのアクセス制御 (RBAC) のアクセス許可を設定する必要があります。 このタスクを完了するには、特定のロールを Azure Logic Apps サービスに割り当てる必要があります。

1. [Azure portal](https://portal.azure.com) で、仮想ネットワークを探して選択します。 

1. 仮想ネットワークのメニューで **[アクセス制御 (IAM)]** を選択します。 

1. **[アクセス制御]** で **[ロールの割り当て]** が選択されていない場合は選択します。 **[ロールの割り当て]** ツール バーで **[追加]** を選択します。 

   ![ロールの割り当てを追加する](./media/connect-virtual-network-vnet-isolated-environment/set-up-role-based-access-control-vnet.png)

1. **[アクセス許可の追加]** ウィンドウで、Azure Log Apps サービス用にこの表内の各ロールを設定します。 各ロールを完了したら、必ず **[保存]** を選択します。

   | Role | アクセスの割り当て先 | Select | 
   |------|------------------|--------|
   | **Network Contributor** | **Azure AD のユーザー、グループ、またはアプリケーション** | **[Azure Logic Apps]** を入力します。 メンバー一覧が表示されたら、同じ値を選択します。 <p>**ヒント**: このサービスが見つからない場合は、Logic Apps サービスのアプリ ID `7cd684f4-8a78-49b0-91ec-6a35d38739ba` を入力します。 | 
   | **クラシック共同作成者** | **Azure AD のユーザー、グループ、またはアプリケーション** | **[Azure Logic Apps]** を入力します。 メンバー一覧が表示されたら、同じ値を選択します。 <p>**ヒント**: このサービスが見つからない場合は、Logic Apps サービスのアプリ ID `7cd684f4-8a78-49b0-91ec-6a35d38739ba` を入力します。 | 
   |||| 

   例: 

   ![Add permissions](./media/connect-virtual-network-vnet-isolated-environment/add-contributor-roles.png)

   ピアリングに必要なロールのアクセス許可の詳細については、[「仮想ネットワーク ピアリングの作成、変更、削除」の「アクセス許可」のセクション](../virtual-network/virtual-network-manage-peering.md#permissions)を参照してください。 

仮想ネットワークが Azure ExpressRoute、Azure Point-to-Site VPN、または Azure Site-to-Site VPN を介して接続されている場合は、必要なゲートウェイ サブネットを追加できるように次のセクションに進みます。 それ以外の場合は、[環境の作成](#create-environment)に関するセクションに進みます。

<a name="add-gateway-subnet"></a>

## <a name="add-gateway-subnet-for-virtual-networks-with-expressroute-or-vpns"></a>ExpressRoute または VPN を使用して仮想ネットワークのゲートウェイ サブネットを追加する

これまでの手順を完了した後、[Azure ExpressRoute](../expressroute/expressroute-introduction.md)、[Azure Point-to-Site VPN](../vpn-gateway/point-to-site-about.md)、または [Azure ExpressRoute](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) を介して接続されている Azure 仮想ネットワークへのアクセス権を統合サービス環境 (ISE) 付与するには、[*ゲートウェイ サブネット*](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsub)も仮想ネットワークに追加する必要があります。

1. [Azure portal](https://portal.azure.com) で、仮想ネットワークを探して選択します。 仮想ネットワークのメニューで **[サブネット]** を選択し、**[ゲートウェイ サブネット]** > **[OK]** の順に選択します。

   ![ゲートウェイ サブネットを追加する](./media/connect-virtual-network-vnet-isolated-environment/add-gateway-subnet.png)

1. 次に、[*ルート テーブル*](../virtual-network/manage-route-table.md)を作成し、以前に作成したゲートウェイ サブネットに関連付けます。

   1. Azure のメイン メニューで **[リソースの作成]** > 
    **[ネットワーク]** > **[ルート テーブル]** の順に選択します。

      ![ルート テーブルを作成する](./media/connect-virtual-network-vnet-isolated-environment/create-route-table.png)

   1. ルート テーブルに関する情報 (名前、使用する Azure サブスクリプション、Azure リソース グループ、場所など) を指定します。 **[BGP ルート伝達]** プロパティが **[有効]** に設定されていることを確認し、**[作成]** を選択します。

      ![ルート テーブルの詳細を指定する](./media/connect-virtual-network-vnet-isolated-environment/enter-route-table-information.png)

   1. ルート テーブル メニューで、**[サブネット]** を選択し、次に **[関連付け]** を選択します。 

      ![ルート テーブルをサブネットに接続する](./media/connect-virtual-network-vnet-isolated-environment/associate-route-table.png)

   1. **[仮想ネットワーク]** を選択し、仮想ネットワークを選択します。
   
   1. **[サブネット]** を選択し、以前に作成したゲートウェイ サブネットを選択します。

   1. 完了したら、**[OK]** を選びます。

1. ポイント対サイト VPN を使用している場合は、以下の手順も実行します。

   1. Azure で、仮想ネットワーク ゲートウェイ リソースを探して選択します。

   1. ゲートウェイのメニューで、**[ポイント対サイトの構成]** を選択します。 
   次に、**[VPN クライアントのダウンロード]** を選択して、最新の VPN クライアント構成を取得します。

      ![最新の VPN クライアントをダウンロードする](./media/connect-virtual-network-vnet-isolated-environment/download-vpn-client.png)

これで、ExpressRoute、ポイント対サイト VPN、またはサイト間 VPN を使用する仮想ネットワーク用のゲートウェイ サブネットの設定は完了です。 統合サービス環境の作成を続行するには、次の手順を実行します。

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE を作成する

統合サービス環境 (ISE) を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) の Azure メイン メニューで、**[リソースの作成]** を選択します。

   ![新しいリソースの作成](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 検索ボックスに、フィルターとして「統合サービス環境」と入力します。
結果リストで **[統合サービス環境 (プレビュー)]** を選択し、**[作成]** を選択します。

   ![[統合サービス環境] を選択する](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![[作成] を選択する](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 環境について次の詳細を指定します。

   ![環境の詳細を指定する](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | プロパティ | 必須 | 値 | [説明] |
   |----------|----------|-------|-------------|
   | **名前** | [はい] | <*environment-name*> | 環境を示す名前 | 
   | **サブスクリプション** | [はい] | <*Azure サブスクリプション名*> | 環境に使用する Azure サブスクリプション | 
   | **[リソース グループ]** | [はい] | <*Azure-resource-group-name*> | 環境を作成する Azure リソース グループ |
   | **場所** | [はい] | <*Azure-datacenter-region*> | 環境に関する情報を格納する Azure データセンター リージョン |
   | **ピア VNET** | いいえ  | <*Azure-VNET-name*> | 環境内のロジック アプリが仮想ネットワークにアクセスできるように、*ピア*として環境に関連付ける Azure 仮想ネットワーク。 この関係を作成する前に、必ず [Azure Logic Apps 用に仮想ネットワークにロールベースのアクセス制御を設定しておきます](#vnet-access)。 <p>**重要**: 仮想ネットワークは必須ではありませんが、仮想ネットワークを選択できるのは、環境の作成時*のみ*です。 | 
   | **ピアリング名** | 選択した仮想ネットワークに対して [はい] | <*peering-name*> | ピアの関係に付ける名前 | 
   | **VNET IP 範囲** | 選択した仮想ネットワークに対して [はい] | <*IP-address-range*> | 環境にリソースを作成するために使用する IP アドレス範囲。 この範囲は、[Classless Inter-Domain Routing (CIDR) 形式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) (たとえば 10.0.0.1/16) を使用する必要があります。また、クラス B アドレス空間が必要です。 **[ピア VNET]** プロパティで選択した仮想ネットワークのアドレス空間内、またはピアリングまたはゲートウェイ経由でピア ネットワークが接続されている他のプライベート IP アドレス内に存在する範囲は指定できません。 <p><p>**重要**: 環境の作成後に、このアドレス範囲を*変更することはできません*。 |
   |||||
   
1. 操作が完了したら、**[作成]** を選択します。 

   Azure は環境の展開を開始しますが、このプロセスが完了するまでに*最長 2 時間*かかります。 
   展開の状態を確認するには、Azure ツールバーで通知アイコンを選択します。これで通知ウィンドウが開きます。

   ![展開の状態を確認する](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   展開が正常に完了すると、Azure に次の通知が表示されます。

   ![デプロイメント成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

1. 展開の完了後に Azure で環境が自動的に表示されない場合は、環境を表示するために **[リソースに移動]** を選択します。  

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>ロジック アプリを作成する - ISE

統合サービス環境 (ISE) を使用するロジック アプリを作成するには、[ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページに記載されている通常の手順に従いますが、次の違いと考慮事項があります。 

* ロジック アプリを作成すると、**[場所]** プロパティの **[統合サービス環境]** に使用できるリージョンと共に ISE の一覧が表示されます。 リージョンではなく ISE を選択します。次に例を示します。

  ![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* 親ロジック アプリと同じ ISE で実行される HTTP トリガーやアクションなど、同じ組み込みを使用できます。 **ISE** ラベルがあるコネクタは、親ロジック アプリと同じ ISE でも動作します。 **ISE** ラベルがないコネクタはグローバルな Logic Apps サービスで実行されます。

  ![ISE のコネクタを選択する](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Azure 仮想ネットワークをピアとして設定済みの場合、ISE のロジック アプリはその仮想ネットワーク内のリソースに直接アクセスできます。 ISE にリンクされている仮想ネットワークのオンプレミス システムの場合、次のいずれかの項目を使用することで、ロジック アプリから直接それらのシステムにアクセスできるようになります。 

  * そのシステムの ISE コネクタ (SQL Server など)

  * HTTP アクション 

  * カスタム コネクタ

  仮想ネットワークにない、または ISE コネクタがないオンプレミス システムの場合は、[オンプレミスのデータ ゲートウェイをセットアップして使用した](../logic-apps/logic-apps-gateway-install.md)後も引き続き接続できます。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>統合アカウントを作成する - ISE

統合サービス環境 (ISE) でロジック アプリに統合アカウントを使用するには、その統合アカウントでロジック アプリと*同じ環境*を使用する必要があります。 ISE のロジック アプリは、同じ ISE 内の統合アカウントのみを参照できます。 

ISE を使用する統合アカウントを作成するには、**[場所]** プロパティを除いて[統合アカウントの作成方法](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)に記載されている通常の手順を実行します。これで **[統合サービス環境]** 以下に ISE の一覧と使用できるリージョンが表示されます。 リージョンではなく ISE を選択します。次に例を示します。

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、<a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps フォーラム</a>にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、<a href="http://aka.ms/logicapps-wish" target="_blank">Logic Apps のユーザー フィードバック サイト</a>にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の詳細を理解する
* [Azure サービスの仮想ネットワーク統合](../virtual-network/virtual-network-for-azure-services.md)について理解する
