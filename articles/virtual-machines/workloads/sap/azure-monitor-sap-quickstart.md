---
title: Azure portal を使用して Azure Monitor for SAP Solutions をデプロイする
description: ブラウザーの方法を使用して Azure Monitor for SAP Solutions をデプロイする方法について説明します。
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 32ded30543e018b86f4329d7d08be100f84831d0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027510"
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
      SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList

7. Go back and select **Profile** > **Save**.
8. After saving the changes for this parameter, please restart the SAPStartSRV service on each of the instances in the SAP system. (Restarting the services will not restart the SAP system; it will only restart the SAPStartSRV service (in Windows) or daemon process (in Unix/Linux))
   8a. On Windows systems, this can be done in a single window using the SAP Microsoft Management Console (MMC) / SAP Management Console(MC).  Right-click on each instance and choose All Tasks -> Restart Service.
![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png)

   8b. On Linux systems, use the below command where NN is the SAP instance number to restart the host which is logged into.
   
   ```RestartService
   sapcontrol -nr <NN> -function RestartService
   
9. Once the SAP service is restarted, please check to ensure the updated web method protection exclusion rules have been applied for each instance by running the following command: 

**Logged as \<sidadm\>** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods`

**Logged as different user** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>"`

   The output should look like :-
   ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)

10. To conclude and validate, a test query can be done against web methods to validate ( replace the hostname , instance number and method name ) leverage the below powershell script 

```Powershell command to test unprotect method 
$SAPHostName = "<hostname>"
$InstanceNumber = "<instancenumber>"
$Function = "ABAPGetWPTable"
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
$sapcntrluri = "https://&quot; + $SAPHostName + &quot;:5&quot; + $InstanceNumber + &quot;14/?wsdl"
$sapcntrl = New-WebServiceProxy -uri $sapcntrluri -namespace WebServiceProxy -class sapcntrl
$FunctionObject = New-Object ($sapcntrl.GetType().NameSpace + ".$Function")
$sapcntrl.$Function($FunctionObject)
```
11. **インスタンス プロファイルごとに手順 3 から 10 を繰り返します**。

>[!Important] 
>SAPControl Web メソッドの保護を解除するには、SAP システムの各インスタンスで sapstartsrv サービスが再起動されることが重要です。  NetWeaver プロバイダーが SAP システムからメトリック データをフェッチするにはこれらの読み取り専用 SOAP API が必要であり、これらのメソッドの保護を解除できなかった場合、NetWeaver のメトリック ブックの視覚化が空になるか表示されません。
   
>[!Tip]
> サーバー ポートへのアクセスをフィルター処理するには、アクセス制御リスト (ACL) を使用します。 詳細については、[こちらの SAP ノート](https://launchpad.support.sap.com/#/notes/1495075)を参照してください。

Azure portal で NetWeaver プロバイダーをインストールするには:

1. 前の前提条件の手順を完了していること、およびサーバーが再起動されていることを確認します。
1. Azure portal の **Azure Monitor for SAP Solutions** で、 **[プロバイダーの追加]** を選択してから次のようにします。

   1. **[種類]** で、 **[SAP NetWeaver]** を選択します。

   1. **[ホスト名]** に、SAP システムのホスト名を入力します。

   1. **[サブドメイン]** に、サブドメインを入力します (ある場合)。

   1. **[Instance No]\(インスタンス番号\)** に、入力したホスト名に対応するインスタンス番号を入力します。 

   1. **[SID]** に、システム ID を入力します。
   
   ![SAP NetWeaver プロバイダーを追加するための構成オプションを示すスクリーンショット。](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    終わったら、 **[プロバイダーの追加]** を選択します。 必要に応じてプロバイダーの追加を続けるか、 **[確認および作成]** を選択してデプロイを完了します。

>[!Important]
>SAP アプリケーション サーバー (つまり仮想マシン) が、Azure Active Directory によって管理されているもののように、ネットワーク ドメインの一部である場合は、対応するサブドメインを [サブドメイン] テキスト ボックスで指定することが重要です。  仮想ネットワーク内に存在する Azure Monitor for SAP コレクター VM はドメインに参加していないため、ホスト名が完全修飾ドメイン名でない限り、SAP システム内のインスタンスのホスト名を解決できません。  これを指定しなかった場合は、NetWeaver ブックに視覚化が存在しないか、不完全になります。
 
>たとえば、SAP システムのホスト名の完全修飾ドメイン名が "myhost.mycompany.global.corp" である場合は、[ホスト名] に「myhost」と入力し、[サブドメイン] で「mycompany.global.corp」と指定します。  NetWeaver プロバイダーが SAP システムで GetSystemInstanceList API を呼び出すと、SAP からシステム内のすべてのインスタンスのホスト名が返されます。  コレクター VM でこの一覧を使用して追加の API 呼び出しが行われ、各インスタンスの機能に固有のメトリックが取り込まれます (例: ABAP、J2EE、MESSAGESERVER、ENQUE、ENQREP など)。 指定した場合は、コレクター VM でサブドメイン "mycompany.global.corp" を使用して、SAP システム内の各インスタンスの完全修飾ドメイン名が作成されます。  
 
>SAP システムがネットワーク ドメインの一部である場合は、[ホスト名] フィールドで IP アドレスを指定しないでください。

   
### <a name="sap-hana-provider"></a>SAP HANA プロバイダー 

1. 構成するプロバイダーを追加するには、 **[プロバイダー]** タブを選択します。 複数のプロバイダーを 1 つずつ追加することも、監視リソースをデプロイした後で追加することもできます。 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="プロバイダーを追加するタブを示すスクリーンショット。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. **[プロバイダーの追加]** を選択した後、次のようにします。

   1. **[種類]** で、 **[SAP HANA]** を選択します。 

      > [!IMPORTANT]
      > SAP HANA プロバイダーが SAP HANA の `master` ノード用に構成されていることを確認します。

   1. **[IP アドレス]** に、HANA サーバーのプライベート IP アドレスを入力します。

   1. **[データベース テナント]** に、使用するテナントの名前を入力します。 任意のテナントを選択できますが、**SYSTEMDB** を使用すると、さまざまな監視領域が有効になるため、これを使用することをお勧めします。 

   1. **[SQL ポート]** に、HANA データベースに関連付けられているポート番号を入力します。 *[3]*  + <*インスタンス番号*> +  *[13]* という形式にする必要があります。 たとえば、**30013** のようになります。 

   1. **[データベース ユーザー名]** には、使用するユーザー名を入力します。 データベース ユーザーには、"*監視*" と "*カタログ読み取り*" のロールが割り当てられている必要があります。

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="SAP HANA プロバイダーを追加するための構成オプションを示すスクリーンショット。" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. 終わったら、 **[プロバイダーの追加]** を選択します。 必要に応じてプロバイダーの追加を続けるか、 **[確認および作成]** を選択してデプロイを完了します。

   
### <a name="microsoft-sql-server-provider"></a>Microsoft SQL Server プロバイダー

1. Microsoft SQL Server プロバイダーを追加する前に、SQL Server Management Studio で次のスクリプトを実行して、プロバイダーを構成するための適切なアクセス許可を持つユーザーを作成します。

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
