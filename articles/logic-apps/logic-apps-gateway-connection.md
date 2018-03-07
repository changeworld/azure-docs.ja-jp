---
title: "Azure Logic Apps 用のオンプレミスのデータ ソースにアクセスする | Microsoft Docs"
description: "オンプレミス データ ゲートウェイを設定して、ロジック アプリからオンプレミスのデータ ソースにアクセスできるようにします"
keywords: "データへのアクセス, オンプレミス, データ転送, 暗号化, データ ソース"
services: logic-apps
author: jeffhollan
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/14/2017
ms.author: LADocs; millopis; estfan
ms.openlocfilehash: 5160913b2f08a04f0a985d8ddadd6641a664b258
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="connect-to-data-sources-on-premises-from-logic-apps-with-on-premises-data-gateway"></a>オンプレミス データ ゲートウェイを使用して、ロジック アプリからオンプレミスのデータに接続する

ロジック アプリからオンプレミスのデータ ソースにアクセスするには、サポートされているコネクタでロジック アプリが使用できるオンプレミス データ ゲートウェイを設定します。 このゲートウェイは、オンプレミスのデータ ソースとロジック アプリ間でのデータ転送と暗号化を高速で行うブリッジとして機能します。 ゲートウェイは、オンプレミスのソースから、Azure Service Bus 経由の暗号化されたチャネルでデータを転送します。 すべてのトラフィックは、ゲートウェイ エージェントからの安全な送信トラフィックとして生成されます。 詳細については、[データ ゲートウェイのしくみ](logic-apps-gateway-install.md#gateway-cloud-service)に関するセクションを参照してください。 

このゲートウェイは、次のオンプレミスのデータ ソースへの接続をサポートします。

*   BizTalk Server 2016
*   DB2  
*   ファイル システム
*   Informix
*   MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP アプリケーション サーバー 
*   SAP メッセージ サーバー
*   SharePoint
*   SQL Server
*   Teradata

以下の手順は、ロジック アプリでオンプレミス データ ゲートウェイが動作するように設定する方法を示しています。 サポートされる接続の詳細については、[Azure Logic Apps のコネクタ](../connectors/apis-list.md)に関するページを参照してください。 

他のサービスでゲートウェイを使用する方法については、次の記事を参照してください。

*   [Microsoft Power BI オンプレミス データ ゲートウェイ](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
*   [Azure Analysis Services オンプレミス データ ゲートウェイ](../analysis-services/analysis-services-gateway.md)
*   [Microsoft Flow オンプレミス データ ゲートウェイ](https://flow.microsoft.com/documentation/gateway-manage/)
*   [Microsoft PowerApps オンプレミス データ ゲートウェイ](https://powerapps.microsoft.com/tutorials/gateway-management/)

## <a name="requirements"></a>必要条件

* [ローカル コンピューターへのデータ ゲートウェイのインストール](logic-apps-gateway-install.md)を済ませている必要があります。

* Azure ポータルにサインインするときは、[オンプレミス データ ゲートウェイのインストール](logic-apps-gateway-install.md#requirements)に使用したものと同じ職場または学校アカウントを使用する必要があります。 サインイン アカウントには、ゲートウェイのインストール用に Azure ポータルでゲートウェイ リソースを作成するときに使用する Azure サブスクリプションも必要です。

* Azure ゲートウェイ リソースから既に要求されているゲートウェイのインストールは要求できません。 ゲートウェイのインストールは 1 つの Azure ゲートウェイ リソースにのみ関連付けることができます。 要求は、他のリソースがインストールを使用できないようにゲートウェイのリソースを作成したときに発生します。

* オンプレミス データ ゲートウェイは、Windows サービスとして実行され、Windows サービスのログオン資格情報として `NT SERVICE\PBIEgwService` を使用するように設定されています。 Azure Portal でゲートウェイ リソースを作成し、保守するには、[Windows サービス アカウント](../logic-apps/logic-apps-gateway-install.md)に**共同作成者**以上のアクセス許可が必要です。 

  > [!NOTE]
  > Windows サービス アカウントは、オンプレミスのデータ ソースへの接続に使用するアカウントとも、クラウド サービスへのサインインに使用する職場または学校アカウントとも異なります。

## <a name="install-the-on-premises-data-gateway"></a>オンプレミス データ ゲートウェイのインストール

インストールが済んでいない場合は、[オンプレミス データ ゲートウェイのインストール](logic-apps-gateway-install.md)手順に従います。 ローカル コンピューターにデータ ゲートウェイがインストールされたことを確認してから、他の手順に進んでください。

<a name="create-gateway-resource"></a>

## <a name="create-an-azure-resource-for-the-on-premises-data-gateway"></a>オンプレミス データ ゲートウェイ向けの Azure リソースの作成

ローカル コンピューターにゲートウェイをインストールした後、データ ゲートウェイをリソースとして Azure 内に作成する必要があります。 この手順でも、ゲートウェイ リソースを Azure サブスクリプションに関連付けます。

1. [Azure Portal](https://portal.azure.com "Azure Portal") にサインインします。 ゲートウェイをインストールするために使用したのと同じ Azure の職場または学校の電子メール アドレスを必ず使用してください。

2. Azure のメイン メニューで、**[リソースの作成]** > **[エンタープライズ統合]** > **[オンプレミスのデータ ゲートウェイ]** の順に選びます。

   !["オンプレミス データ ゲートウェイ" を見つける](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. **[接続ゲートウェイの作成]** ページで、次の詳細を指定して、データ ゲートウェイ リソースを作成します。

    * **名前**: ゲートウェイ リソースの名前を入力します。 

    * **サブスクリプション**: ゲートウェイ リソースに関連付ける Azure サブスクリプションを選択します。 
    このサブスクリプションは、ロジック アプリと同じサブスクリプションである必要があります。
   
      既定のサブスクリプションは、サインインするために使用した Azure アカウントに基づきます。

    * **リソース グループ**: ゲートウェイ リソースをデプロイするリソース グループを作成するか、既存のリソース グループを選択します。 
    リソース グループを使用して、関連する Azure 資産をコレクションとして管理できます。

    * **場所**: Azure では、この場所は、[ゲートウェイのインストール](logic-apps-gateway-install.md)中にゲートウェイ クラウド サービス向けに選択したリージョンと同じリージョンに限定されます。 

      > [!NOTE]
      > ゲートウェイ リソースの場所とゲートウェイ クラウド サービスの場所が一致していることを確認してください。 一致していないと、ゲートウェイのインストールは、次の手順で選択するインストール済みのゲートウェイの一覧に表示されない可能性があります。
      > 
      > ゲートウェイ リソースとロジック アプリでは、異なるリージョンを使用できます。

    * **インストール名**: ゲートウェイのインストールが既に選択されていない場合は、前にインストールしたゲートウェイを選択します。 

    ゲートウェイ リソースを Azure ダッシュボードに追加するには、**[ダッシュボードにピン留め]** を選択します。 
    操作が完了したら、**[作成]** を選択します。

    例: 

    ![詳細を指定してオンプレミス データ ゲートウェイを作成する](./media/logic-apps-gateway-connection/createblade.png)

    必要なときにデータ ゲートウェイを検索したり表示したりするには、Azure のメイン メニューで **[すべてのサービス]** を選びます。 
    検索ボックスに「オンプレミスのデータ ゲートウェイ」と入力し、**[オンプレミスのデータ ゲートウェイ]** を選びます。

    !["オンプレミスのデータ ゲートウェイ" を見つける](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-your-logic-app-to-the-on-premises-data-gateway"></a>ロジック アプリのオンプレミス データ ゲートウェイへの接続

データ ゲートウェイ リソースの作成と Azure サブスクリプションへのリソースの関連付けが終わったので、ロジック アプリとデータ ゲートウェイ間の接続を作成します。

> [!NOTE]
> ゲートウェイ接続の場所はロジック アプリと同じリージョンに存在する必要がありますが、異なるリージョンに存在するデータ ゲートウェイを使用することができます。

1. Azure ポータルで、ロジック アプリ デザイナーでロジック アプリを作成するか開きます。

2. オンプレミス接続をサポートするコネクタ (SQL Server など) を選択します。

3. 下に示されている順序に従って、**[オンプレミスのデータ ゲートウェイ経由で接続する]** を選択し、一意の接続名と必要な情報を指定し、使用するデータ ゲートウェイ リソースを選択します。 操作が完了したら、**[作成]** を選択します。

   > [!TIP]
   > 一意の接続名を使用すると、後でその接続を簡単に識別できます。複数の接続を作成する場合は特に便利です。 該当する場合は、ユーザー名の完全修飾ドメイン名も含めてください。 

   ![ロジック アプリとデータ ゲートウェイ間の接続を作成する](./media/logic-apps-gateway-connection/blankconnection.png)

お疲れさまでした。これで、ロジック アプリでゲートウェイの接続を使用する準備が整いました。

## <a name="edit-your-gateway-connection-settings"></a>データ ゲートウェイ接続設定の編集

ロジック アプリ向けのゲートウェイ接続を作成した後、その特定の接続の設定を後で更新することができます。

1. ゲートウェイ接続を検索するには:

   * ロジック アプリ メニューの **[開発ツール]** で、**[API 接続]** を選択します。 
   
     **[API 接続]** ウィンドウに、データ ゲートウェイ接続を含む、ロジック アプリに関連付けられているすべての API 接続が表示されます。

     ![ロジック アプリに移動し、[API 接続] を選択する](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * または、Azure のメイン メニューで、**[その他のサービス]** > **[Web + モバイル]** > **[API 接続]** の順に移動します。Azure サブスクリプションに関連付けられているすべての API 接続 (ゲートウェイ接続を含む) が表示されます。 

   * または、Azure のメイン メニューで、すべての API 接続の **[すべてのリソース]** を選択します。Azure サブスクリプションに関連付けられている、ゲートウェイ接続を含むすべての API 接続が表示されます。

2. 表示または編集するゲートウェイ接続を選択し、**[API 接続の編集]** を選択します。

   > [!TIP]
   > 更新が反映されない場合は、[ゲートウェイの Windows サービスを停止して再起動](./logic-apps-gateway-install.md#restart-gateway)してみてください。

<a name="change-delete-gateway-resource"></a>

## <a name="switch-or-delete-your-on-premises-data-gateway-resource"></a>オンプレミス データ ゲートウェイ リソースの切り替えまたは削除

別のゲートウェイ リソースを作成するには、ゲートウェイを別のリソースに関連付けるか、ゲートウェイ リソースを削除します。ゲートウェイ リソースは、ゲートウェイのインストールに影響を与えずに削除できます。 

1. Azure のメイン メニューから **[すべてのリソース]** を選択します。 
2. データ ゲートウェイ リソースを検索して選択します。
3. **[オンプレミス データ ゲートウェイ]** を選択し、リソース ツールバーの **[削除]** を選択します。

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>よく寄せられる質問

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>次の手順

* [ロジック アプリをセキュリティで保護する](./logic-apps-securing-a-logic-app.md)
* [ロジック アプリの接続の例とシナリオ](./logic-apps-examples-and-scenarios.md)
