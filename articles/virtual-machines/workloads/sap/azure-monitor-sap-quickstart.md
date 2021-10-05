---
title: Azure portal を使用して Azure Monitor for SAP Solutions をデプロイする
description: ブラウザーの方法を使用して Azure Monitor for SAP Solutions をデプロイする方法について説明します。
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 85cfe6887ded3844e2143754c31a3c6efee5e132
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579462"
---
# <a name="deploy-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Azure portal を使用して Azure Monitor for SAP Solutions をデプロイする

この記事では、[Azure portal](https://azure.microsoft.com/features/azure-portal) から Azure Monitor for SAP Solutions をデプロイする手順について説明します。 ポータルのブラウザー ベースのインターフェイスを使用して、Azure Monitor for SAP Solutions のデプロイとプロバイダーの構成の両方を行います。

## <a name="sign-in-to-the-portal"></a>ポータルにサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="create-a-monitoring-resource"></a>監視リソースを作成する

1. **Marketplace** で **Azure Monitor for SAP Solutions** を選択します。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Azure Marketplace からの Azure Monitor for SAP Solutions オファーの選択を示すスクリーンショット。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. **[基本]** タブで、必要な値を指定します。 該当する場合は、既存の Log Analytics ワークスペースを使用できます。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="[基本] タブの構成オプションを示すスクリーンショット。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

   仮想ネットワークを選択するときは、監視対象のシステムにその仮想ネットワーク内から到達できることを確認してください。 

   > [!IMPORTANT]
   > **[Share data with Microsoft support]\(Microsoft サポートとデータを共有する\)** で **[Share]\(共有する\)** を選択すると、サポート チームはお客様のトラブルシューティングをお手伝いできます。

## <a name="configure-providers"></a>プロバイダーを構成する

### <a name="sap-netweaver-provider"></a>SAP NetWeaver プロバイダー

SAP 起動サービスにより、SAP システムの監視など、サービスのホストが提供されます。 Microsoft は、これらの機能を公開する SOAP Web サービス インターフェイスである SAPControl を使用しています。 SAPControl Web サービス インターフェイスでは、[保護された Web サービス メソッドと保護されていない Web サービス メソッド](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv)が区別されます。 

特定のメトリックをフェッチするには、現在のリリースの一部のメソッドの保護を解除する必要があります。 各 SAP システムで次の手順のようにします。

1. SAP サーバーへの SAP GUI 接続を開きます。
2. 管理アカウントを使用してサインインします。
3. トランザクション RZ10 を実行します。
4. 適切なプロファイル (*DEFAULT.PFL*) を選択します。
5. **[Extended Maintenance]\(拡張メンテナンス\)**  >  **[Change]\(変更\)** を選択します。 
6. プロファイル パラメーター "service/protectedwebmethods" を選択して次の値に変更し、[Copy]\(コピー\) をクリックします。  

   ```service/protectedwebmethods instruction
      SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList```

7. Go back and select **Profile** > **Save**.
8. After saving the changes for this parameter, please restart the SAPStartSRV service on each of the instances in the SAP system. (Restarting the services will not restart the SAP system; it will only restart the SAPStartSRV service (in Windows) or daemon process (in Unix/Linux))
   8a. On Windows systems, this can be done in a single window using the SAP Microsoft Management Console (MMC) / SAP Management Console(MC).  Right-click on each instance and choose All Tasks -> Restart Service.
![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png)

   8b. On Linux systems, use the below command where NN is the SAP instance number to restart the host which is logged into.
   
   ```RestartService
   sapcontrol -nr <NN> -function RestartService```
   
9. Once the SAP service is restarted, please check to ensure the updated web method protection exclusion rules have been applied for each instance by running the following command: 

**Logged as \<sidadm\>** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods`

**Logged as different user** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>"`

   The output should look like :-
   ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)

10. To conclude and validate, a test query can be done against web methods to validate the connection by logging into each instance and running the following commands:

    - For all instances : `sapcontrol -nr <NN> -function GetProcessList`
    - For the ENQUE instance : `sapcontrol -nr <NN> -function EnqGetStatistic`
    - For ABAP instances : `sapcontrol -nr <NN> -function ABAPGetWPTable`
    - For ABAP/J2EE/JEE instances : `sapcontrol -nr <NN> -function GetQueueStatistic`

