---
title: Azure portal を使用して SAP ソリューション向け Azure Monitor をデプロイする
description: Azure portal を使用して SAP ソリューション向け Azure Monitor をデプロイする
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: a9208101777cd88f0237e661a414550759a069b0
ms.sourcegitcommit: aaba99b8b1c545ad5d19f400bcc2d30d59c63f39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2021
ms.locfileid: "108007383"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Azure portal を使用して SAP ソリューション向け Azure Monitor をデプロイする

SAP ソリューション向け Azure Monitor は、[Azure portal](https://azure.microsoft.com/features/azure-portal) を使用して作成できます。 この方法では、SAP ソリューション向け Azure Monitor をデプロイし、プロバイダーを構成するために、ブラウザーベースのユーザー インターフェイスが提供されます。

## <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする

Azure portal (https://portal.azure.com) にサインインする

## <a name="create-monitoring-resource"></a>監視リソースを作成する

1. **Azure Marketplace** から **[SAP ソリューション向け Azure Monitor]** を選択します。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="図は、Azure Marketplace から [SAP ソリューション向け Azure Monitor] を選択する例を示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. **[基本]** タブで、必要な値を指定します。 該当する場合は、既存の Log Analytics ワークスペースを使用できます。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Azure portal 構成オプションを示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. 仮想ネットワークを選択する際に、監視対象のシステムがその VNET 内から到達可能であることを確認してください。 

   > [!IMPORTANT]
   > Microsoft とデータを共有するために **[共有]** を選択すると、サポート チームに追加のサポートを提供できます。

## <a name="configure-providers"></a>プロバイダーを構成する

### <a name="sap-netweaver-provider"></a>SAP NetWeaver プロバイダー

#### <a name="prerequisites-for-adding-netweaver-provider"></a>NetWeaver プロバイダーを追加するための前提条件

"SAP start service" は、SAP システムの監視などのサービスのホストを提供します。 Microsoft は、これらの機能を公開する SOAP Web サービス インターフェイスである "SAPControl" を利用しています。 この SAPControl Web サービス インターフェイスにより、[保護された Web サービス メソッドと保護されていない Web サービス メソッド](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv)が区別されます。 特定のメトリックをフェッチできるようにするには、一部のメソッドの保護を解除する必要があります。 現在のリリースで必要なメソッドの保護を解除するには、SAP システムごとに次の手順を行います。

1. SAP サーバーへの SAP GUI 接続を開きます
2. 管理者アカウントを使用してログインします
3. トランザクション RZ10 を実行します
4. 適切なプロファイル (DEFAULT.PFL) を選択します
5. [Extended Maintenance] を選択し、[Change] をクリックします 
6. 影響を受けるパラメーター "service/protectedwebmethods" の値を "SDEFAULT -GetQueueStatistic –ABAPGetWPTable –EnqGetStatistic –GetProcessList" に変更し、[Copy] をクリックします
7. 戻って [Profile]、[Save] の順に選択します
8. システムを再起動して、パラメーターを有効にします

>[!Tip]
> サーバー ポートへのアクセスをフィルター処理するには、アクセス制御リスト (ACL) を使用します。 [SAP Note](https://launchpad.support.sap.com/#/notes/1495075) を参照してください

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>Azure portal に NetWeaver プロバイダーをインストールする
1.  前提条件の手順が完了していて、サーバーが再起動されていることを確認します
2.  Azure portal の AMS で、[プロバイダーの追加] を選択し、ドロップダウンから [SAP NetWeaver] を選択します
3.  SAP システムのホスト名とサブドメイン (該当する場合) を入力します
4.  入力したホスト名に対応するインスタンス番号を入力します 
5.  システム ID (SID) を入力します
6.  完了したら、[プロバイダーの追加] を選択します
7.  必要に応じて引き続きプロバイダーを追加するか、[確認と作成] を選択してデプロイを完了します

![image](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

### <a name="sap-hana-provider"></a>SAP HANA プロバイダー 

1. **[プロバイダー]** タブを選択して、構成するプロバイダーを追加します。 複数のプロバイダーを 1 つずつ追加することも、監視リソースのデプロイ後に追加することもできます。 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="[プロバイダー] タブで SAP ソリューション向け Azure Monitor に追加のプロバイダーを追加する例を示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. **[プロバイダーの追加]** を選択し、ドロップダウンから **[SAP HANA]** を選択します。 

   > [!IMPORTANT]
   > SAP HANA プロバイダーが SAP HANA "マスター" ノードに対して構成されていることを確認してください。

3. HANA サーバーのプライベート IP を入力します。

4. 使用するデータベース テナントの名前を入力します。 任意のテナントを選択できますが、**SYSTEMDB** を使用すると、さまざまな監視領域が有効になるため、これを使用することをお勧めします。 

5. HANA データベースに関連付けられている SQL ポート番号を入力します。 ポート番号は、 **[3]**  +  **[instance#]**  +  **[13]** の形式で入力する必要があります (例: 30013)。 

6. 使用するデータベース ユーザー名を入力します。 データベース ユーザーに **監視** および **カタログ読み取り** ロールが割り当てられていることを確認します。 

7. 完了したら、 **[プロバイダーの追加]** を選択します。 必要に応じて他のプロバイダーを続けて追加するか、 **[確認および作成]** を選択してデプロイを完了します。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="プロバイダー情報を追加するときの構成オプションの画像です。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::
   
### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server プロバイダー

1. Microsoft SQL Server プロバイダーを追加する前に、SQL Server Management Studio で次のスクリプトを実行して、プロバイダーの構成に必要な適切なアクセス許可を持つユーザーを作成する必要があります。

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. **[プロバイダーの追加]** を選択し、ドロップダウンから **[Microsoft SQL Server]** を選択します。 

3. Microsoft SQL Server に関連付けられている情報を使用して、フィールドに入力します。 

4. 完了したら、 **[プロバイダーの追加]** を選択します。 必要に応じて他のプロバイダーを続けて追加するか、 **[確認および作成]** を選択してデプロイを完了します。

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="画像は Microsoft SQL Server プロバイダーの追加に関連する情報を示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>高可用性クラスター (Pacemaker) プロバイダー

1. ドロップダウンから **[High-availability cluster (Pacemaker)]\(高可用性クラスター (Pacemaker)\)** を選択します。 

   > [!IMPORTANT]
   > 高可用性クラスター (Pacemaker) プロバイダーを構成するには、各ノードに ha_cluster_provider がインストールされていることを確認します。 詳細については、[HA クラスター エクスポーター](https://github.com/ClusterLabs/ha_cluster_exporter#installation)に関するセクションを参照してください。

2. Prometheus エンドポイントを http://IP:9664/metrics の形式で入力します。 
 
3. システム ID (SID)、ホスト名、およびクラスター名を入力します。

4. 完了したら、 **[プロバイダーの追加]** を選択します。 必要に応じて他のプロバイダーを続けて追加するか、 **[確認および作成]** を選択してデプロイを完了します。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="画像は HA クラスター Pacemaker プロバイダーに関連するオプションを示しています。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::

### <a name="os-linux-provider"></a>OS (Linux) プロバイダー 

1. ドロップダウンから [OS (Linux)] を選択します 

   >[!IMPORTANT]
   > OS (Linux) プロバイダーを構成するには、監視する各ホスト (BareMetal または VM) に最新バージョンの Node_Exporter がインストールされていることを確認します。 [最新版の Node Exporter](https://prometheus.io/download/#node_exporter) をインストールしてください。 詳細については、[こちら](https://github.com/prometheus/node_exporter)をご覧ください

2. 名前を入力します。これは、BareMetal インスタンスの識別子になります。
3. Node Exporter エンドポイントを http://IP:9100/metrics の形式で入力します。

   >[!IMPORTANT]
   >Linux ホストのプライベート IP アドレスを使用してください。 ホストと AMS リソースが同じ VNET 内にあることを確認してください。 

   >[!Note]
   > Linux ホストでファイアウォール ポート "9100" を開く必要があります。
   >(firewall-cmd を使用している場合: firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload。ufw を使用している場合: ufw allow 9100/tcp ufw reload)

    >[!Tip]
    > Linux ホストが Azure VM の場合は、該当するすべての NSG で、ソースとしての "VirtualNetwork" からの受信トラフィックがポート 9100 で許可されるようにしてください。
 
5. 完了したら、 **[プロバイダーの追加]** を選択します。 必要に応じて他のプロバイダーを続けて追加するか、 **[確認および作成]**  を選択してデプロイを完了します。 


## <a name="next-steps"></a>次のステップ

[SAP ソリューション向け Azure Monitor](azure-monitor-overview.md) の詳細
