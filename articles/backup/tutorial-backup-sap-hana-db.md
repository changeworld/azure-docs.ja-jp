---
title: チュートリアル - Azure VM での SAP HANA データベースのバックアップ
description: このチュートリアルでは、Azure VM 上で稼働している SAP HANA データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について学習します。
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 475c742cae311cc1d816c7b879af61aa09fe882e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709845"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>チュートリアル:Azure VM での SAP HANA データベースのバックアップ

このチュートリアルでは、Azure VM 上で稼働している SAP HANA データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について説明します。 この記事では、以下を行う方法について説明します。

> [!div class="checklist"]
>
> * コンテナーを作成して構成する
> * データベースを検出する
> * バックアップを構成する

現在サポートされているすべてのシナリオについては、[こちら](sap-hana-backup-support-matrix.md#scenario-support)を参照してください。

## <a name="prerequisites"></a>前提条件

バックアップを構成する前に、必ず、次の操作を行ってください。

* SAP HANA を実行する VM として、同じリージョンおよびサブスクリプションの [Recovery Services コンテナー](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)を特定または作成します。
* VM からインターネットへの接続を許可して、Azure に到達できるようにします。以下の「[ネットワーク接続を設定する](#set-up-network-connectivity)」の手順を参照してください。
* SAP HANA Server VM 名とリソース グループ名とを組み合わせた長さが、Azure Resource Manager (ARM_ VM) の場合は 84 文字、クラシック VM の場合は 77 文字を超えないようにしてください。 この制限は、一部の文字がサービスによって予約されていることに起因します。
* 次の条件を満たすキーが **hdbuserstore** に存在する必要があります。
  * 既定の **hdbuserstore** に存在する。 既定値は、SAP HANA がインストールされている `<sid>adm` アカウントです。
  * MDC の場合、キーが **NAMESERVER** の SQL ポートを指すこと。 SDC の場合は、**INDEXSERVER** の SQL ポートを指す必要があります
  * ユーザーを追加したり削除したりするための資格情報があること。
  * このキーは、事前登録スクリプトを正常に実行した後に削除できることに注意してください
* また、上記の手順に従ってカスタム キーを作成する代わりに、**hdbuserstore** の既存の HANA SYSTEM ユーザーのキーを作成することも選択できます。
* HANA がインストールされている仮想マシンで、SAP HANA バックアップ構成スクリプト (事前登録スクリプト) をルート ユーザーとして実行します。 [このスクリプト](https://go.microsoft.com/fwlink/?linkid=2173610)で、バックアップに備えて HANA システムの準備を行い、上記の手順で作成したキーを入力として渡すように求めます。 この入力をパラメーターとしてスクリプトに渡す方法については、「[事前登録スクリプトで実行される処理](#what-the-pre-registration-script-does)」セクションを参照してください。 ここでは、事前登録スクリプトで実行される処理の詳細についても説明されています。
* HANA セットアップでプライベート エンドポイントが使用されている場合は、 [事前登録スクリプト](https://go.microsoft.com/fwlink/?linkid=2173610)を *-sn* または *--skip-network-checks* パラメーターを指定して実行します。

>[!NOTE]
>事前登録スクリプトは、RHEL (7.4、7.6、および 7.7) 上で実行されている SAP HANA ワークロードには **compat-unixODBC234** をインストールし、RHEL 8.1 には **unixODBC** をインストールします。 [このパッケージは、RHEL for SAP HANA (RHEL 7 Server の場合) の SAP ソリューション用更新サービス (RPM) リポジトリ](https://access.redhat.com/solutions/5094721)にあります。  Azure Marketplace RHEL イメージの場合、リポジトリは **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms** になります。

## <a name="set-up-network-connectivity"></a>ネットワーク接続を設定する

すべての操作において、Azure VM で実行されている SAP HANA データベースには Azure Backup サービス、Azure Storage、Azure Active Directory への接続が必要です。 これは、プライベート エンドポイントを使用するか、必要なパブリック IP アドレスまたは FQDN へのアクセスを許可することによって実現できます。 必要な Azure サービスへの適切な接続を許可しないと、データベースの検出、バックアップの構成、バックアップの実行、データの復元などの操作の失敗につながる可能性があります。

次の表に、接続の確立に使用できるさまざまな選択肢を示します。

| **オプション**                        | **長所**                                               | **短所**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| プライベート エンドポイント                 | 仮想ネットワーク内のプライベート IP 経由でのバックアップを可能にする  <br><br>   ネットワークとコンテナーの側で詳細な制御を提供する | 標準のプライベート エンドポイント <bpt id="p1">[</bpt>コスト<ept id="p1">](https://azure.microsoft.com/pricing/details/private-link/)</ept>が発生する |
| NSG サービス タグ                  | 範囲の変更が自動的にマージされるため管理しやすい   <br><br>   追加のコストが発生しない | NSG でのみ使用可能  <br><br>    サービス全体へのアクセスを提供する |
| Azure Firewall の FQDN タグ          | 必要な FQDN が自動的に管理されるため管理しやすい | Azure Firewall でのみ使用可能                         |
| サービスの FQDN/IP へのアクセスを許可する | 追加のコストが発生しない   <br><br>  すべてのネットワーク セキュリティ アプライアンスとファイアウォールで動作する | 広範な IP または FQDN のセットへのアクセスが必要になる場合がある   |
| HTTP プロキシを使用する                 | VM に対するインターネット アクセスを単一の場所で実現                       | プロキシ ソフトウェアで VM を実行するための追加のコストが発生する         |
| [仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)    |     Azure Storage (= Recovery Services コンテナー) に使用できます。     <br><br>     データ プレーン トラフィックのパフォーマンスを最適化する上で大きなメリットがあります。          |         Azure AD、Azure Backup サービスには使用できません。    |
| ネットワーク仮想アプライアンス      |      Azure Storage、Azure AD、Azure Backup サービスに使用できます。 <br><br> **データ プレーン**   <ul><li>      Azure Storage: `*.blob.core.windows.net`、`*.queue.core.windows.net`  </li></ul>   <br><br>     <bpt id="p1">**</bpt>管理プレーン<ept id="p1">**</ept>  <ul><li>  Azure AD:「[Microsoft 365 Common および Office Online](/microsoft-365/enterprise/urls-and-ip-address-ranges?view=o365-worldwide&preserve-view=true#microsoft-365-common-and-office-online)」のセクション 56 および 59 に記載されている FQDN へのアクセスを許可します。 </li><li>   Azure Backup サービス: `.backup.windowsazure.com` </li></ul> <br>[Azure Firewall のサービス タグ](../firewall/fqdn-tags.md)の詳細を参照してください。       |  データ プレーン トラフィックにオーバーヘッドが追加され、スループットとパフォーマンスが低下します。  |

これらのオプションを使用する方法の詳細については、以下を参照してください。

### <a name="private-endpoints"></a>プライベート エンドポイント

プライベート エンドポイントを使用すると、仮想ネットワーク内のサーバーから Recovery Services コンテナーに安全に接続できます。 プライベート エンドポイントでは、お使いのコンテナーの VNET アドレス空間からのプライベート IP が使用されます。 仮想ネットワーク内のリソースとコンテナー間のネットワーク トラフィックは、仮想ネットワークと Microsoft のバックボーン ネットワーク上のプライベート リンクを経由します。 これにより、パブリック インターネットへの露出が排除されます。 プライベート エンドポイントは仮想ネットワークの特定のサブネットに割り当てられ、Azure Active Directory には使用できません。 <bpt id="p1">[</bpt>こちら<ept id="p1">](./private-endpoints.md)</ept>から、Azure Backup のプライベート エンドポイントの詳細を参照してください。

### <a name="nsg-tags"></a>NSG タグ

ネットワーク セキュリティ グループ (NSG) を使用する場合は、<bpt id="p1">*</bpt>AzureBackup<ept id="p1">*</ept> サービス タグを使用して、Azure Backup への発信アクセスを許可します。 Azure Backup タグに加えて、Azure AD (<bpt id="p2">*</bpt>AzureActiveDirectory<ept id="p2">*</ept>) および Azure Storage (<bpt id="p3">*</bpt>Storage<ept id="p3">*</ept>) に対して同様の <bpt id="p1">[</bpt>NSG 規則<ept id="p1">](../virtual-network/network-security-groups-overview.md#service-tags)</ept>を作成することによって、認証とデータ転送のための接続を許可する必要もあります。 次の手順では、Azure Backup タグの規則を作成するプロセスについて説明します。

1. **[すべてのサービス]** で、 **[ネットワーク セキュリティ グループ]** に移動して、ネットワーク セキュリティ グループを選択します。

1. <bpt id="p2">**</bpt>[設定]<ept id="p2">**</ept> で <bpt id="p1">**</bpt>[送信セキュリティ規則]<ept id="p1">**</ept> を選択します。

1. **[追加]** を選択します。 <bpt id="p1">[</bpt>セキュリティ規則の設定<ept id="p1">](../virtual-network/manage-network-security-group.md#security-rule-settings)</ept>の説明に従って、新しい規則を作成するために必要なすべての詳細を入力します。 オプション <bpt id="p1">**</bpt>[宛先]<ept id="p1">**</ept> が <bpt id="p2">*</bpt>[サービス タグ]<ept id="p2">*</ept> に、 <bpt id="p3">**</bpt>[宛先サービス タグ]<ept id="p3">**</ept> が <bpt id="p4">*</bpt>[AzureBackup]<ept id="p4">*</ept> に設定されていることを確認します。

1. **[追加]** を選択して、新しく作成した送信セキュリティ規則を保存します。

Azure Storage と Azure AD に対する [NSG 送信セキュリティ規則](../virtual-network/network-security-groups-overview.md#service-tags)も、同様に作成できます。 サービス タグの詳細については、[こちらの記事](../virtual-network/service-tags-overview.md)を参照してください。

### <a name="azure-firewall-tags"></a>Azure Firewall タグ

Azure Firewall を使用している場合は、<bpt id="p1">*</bpt>AzureBackup<ept id="p1">*</ept> <bpt id="p2">[</bpt>Azure Firewall FQDN タグ<ept id="p2">](../firewall/fqdn-tags.md)</ept>を使用してアプリケーション規則を作成します。 これにより、Azure Backup へのすべての発信アクセスが許可されます。

### <a name="allow-access-to-service-ip-ranges"></a>サービスの IP 範囲へのアクセスを許可する

サービスの IP へのアクセスを許可することを選択した場合は、<bpt id="p1">[</bpt>こちら<ept id="p1">](https://www.microsoft.com/download/confirmation.aspx?id=56519)</ept>から利用可能な IP 範囲の JSON ファイルを参照してください。 Azure Backup、Azure Storage、および Azure Active Directory に対応する IP へのアクセスを許可する必要があります。

### <a name="allow-access-to-service-fqdns"></a>サービスの FQDN へのアクセスを許可する

次の FQDN を使用することで、サーバーから必要なサービスへのアクセスを許可することもできます。

| サービス    | アクセスするドメイン名                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | <bpt id="p1">[</bpt>この記事<ept id="p1">](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)</ept>に従って、セクション 56 および 59 の FQDN へのアクセスを許可します |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>トラフィックをルーティングするために HTTP プロキシ サーバーを使用する

> [!NOTE]
> 現時点では、SAP HANA のプロキシはサポートされていません。 HANA VM の Azure バックアップを使用したデータベース バックアップの送信接続要件を取り除くには、プライベート エンド ポイントなどの他のオプションを検討してください。

## <a name="understanding-backup-and-restore-throughput-performance"></a>バックアップと復元のスループット パフォーマンスについて

SAP HANA Azure VM では、Backint によってバックアップ (ログとそれ以外) が実現されますが、このバックアップは (Azure Storage Blob を内部で使用する) Azure Recovery Services コンテナーへのストリームであるため、このストリーミング手法について理解することが重要となります。

HANA の Backint コンポーネントは、ディスクに接続された "パイプ" (情報を読み取るためのパイプと書き込むためのパイプ) の役割を果たします。基になるディスクには、データベース ファイルが存在します。それらが Azure Backup サービスによって読み取られて、リモート Azure Storage アカウントである Azure Recovery Services コンテナーに転送されます。 Azure Backup サービスでは、Backint のネイティブ検証チェックとは別に、チェックサムによってストリームが検証されます。 これらの検証によって、Azure Recovery Services コンテナーにあるデータが確かに信頼できるものであり、回復可能であることが確認されます。

ストリームの処理対象になるのは主にディスクであるため、バックアップと復元のパフォーマンスを正確に測定するためには、ディスクの読み取りパフォーマンスとバックアップ データを転送するためのネットワーク パフォーマンスを把握する必要があります。 Azure VM におけるディスクまたはネットワークのスループットとパフォーマンスを深く理解するために、[こちらの記事](../virtual-machines/disks-performance.md)を参照してください。 これらはバックアップと復元のパフォーマンスにも当てはまります。

**Azure Backup サービスは、ログ バックアップ以外 (完全、差分、増分など) で最大約 420 MBps を、HANA のログ バックアップで最大 100 MBps を達成しようと試みます**。 前述のように、これらの速度は保証されているわけではなく、次の要因に左右されます。

- VM のキャッシュ不使用時の最大ディスク スループット - データまたはログ領域から読み取ります。
- 基になるディスクの種類とそのスループット - データまたはログ領域から読み取ります。
- VM の最大ネットワーク スループット - Recovery Services コンテナーに書き込みます。
- VNET に NVA またはファイアウォールがある場合はそのネットワーク スループット
- Azure NetApp Files にデータまたはログがある場合 - ANF からの読み取りとコンテナーへの書き込みの両方により VM のネットワークが使用されます。

> [!IMPORTANT]
> キャッシュ不使用時のディスク スループットが 400 MBps を超えないまでも、それに限りなく近いような小さな VM では、ディスク全体の IOPS がバックアップ サービスによって消費されるおそれがあり、ディスクに対する読み取り/書き込みに関連した SAP HANA の動作に影響を及ぼす可能性があります。 そのようなケースで、バックアップ サービスの消費をスロットル (制限) して上限を超えないようにしたい場合は、次のセクションを参照してください。

### <a name="limiting-backup-throughput-performance"></a>バックアップのスループット パフォーマンスを制限する

バックアップ サービスのディスク IOPS 消費をスロットルして上限値を超えないようにしたい場合は、次の手順を実行してください。

1. "opt/msawb/bin" フォルダーに移動します
2. "ExtensionSettingOverrides.JSON" という名前の新しい JSON ファイルを作成します
3. JSON ファイルに、次のキーと値のペアを追加します。

    ```json
    {
    "MaxUsableVMThroughputInMBPS": 200
    }
    ```

4. ファイルのアクセス許可と所有権を次のように変更します。
    
    ```bash
    chmod 750 ExtensionSettingsOverrides.json
    chown root:msawb ExtensionSettingsOverrides.json
    ```

5. サービスを再起動する必要はありません。 Azure Backup サービスは、このファイルの記述に従ってスループット パフォーマンスの制限を試みます。

## <a name="what-the-pre-registration-script-does"></a>事前登録スクリプトで実行される処理

事前登録スクリプトでは、次の機能が実行されます。

* ご利用の Linux ディストリビューションに基づいて、スクリプトにより、Azure Backup エージェントに必要なすべてのパッケージがインストールまたは更新されます。
* Azure Backup サーバーと依存サービス (Azure Active Directory、Azure Storage など) に対するアウトバウンド ネットワーク接続チェックを実行します。
* [前提条件](#prerequisites)の 1 つとして指定されているカスタム ユーザー キーまたは SYSTEM ユーザー キーを使用して、HANA システムにログインします。 これは、バックアップ ユーザー (AZUREWLBACKUPHANAUSER) を HANA システムに作成するために使用されます。また、ユーザー キーは、事前登録スクリプトが正常に実行された後で削除できます。 _SYSTEM ユーザー キーは削除できないことに注意してください。_
* AZUREWLBACKUPHANAUSER には、次の必要なロールとアクセス許可が割り当てられます。
  * MDC の場合: DATABASE ADMIN および BACKUP ADMIN (HANA 2.0 SPS05 以降): 復元中に新しいデータベースを作成します。
  * SDC の場合: BACKUP ADMIN: 復元中に新しいデータベースを作成します。
  * CATALOG READ: バックアップ カタログを読み取ります。
  * SAP_INTERNAL_HANA_SUPPORT いくつかのプライベート テーブルにアクセスします。 HANA 2.0 SPS04 Rev 46 未満の SDC および MDC バージョンでのみ必要となります。 HANA 2.0 SPS04 Rev 46 以上では必要ありません。必要な情報は、HANA チームからの修正プログラムと共に、パブリック テーブルから入手することになります。
* このスクリプトにより、HANA バックアップ プラグインですべての操作 (データベース クエリ、復元操作、バックアップの構成と実行) を処理するために AZUREWLBACKUPHANAUSER の **hdbuserstore** にキーが追加されます。
* または、独自のカスタム Backup ユーザーを作成することを選択できます。 このユーザーに、次の必要なロールとアクセス許可が割り当てられていることを確認してください。
  * MDC の場合: DATABASE ADMIN および BACKUP ADMIN (HANA 2.0 SPS05 以降): 復元中に新しいデータベースを作成します。
  * SDC の場合: BACKUP ADMIN: 復元中に新しいデータベースを作成します。
  * CATALOG READ: バックアップ カタログを読み取ります。
  * SAP_INTERNAL_HANA_SUPPORT いくつかのプライベート テーブルにアクセスします。 HANA 2.0 SPS04 Rev 46 未満の SDC および MDC バージョンでのみ必要となります。 これは、HANA 2.0 SPS04 Rev 46 以上では必要ありません。必要な情報は、HANA チームからの修正プログラムと共に、パブリック テーブルから入手することになります。
* 次に、HANA バックアップ プラグインですべての操作 (データベース クエリ、復元操作、構成、バックアップの実行) を処理するために、カスタム Backup ユーザーの hdbuserstore にキーを追加します。 このカスタム Backup ユーザー キーを、パラメーター `-bk CUSTOM_BACKUP_KEY_NAME` または `-backup-key CUSTOM_BACKUP_KEY_NAME` としてスクリプトに渡します。  _このカスタム バックアップ キーのパスワードの期限切れが、バックアップと復元の失敗につながる可能性があることに注意してください。_

>[!NOTE]
> このスクリプトで受け取るその他のパラメーターを確認するには、`bash msawb-plugin-config-com-sap-hana.sh --help` コマンドを使用してください。

キーの作成を確認するには、SIDADM 資格情報を使用して HANA コンピューター上で HDBSQL コマンドを実行します。

```hdbsql
hdbuserstore list
```

このコマンド出力には、AZUREWLBACKUPHANAUSER として示されるユーザーと共に {SID}{DBNAME} キーが表示されます。

>[!NOTE]
> `/usr/sap/{SID}/home/.hdb/` の下に固有の一連の SSFS ファイルがあることを確認してください。 このパスにはフォルダーが 1 つしか存在しません。

事前登録スクリプトの実行を完了するために必要な手順の概要を次に示します。 このフローでは、事前登録スクリプトへの入力パラメーターとして SYSTEM ユーザー キーを提供していることに注意してください。

| 担当者     |    ソース    |    実行対象    |    説明    |
| --- | --- | --- | --- |
| `<sid>`adm (OS) |    HANA OS   | チュートリアルを読み、事前登録スクリプトをダウンロードします。  |    チュートリアル: [Azure VM での SAP HANA データベースのバックアップ]()   <br><br>    [事前登録スクリプト](https://go.microsoft.com/fwlink/?linkid=2173610)をダウンロードします |
| `<sid>`adm (OS)    |    HANA OS    |   HANA を開始します (HDB start)    |   セットアップの前に、HANA が稼働していることを確認します。   |
| `<sid>`adm (OS)   |   HANA OS  |    次のコマンドを実行します。 <br>  `hdbuserstore Set`   |  `hdbuserstore Set SYSTEM <hostname>:3<Instance#>13 SYSTEM <password>`  <br><br>   **注:** <br>  IP アドレスまたは FQDN ではなく、ホスト名を必ず使用してください。   |
| `<sid>`adm (OS)   |  HANA OS    |   次のコマンドを実行します。<br> `hdbuserstore List`   |  結果に次のような既定のストアが含まれているかどうかを確認します。 <br><br> `KEY SYSTEM`  <br> `ENV : <hostname>:3<Instance#>13`    <br>  `USER : SYSTEM`   |
| root (OS)   |   HANA OS    |    [Azure Backup HANA 事前登録スクリプト](https://go.microsoft.com/fwlink/?linkid=2173610)を実行します。     | `./msawb-plugin-config-com-sap-hana.sh -a --sid <SID> -n <Instance#> --system-key SYSTEM`    |
| `<sid>`adm (OS)   |   HANA OS   |    次のコマンドを実行します。 <br> `hdbuserstore List`   |   結果に次のような新しい行が結果に含まれているかどうかを確認します。 <br><br>  `KEY AZUREWLBACKUPHANAUSER` <br>  `ENV : localhost: 3<Instance#>13`   <br> `USER: AZUREWLBACKUPHANAUSER`    |
| Azure 共同作成者     |    Azure portal    |   Azure Backup サービス、Azure AD、および Azure Storage への送信トラフィックを許可するように、NSG、NVA、Azure Firewall などを構成します。     |    [ネットワーク接続を設定する](#set-up-network-connectivity)    |
| Azure 共同作成者 |   Azure portal    |   Recovery Services コンテナーを作成するかまたは開いて、HANA バックアップを選択します。   |   バックアップするすべてのターゲット HANA VM を検出します。   |
| Azure 共同作成者    |   Azure portal    |   HANA データベースを検出し、バックアップ ポリシーを構成します。   |  次に例を示します。 <br><br>  週次バックアップ: 毎週日曜日午前 2 時、保持期間は週単位で 12 週間、月単位で 12 か月、年単位で 3 年間   <br>   差分または増分: 日曜日を除く毎日    <br>   ログ: 15 分ごと、保持期間は 35 日間    |
| Azure 共同作成者  |   Azure portal    |    Recovery Service コンテナー – バックアップ項目 – SAP HANA     |   バックアップ ジョブ (Azure ワークロード) を確認します。    |
| HANA 管理者    | HANA Studio   | Backup Console、Backup カタログ、backup.log、backint.log、および globa.ini を確認します   |    SYSTEMDB とテナント データベースの両方。   |

事前登録スクリプトが正常に実行されたことを確認したら、[接続要件](#set-up-network-connectivity)の確認に進み、次に Recovery Services コンテナーから[バックアップを構成](#discover-the-databases)できます。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは、経時的に作成されたバックアップと復旧ポイントを格納するエンティティです。 Recovery Services コンテナーには、保護される仮想マシンに関連付けられたバックアップ ポリシーも含まれます。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com/) でサブスクリプションにサインインします。

2. 左側のメニューから、 **[すべてのサービス]** を選択します。

   ![[すべてのサービス] を選択する](./media/tutorial-backup-sap-hana-db/all-services.png)

3. **[すべてのサービス]** ダイアログ ボックスに、「**Recovery Services**」と入力します。 入力に従って、リソースの一覧がフィルター処理されます。 リソースの一覧から **[Recovery Services コンテナー]** を選択します。

   ![Recovery Services コンテナーを選択する](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. **[Recovery Services コンテナー]** ダッシュボードで **[追加]** を選択します。

   ![Recovery Services コンテナーを追加する](./media/tutorial-backup-sap-hana-db/add-vault.png)

   **[Recovery Services コンテナー]** ダイアログ ボックスが開きます。 **[名前]、[サブスクリプション]、[リソース グループ]** 、および **[場所]** に値を入力します。

   ![Recovery Services コンテナーの作成](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Name**:この名前は、Recovery Services コンテナーを識別するために使用されるため、Azure サブスクリプションに対して一意である必要があります。 2 文字以上で、50 文字以下の名前を指定します。 名前の先頭にはアルファベットを使用する必要があります。また、名前に使用できるのはアルファベット、数字、ハイフンのみです。 このチュートリアルでは、**SAPHanaVault** という名前を使用しています。
   * **サブスクリプション**:使用するサブスクリプションを選択します。 1 つのサブスクリプションのみのメンバーの場合は、その名前が表示されます。 どのサブスクリプションを使用すればよいかがわからない場合は、既定 (推奨) のサブスクリプションを使用してください。 職場または学校アカウントが複数の Azure サブスクリプションに関連付けられている場合に限り、複数の選択肢が存在します。 ここでは、**SAP HANA solution lab subscription** サブスクリプションを使用しています。
   * **[リソース グループ]** :既存のリソース グループを使用するか、新しいリソース グループを作成します。 ここでは、**SAPHANADemo** を使用しています。<br>
   サブスクリプションの使用可能なリソース グループの一覧を表示するには、 **[既存のものを使用]** を選択し、ドロップダウン リスト ボックスからリソースを選択します。 新しいリソース グループを作成するには、 **[新規作成]** を選択し、名前を入力します。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」を参照してください。
   * **[場所]** :コンテナーの地理的リージョンを選択します。 このコンテナーは、SAP HANA を実行している仮想マシンと同じリージョンにある必要があります。 ここでは、 **[米国東部 2]** を使用しています。

5. **[確認および作成]** を選択します。

   ![[確認および作成] を選択する](./media/tutorial-backup-sap-hana-db/review-create.png)

これで、Recovery Services コンテナーが作成されました。

## <a name="enable-cross-region-restore"></a>リージョンをまたがる復元を有効にする

Recovery Services コンテナーでは、リージョンをまたがる復元を有効にできます。 HANA データベースでバックアップを構成および保護する前に、リージョンをまたがる復元を有効にする必要があります。 [リージョンをまたがる復元を有効にする方法](./backup-create-rs-vault.md#set-cross-region-restore)に関する記事を参照してください。

リージョンをまたがる復元に関する[詳細情報を参照してください](./backup-azure-recovery-services-vault-overview.md)。

## <a name="discover-the-databases"></a>データベースを検出する

1. コンテナーの <bpt id="p1">**</bpt>[作業の開始]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt>[バックアップ]<ept id="p2">**</ept> を選択します。 <bpt id="p1">**</bpt>[ワークロードはどこで実行されていますか?]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt>[Azure VM の SAP HANA]<ept id="p2">**</ept> を選択します。
2. <bpt id="p1">**</bpt>[検出の開始]<ept id="p1">**</ept> を選択します。 これで、コンテナー リージョン内の保護されていない Linux VM の検出が開始されます。 保護する Azure VM が表示されます。
3. **[仮想マシンの選択]** で、リンクを選択して、データベースの検出のために SAP HANA VM へのアクセス許可を Azure Backup サービスに与えるスクリプトをダウンロードします。
4. バックアップする SAP HANA データベースをホストしている VM 上でスクリプトを実行します。
5. VM 上でスクリプトを実行した後、 **[仮想マシンの選択]** で VM を選択します。 次に、 **[Discover DBs]\(データベースを検出\)** を選択します。
6. Azure Backup によって、VM 上のすべての SAP HANA データベースが検出されます。 検出中に、Azure Backup によって VM がコンテナーに登録され、VM に拡張機能がインストールされます。 エージェントはデータベースにインストールされません。

   ![データベースを検出する](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>バックアップの構成

バックアップするデータベースが検出されたら、バックアップを有効にしましょう。

1. **[バックアップの構成]** を選択します。

   ![バックアップの構成](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. **[Select items to back up]\(バックアップする項目の選択\)** で、保護するデータベースを 1 つ以上選択し、 **[OK]** を選択します。

   ![バックアップする項目を選択する](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. **[バックアップ ポリシー] > [バックアップ ポリシーの選択]** で、次のセクションの手順に従って、データベースの新しいバックアップ ポリシーを作成します。

   ![バックアップ ポリシーを選択する](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. ポリシーを作成した後、 **[バックアップ] メニュー** の **[バックアップの有効化]** を選択します。

   ![[バックアップの有効化] の選択](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. ポータルの **[通知]** 領域で、バックアップ構成の進行状況を追跡します。

## <a name="creating-a-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保持される期間を定義します。

* ポリシーはコンテナー レベルで作成されます。
* 複数のコンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。

次のように、ポリシー設定を指定します。

1. **[ポリシー名]** に新しいポリシーの名前を入力します。 この場合は、「**SAPHANA**」と入力します。

   ![新しいポリシーの名前を入力する](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. **[Full Backup policy]\(完全バックアップのポリシー\)** で、 **[バックアップ頻度]** を選択します。 **[Daily]\(毎日\)** または **[毎週]** を選択できます。 このチュートリアルでは、 **[Daily]\(毎日\)** バックアップを選択しています。

   ![バックアップ頻度を選択する](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. **[リテンション期間]** で、完全バックアップのリテンション期間の設定を構成します。
   * 既定では、すべてのオプションが選択されています。 使用しない保持期間の制限をすべてクリアして、使用するものを設定します。
   * あらゆる種類のバックアップ (完全、差分、ログ) の最小保持期間は 7 日間です。
   * 復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。
   * 特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間と設定に基づいて保持されます。
   * 月次および年次のリテンション期間の範囲でも、同様の動作になります。
4. **完全バックアップのポリシー** メニューで、 **[OK]** を選択して設定を確定します。
5. 次に、 **[差分バックアップ]** を選択して、差分ポリシーを追加します。
6. **差分バックアップのポリシー** で、 **[有効]** を選択して頻度とリテンション期間の制御を開きます。 ここでは、毎週 **日曜日** の **午前 2:00** に実行され、**30 日間** 保持される差分バックアップを有効にしました。

   ![差分バックアップ ポリシー](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >毎日のバックアップとしては、差分バックアップまたは増分バックアップのどちらかを選択できます。両方を選択することはできません。

7. <bpt id="p1">**</bpt>[増分バックアップ ポリシー]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt>[有効]<ept id="p2">**</ept> を選択して頻度と保有期間の制御を開きます。
    * 最多で、1 日に 1 回の増分バックアップをトリガーできます。
    * 増分バックアップは、最大 180 日間保持できます。 より長いリテンション期間が必要な場合は、完全バックアップを使用する必要があります。

    ![増分バックアップ ポリシー](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

8. <bpt id="p1">**</bpt>[OK]<ept id="p1">**</ept> を選択してポリシーを保存し、 <bpt id="p2">**</bpt>[バックアップ ポリシー]<ept id="p2">**</ept> のメイン メニューに戻ります。
9. **[ログ バックアップ]** を選択し、トランザクション ログ バックアップ ポリシーを追加します。
   * **[ログ バックアップ]** は既定で **[有効]** に設定されています。 SAP HANA ではすべてのログ バックアップが管理されるため、これを無効にすることはできません。
   * ここでは、バックアップのスケジュールとして **2 時間** を設定し、保持期間として **15 日間** を設定しています。

    ![ログ バックアップのポリシー](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > ログ バックアップでは、1 回の完全バックアップが正常に完了した後にのみ、フローが開始されます。
   >

10. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。
11. バックアップ ポリシーの定義が完了した後、 **[OK]** を選択します。

これで、SAP HANA データベースのバックアップが正常に構成されました。

## <a name="next-steps"></a>次の手順

* [Azure VM で稼働している SAP HANA データベースでオンデマンド バックアップを実行する](backup-azure-sap-hana-database.md#run-an-on-demand-backup)方法を学習する
* [Azure VM で稼働している SAP HANA データベースを復元する](sap-hana-db-restore.md)方法を学習する
* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](sap-hana-db-manage.md)方法を学習する
* [SAP HANA データベースのバックアップ時に発生する一般的な問題をトラブルシューティングする](backup-azure-sap-hana-database-troubleshoot.md)方法を学習する