>[!Important] 
>It is critical that the sapstartsrv service is restarted on each instance of the SAP system for the SAPControl web methods to be unprotected.  These read-only SOAP API are required for the NetWeaver provider to fetch metric data from the SAP System and failure to unprotect these methods will lead to empty or missing visualizations on the NetWeaver metric workbook.
   
>[!Tip]
> Use an access control list (ACL) to filter the access to a server port. For more information, see [this SAP note](https://launchpad.support.sap.com/#/notes/1495075).

To install the NetWeaver provider on the Azure portal:

1. Make sure you've completed the earlier prerequisite steps and that the server has been restarted.
1. On the Azure portal, under **Azure Monitor for SAP Solutions**, select **Add provider**, and then:

   1. For **Type**, select **SAP NetWeaver**.

   1. For **Hostname**, enter the host name of the SAP system.

   1. For **Subdomain**, enter a subdomain if one applies.

   1. For **Instance No**, enter the instance number that corresponds to the host name you entered. 

   1. For **SID**, enter the system ID.
   
   ![Screenshot showing the configuration options for adding a SAP NetWeaver provider.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

>[!Important]
>If the SAP application servers (ie. virtual machines) are part of a network domain, such as one managed by Azure Active Directory, then it is critical that the corresponding subdomain is provided in the Subdomain text box.  The Azure Monitor for SAP collector VM that exists inside the Virtual Network is not joined to the domain and as such will not be able to resolve the hostname of instances inside the SAP system unless the hostname is a fully qualified domain name.  Failure to provide this will result in missing / incomplete visualizations in the NetWeaver workbook.
 
>For example, if the hostname of the SAP system has a fully qualified domain name of "myhost.mycompany.global.corp" then please enter a Hostname of "myhost" and provide a Subdomain of "mycompany.global.corp".  When the NetWeaver provider invokes the GetSystemInstanceList API on the SAP system, SAP returns the hostnames of all instances in the system.  The collector VM will use this list to make additional API calls to fetch metrics specific to each instance's features (e.g.  ABAP, J2EE, MESSAGESERVER, ENQUE, ENQREP, etc…). If specified, the collector VM will then use the subdomain  "mycompany.global.corp" to build the fully qualified domain name of each instance in the SAP system.  
 
>Please DO NOT specify an IP Address for the hostname field if the SAP system is a part of network domain.

   
### SAP HANA provider 

1. Select the **Providers** tab to add the providers you want to configure. You can add multiple providers one after another, or add them after you deploy the monitoring resource. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Screenshot showing the tab where you add providers." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. Select **Add provider**, and then:

   1. For **Type**, select **SAP HANA**. 

      > [!IMPORTANT]
      > Ensure that a SAP HANA provider is configured for the SAP HANA `master` node.

   1. For **IP address**, enter the private IP address for the HANA server.

   1. For **Database tenant**, enter the name of the tenant you want to use. You can choose any tenant, but we recommend using **SYSTEMDB** because it enables a wider array of monitoring areas. 

   1. For **SQL port**, enter the port number associated with your HANA database. It should be in the format of *[3]* + *[instance#]* + *[13]*. An example is **30013**. 

   1. For **Database username**, enter the username you want to use. Ensure the database user has the *monitoring* and *catalog read* roles assigned.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Screenshot showing configuration options for adding an SAP HANA provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

   
### Microsoft SQL Server provider

1. Before you add the Microsoft SQL Server provider, run the following script in SQL Server Management Studio to create a user with the appropriate permissions for configuring the provider.

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

1. **[プロバイダーの追加]** を選択した後、次のようにします。

   1. **[種類]** で、 **[Microsoft SQL Server]** を選択します。 

   1. 残りのフィールドは、SQL Server インスタンスに関連付けられている情報を使用して入力します。 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Microsoft SQL Server プロバイダーを追加するための構成オプションを示すスクリーンショット。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

1. 終わったら、 **[プロバイダーの追加]** を選択します。 必要に応じてプロバイダーの追加を続けるか、 **[確認および作成]** を選択してデプロイを完了します。

### <a name="high-availability-cluster-pacemaker-provider"></a>高可用性クラスター (Pacemaker) プロバイダー

高可用性 (Pacemaker) クラスター用のプロバイダーを追加する前に、環境に適したエージェントをインストールしてください。

**SUSE** ベースのクラスターの場合は、ha_cluster_provider が各ノードにインストールされていることを確認します。 [HA クラスター エクスポーター](https://github.com/ClusterLabs/ha_cluster_exporter#installation)のインストール方法に関するページを参照してください。 サポートされている SUSE のバージョン: SLES for SAP 12 SP3 以降。  
   
**RHEL** ベースのクラスターの場合は、Performance Co-Pilot (PCP) と pcp-pmda-hacluster サブパッケージが各ノードにインストールされていることを確認します。 PCP HACLUSTER エージェントのインストール方法に関するページを参照してください (https://access.redhat.com/articles/6139852) 。 サポートされている RHEL のバージョン: 8.2、8.4 以降。
 
上記の前提条件のインストールを完了した後、クラスター ノードごとにプロバイダーを作成します。

1. **[プロバイダーの追加]** を選択した後、次のようにします。

1. **[種類]** で、 **[High-availability cluster (Pacemaker)]\(高可用性クラスター (Pacemaker)\)** を選択します。 
   
1. **[HA Cluster Exporter Endpoint]\(HA クラスター エクスポーター エンドポイント\)** にエンドポイント URL を入力して、クラスターの各ノード用にプロバイダーを構成します。 **SUSE** ベースのクラスターの場合は、「**http://\<IP  address\>:9664/metrics**」と入力します。 **RHEL** ベースのクラスターの場合は、「**http://\<IP address\>:44322/metrics?names=ha_cluster**」と入力します
 
1. システム ID、ホスト名、クラスター名を各ボックスに入力します。
   
   > [!IMPORTANT]
   > ホスト名とは、VM での実際のホスト名のことです。 SUSE ベースと RHEL ベースどちらのクラスターでも、"hostname -s" コマンドを使用してください。  

1. 終わったら、 **[プロバイダーの追加]** を選択します。 必要に応じてプロバイダーの追加を続けるか、 **[確認および作成]** を選択してデプロイを完了します。

### <a name="os-linux-provider"></a>OS (Linux) プロバイダー 

1. **[プロバイダーの追加]** を選択した後、次のようにします。

   1. **[種類]** で、 **[OS (Linux)]** を選択します。 

      >[!IMPORTANT]
      > OS (Linux) プロバイダーを構成するには、監視する各ホスト (BareMetal または仮想マシン) に[最新バージョンの node_exporter](https://prometheus.io/download/#node_exporter) がインストールされていることを確認します。 [詳細については、こちらを参照してください](https://github.com/prometheus/node_exporter)。

   1. **[名前]** に、BareMetal インスタンスの識別子になる名前を入力します。

   1. **[Node Exporter Endpoint]\(ノード エクスポーター エンドポイント\)** に、「 **http://IP:9100/metrics** 」と入力します。

      >[!IMPORTANT]
      >Linux ホストのプライベート IP アドレスを使用します。 SAP リソース用のホストと Azure Monitor が同じ仮想ネットワーク内にあることを確認します。 
      >
      >Linux ホストでファイアウォール ポート 9100 を開く必要があります。 `firewall-cmd` を使用している場合は、次のコマンドを使用します。 
      >
      >`firewall-cmd --permanent --add-port=9100/tcp`
      >
      >`firewall-cmd --reload`
      >
      >`ufw` を使用している場合は、次のコマンドを使用します。
      >
      >`ufw allow 9100/tcp`
      >
      >`ufw reload`
      >
      > Linux ホストが Azure 仮想マシン (VM) である場合は、該当するすべてのネットワーク セキュリティ グループで、ソースとして `VirtualNetwork` からのポート 9100 でのインバウンド トラフィックが許可されていることを確認します。
 
1. 終わったら、 **[プロバイダーの追加]** を選択します。 必要に応じてプロバイダーの追加を続けるか、 **[確認および作成]**  を選択してデプロイを完了します。 


## <a name="next-steps"></a>次のステップ

Azure Monitor for SAP Solutions の詳細について学習します。

> [!div class="nextstepaction"]
> [SAP on Azure の監視](monitor-sap-on-azure.md)
