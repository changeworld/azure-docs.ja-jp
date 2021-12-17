---
title: Azure Backup を使用して Azure に SAP HANA データベースをバックアップする
description: この記事では、Azure Backup サービスを使用して SAP HANA データベースを Azure 仮想マシンにバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 4a903db39405ff930dd3433cb6188e6d5d2aa69e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714449"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Azure VM での SAP HANA データベースのバックアップ

SAP HANA データベースは、低い回復ポイントの目標値 (RPO) と長期リテンション期間を必要とする重要なワークロードです。 <bpt id="p1">[</bpt>Azure Backup<ept id="p1">](backup-overview.md)</ept> を使用して、Azure 仮想マシン (VM) 上で稼働している SAP HANA データベースをバックアップできます。

この記事では、Azure VM 上で稼働している SAP HANA データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について説明します。

この記事では、次の方法について学習します。
> [!div class="checklist"]
>
> * コンテナーを作成して構成する
> * データベースを検出する
> * バックアップを構成する
> * オンデマンド バックアップ ジョブを実行する

>[!NOTE]
サポートされている構成とシナリオの詳細については、「[SAP HANA バックアップのサポート マトリックス](sap-hana-backup-support-matrix.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

バックアップ用にデータベースを設定するには、「<bpt id="p1">[</bpt>前提条件<ept id="p1">](tutorial-backup-sap-hana-db.md#prerequisites)</ept>」と「<bpt id="p2">[</bpt>事前登録スクリプトで実行される処理<ept id="p2">](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)</ept>」のセクションを参照してください。

### <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

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

#### <a name="private-endpoints"></a>プライベート エンドポイント

プライベート エンドポイントを使用すると、仮想ネットワーク内のサーバーから Recovery Services コンテナーに安全に接続できます。 プライベート エンドポイントでは、お使いのコンテナーの VNET アドレス空間からの IP アドレスが使用されます。 仮想ネットワーク内のリソースとコンテナー間のネットワーク トラフィックは、仮想ネットワークと Microsoft のバックボーン ネットワーク上のプライベート リンクを経由します。 これにより、パブリック インターネットへの露出が排除されます。 <bpt id="p1">[</bpt>こちら<ept id="p1">](./private-endpoints.md)</ept>から、Azure Backup のプライベート エンドポイントの詳細を参照してください。

#### <a name="nsg-tags"></a>NSG タグ

ネットワーク セキュリティ グループ (NSG) を使用する場合は、<bpt id="p1">*</bpt>AzureBackup<ept id="p1">*</ept> サービス タグを使用して、Azure Backup への発信アクセスを許可します。 Azure Backup タグに加えて、Azure AD (<bpt id="p2">*</bpt>AzureActiveDirectory<ept id="p2">*</ept>) および Azure Storage (<bpt id="p3">*</bpt>Storage<ept id="p3">*</ept>) に対して同様の <bpt id="p1">[</bpt>NSG 規則<ept id="p1">](../virtual-network/network-security-groups-overview.md#service-tags)</ept>を作成することによって、認証とデータ転送のための接続を許可する必要もあります。  次の手順では、Azure Backup タグの規則を作成するプロセスについて説明します。

1. **[すべてのサービス]** で、 **[ネットワーク セキュリティ グループ]** に移動して、ネットワーク セキュリティ グループを選択します。

1. <bpt id="p2">**</bpt>[設定]<ept id="p2">**</ept> で <bpt id="p1">**</bpt>[送信セキュリティ規則]<ept id="p1">**</ept> を選択します。

1. **[追加]** を選択します。 <bpt id="p1">[</bpt>セキュリティ規則の設定<ept id="p1">](../virtual-network/manage-network-security-group.md#security-rule-settings)</ept>の説明に従って、新しい規則を作成するために必要なすべての詳細を入力します。 オプション <bpt id="p1">**</bpt>[宛先]<ept id="p1">**</ept> が <bpt id="p2">*</bpt>[サービス タグ]<ept id="p2">*</ept> に、 <bpt id="p3">**</bpt>[宛先サービス タグ]<ept id="p3">**</ept> が <bpt id="p4">*</bpt>[AzureBackup]<ept id="p4">*</ept> に設定されていることを確認します。

1. **[追加]** を選択して、新しく作成した送信セキュリティ規則を保存します。

Azure Storage と Azure AD に対する NSG 送信セキュリティ規則も、同様に作成できます。 サービス タグの詳細については、[こちらの記事](../virtual-network/service-tags-overview.md)を参照してください。

#### <a name="azure-firewall-tags"></a>Azure Firewall タグ

Azure Firewall を使用している場合は、<bpt id="p1">*</bpt>AzureBackup<ept id="p1">*</ept> <bpt id="p2">[</bpt>Azure Firewall FQDN タグ<ept id="p2">](../firewall/fqdn-tags.md)</ept>を使用してアプリケーション規則を作成します。 これにより、Azure Backup へのすべての発信アクセスが許可されます。

#### <a name="allow-access-to-service-ip-ranges"></a>サービスの IP 範囲へのアクセスを許可する

サービスの IP へのアクセスを許可することを選択した場合は、<bpt id="p1">[</bpt>こちら<ept id="p1">](https://www.microsoft.com/download/confirmation.aspx?id=56519)</ept>から利用可能な IP 範囲の JSON ファイルを参照してください。 Azure Backup、Azure Storage、および Azure Active Directory に対応する IP へのアクセスを許可する必要があります。

#### <a name="allow-access-to-service-fqdns"></a>サービスの FQDN へのアクセスを許可する

次の FQDN を使用することで、サーバーから必要なサービスへのアクセスを許可することもできます。

| サービス    | アクセスするドメイン名                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` <br><br> `*.blob.storage.azure.net` |
| Azure AD      | <bpt id="p1">[</bpt>この記事<ept id="p1">](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)</ept>に従って、セクション 56 および 59 の FQDN へのアクセスを許可します |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>トラフィックをルーティングするために HTTP プロキシ サーバーを使用する

> [!NOTE]
> 現時点では、SAP HANA のプロキシはサポートされていません。 HANA VM の Azure バックアップを使用したデータベース バックアップの送信接続要件を取り除くには、プライベート エンド ポイントなどの他のオプションを検討してください。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="enable-cross-region-restore"></a>リージョンをまたがる復元を有効にする

Recovery Services コンテナーでは、リージョンをまたがる復元を有効にできます。 HANA データベースでバックアップを構成および保護する前に、リージョンをまたがる復元を有効にする必要があります。 [リージョンをまたがる復元を有効にする方法](./backup-create-rs-vault.md#set-cross-region-restore)に関する記事を参照してください。

リージョンをまたがる復元に関する[詳細情報を参照してください](./backup-azure-recovery-services-vault-overview.md)。

## <a name="discover-the-databases"></a>データベースを検出する

1. Azure portal で、 **[バックアップ センター]** に移動し、 **[+ バックアップ]** をクリックします。

   :::image type="content" source="./media/backup-azure-sap-hana-database/backup-center-configure-inline.png" alt-text="SAP HANA データベースのチェックを開始することを示すスクリーンショット。" lightbox="./media/backup-azure-sap-hana-database/backup-center-configure-expanded.png":::

1. データソースの種類として **[Azure VM の SAP HANA]** を選び、バックアップに使う Recovery Services コンテナーを選んで、 **[続行]** をクリックします。

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-select-vault.png" alt-text="Azure VM での SAP HANA データベースの選択を示すスクリーンショット。":::

1. <bpt id="p1">**</bpt>[検出の開始]<ept id="p1">**</ept> を選択します。 これで、コンテナー リージョン内の保護されていない Linux VM の検出が開始されます。

   * 検出後、保護されていない VM は、ポータルで名前およびリソース グループ別に一覧表示されます。
   * VM が予期したとおりに一覧表示されない場合は、それが既にコンテナーにバックアップされているかどうかを確認してください。
   * 複数の VM を同じ名前にすることはできますが、それらは異なるリソース グループに属しています。

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-discover-databases.png" alt-text="[検出の開始] の選択を示すスクリーンショット。":::

1. <bpt id="p1">**</bpt>[仮想マシンの選択]<ept id="p1">**</ept> で、リンクをクリックして、データベースの検出のために SAP HANA VM へのアクセス許可を Azure Backup サービスに与えるスクリプトをダウンロードします。
1. バックアップする SAP HANA データベースをホストしている各 VM でスクリプトを実行します。
1. VM でスクリプトを実行した後、 <bpt id="p1">**</bpt>[仮想マシンの選択]<ept id="p1">**</ept> で、VM を選択します。 次に、 <bpt id="p1">**</bpt>[データベースを検出]<ept id="p1">**</ept> を選択します。
1. Azure Backup によって、VM 上のすべての SAP HANA データベースが検出されます。 検出中に、Azure Backup によって VM がコンテナーに登録され、VM に拡張機能がインストールされます。 エージェントはデータベースにインストールされません。

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-select-virtual-machines-inline.png" alt-text="検出された SAP HANA データベースを示すスクリーンショット。" lightbox="./media/backup-azure-sap-hana-database/hana-select-virtual-machines-expanded.png":::

## <a name="configure-backup"></a>バックアップの構成  

ここでバックアップを有効にします。

1. 手順 2 で、 <bpt id="p1">**</bpt>[バックアップの構成]<ept id="p1">**</ept> を選択します。

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-configure-backups.png" alt-text="バックアップの構成を示すスクリーンショット。":::

2. <bpt id="p1">**</bpt>[バックアップする項目の選択]<ept id="p1">**</ept> で、保護するデータベースをすべて選択し、 <bpt id="p2">**</bpt>[OK]<ept id="p2">**</ept> を選びます。

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-select-databases-inline.png" alt-text="バックアップするデータベースの選択を示すスクリーンショット。" lightbox="./media/backup-azure-sap-hana-database/hana-select-databases-expanded.png":::

3. <bpt id="p1">**</bpt>[バックアップ ポリシー]<ept id="p1">**</ept> <ph id="ph1"> > </ph> <bpt id="p2">**</bpt>[バックアップ ポリシーの選択]<ept id="p2">**</ept> で、以下の手順に従って、データベースの新しいバックアップ ポリシーを作成します。

   :::image type="content" source="./media/backup-azure-sap-hana-database/hana-policy-summary.png" alt-text="バックアップ ポリシーの選択を示すスクリーンショット。":::

4. ポリシーを作成した後、 <bpt id="p1">**</bpt>[バックアップ] メニュー<ept id="p1">**</ept>の <bpt id="p2">**</bpt>[バックアップの有効化]<ept id="p2">**</ept> を選択します。

    ![バックアップの有効化](./media/backup-azure-sap-hana-database/enable-backup.png)
5. ポータルの **[通知]** 領域で、バックアップ構成の進行状況を追跡します。

### <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保持される期間を定義します。

* ポリシーはコンテナー レベルで作成されます。
* 複数のコンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。

>[!NOTE]
>Azure Backup では、Azure VM で実行されている SAP HANA データベースをバックアップしている場合、夏時間変更に合わせた自動調整は行われません。
>
>必要に応じて手動でポリシーを変更してください。

次のように、ポリシー設定を指定します。

1. **[ポリシー名]** に新しいポリシーの名前を入力します。

   ![ポリシー名を入力する](./media/backup-azure-sap-hana-database/policy-name.png)
1. <bpt id="p1">**</bpt>完全バックアップのポリシー<ept id="p1">**</ept>で <bpt id="p2">**</bpt>[バックアップ頻度]<ept id="p2">**</ept> を選択し、 <bpt id="p3">**</bpt>[毎日]<ept id="p3">**</ept> または <bpt id="p4">**</bpt>[毎週]<ept id="p4">**</ept> を選びます。
   * <bpt id="p1">**</bpt>日次<ept id="p1">**</ept>:バックアップ ジョブが開始される、時刻とタイム ゾーンを選択します。
       * 完全バックアップを実行する必要があります。 このオプションをオフにすることはできません。
       * <bpt id="p1">**</bpt>[完全バックアップ]<ept id="p1">**</ept> を選択し、ポリシーを表示します。
       * 日次の完全バックアップを選択する場合は、差分バックアップを作成できません。
   * <bpt id="p1">**</bpt>毎週<ept id="p1">**</ept>:バックアップ ジョブが実行される、曜日、時刻、およびタイム ゾーンを選択します。

   ![バックアップの頻度を選択する](./media/backup-azure-sap-hana-database/backup-frequency.png)

1. **[リテンション期間]** で、完全バックアップのリテンション期間の設定を構成します。
    * 既定では、すべてのオプションが選択されています。 使用しないリテンション期間の制限をすべてクリアして、使用するものを設定します。
    * あらゆる種類のバックアップ (完全、差分、ログ) の最小保持期間は 7 日間です。
    * 復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。
    * 特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間と設定に基づいて保持されます。
    * 月次および年次のリテンション期間の範囲でも、同様の動作になります。

1. **完全バックアップのポリシー** メニューで、 **[OK]** を選択して設定を確定します。
1. <bpt id="p1">**</bpt>[差分バックアップ]<ept id="p1">**</ept> を選択して、差分ポリシーを追加します。
1. **差分バックアップのポリシー** で、 **[有効]** を選択して頻度とリテンション期間の制御を開きます。
    * 最多で、1 日に 1 回の差分バックアップをトリガーできます。
    * 差分バックアップは、最大 180 日間保持できます。 より長いリテンション期間が必要な場合は、完全バックアップを使用する必要があります。

    ![差分バックアップ ポリシー](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 毎日のバックアップとしては、差分バックアップまたは増分バックアップのどちらかを選択できます。両方を選択することはできません。
1. <bpt id="p1">**</bpt>[増分バックアップ ポリシー]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt>[有効]<ept id="p2">**</ept> を選択して頻度と保有期間の制御を開きます。
    * 最多で、1 日に 1 回の増分バックアップをトリガーできます。
    * 増分バックアップは、最大 180 日間保持できます。 より長いリテンション期間が必要な場合は、完全バックアップを使用する必要があります。

    ![増分バックアップ ポリシー](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

1. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。
1. **[ログ バックアップ]** を選択し、トランザクション ログ バックアップ ポリシーを追加します。
    * <bpt id="p1">**</bpt>[ログ バックアップ]<ept id="p1">**</ept> で、 <bpt id="p2">**</bpt>[有効化]<ept id="p2">**</ept> を選択します。  すべてのログ バックアップは SAP HANA で管理されるため、これを無効にすることはできません。
    * 頻度とリテンション期間の制御を設定します。

    > [!NOTE]
    > ログ バックアップでは、完全バックアップが正常に完了した後にのみ、フローが開始されます。

1. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。
1. バックアップ ポリシーの定義が完了した後、 **[OK]** を選択します。

> [!NOTE]
> 各ログ バックアップは、復旧チェーンを形成するために、以前の完全バックアップにチェーンされています。 この完全バックアップは、前回のログ バックアップのリテンション期間が終了するまで保持されます。 これは、完全バックアップのリテンション期間を追加して、すべてのログが確実に復旧されるようにすることを意味します。 ユーザーが、週単位の完全バックアップ、日単位の差分、2 時間ごとのログを実行しているとしましょう。 これらのすべてが 30 日間保持されます。 ただし、週単位の完全バックアップは、次の完全バックアップが利用可能になった後でのみ、すなわち 30 + 7 日後に、実際にクリーンアップまたは削除することができます。 たとえば、週単位の完全バックアップが 11 月 16 日に行われたとします。 保持ポリシーに従って、12 月 16 日まで保持されます。 この完全バックアップに対する前回のログ バックアップは、11 月 22 日に予定されている次の完全バックアップの前に行われます。 このログが 12 月 22 日までに利用可能になるまでは、11 月 16 日の完全バックアップは削除されません。 そのため、11 月 16 日の完全バックアップは、12 月 22 日までは保持されます。

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

バックアップは、ポリシー スケジュールに従って実行されます。 次のように、バックアップ オンデマンを実行できます。

1. コンテナー メニューで **[バックアップ項目]** を選択します。
2. **[バックアップ項目]** で、SAP HANA データベースを実行している VM を選択してから、 **[今すぐバックアップ]** を選択します。
3. **[今すぐバックアップ]** で、実行するバックアップの種類を選択します。 **[OK]** をクリックします。 このバックアップは 45 日間保持されます。
4. ポータルの通知を監視します。 コンテナー ダッシュボードの **[バックアップ ジョブ]**  >  **[進行中]** でジョブの進行状況を監視できます。 データベースのサイズによっては、最初のバックアップの作成に時間がかかる場合があります。

既定では、オンデマンド バックアップの保持期間は 45 日です。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Azure Backup が有効になっているデータベースで SAP HANA Studio のバックアップを実行する

Azure Backup でバックアップされているデータベースの (HANA Studio を使用する) ローカル バックアップを作成する場合は、次の操作を行います。

1. データベースの完全バックアップまたはログ バックアップがすべて完了するまで待ちます。 SAP HANA Studio/Cockpit で状態を確認します。
1. ログ バックアップを無効にし、関連するデータベースのファイル システムにバックアップ カタログを設定します。
1. これを行うには、**systemdb** >  **[構成]**  >  **[データベースの選択]**  >  **[Filter (Log)]\(フィルター (ログ)\)** の順にダブルクリックします。
1. <bpt id="p1">**</bpt>[enable_auto_log_backup]<ept id="p1">**</ept> を <bpt id="p2">**</bpt>[No]<ept id="p2">**</ept> に設定します。
1. <bpt id="p1">**</bpt>[log_backup_using_backint]<ept id="p1">**</ept> を <bpt id="p2">**</bpt>[False]<ept id="p2">**</ept> に設定します。
1. <bpt id="p1">**</bpt>[catalog_backup_using_backint]<ept id="p1">**</ept> を <bpt id="p2">**</bpt>[False]<ept id="p2">**</ept> に設定します。
1. データベースのオンデマンド完全バックアップを作成します。
1. 完全バックアップとカタログ バックアップが完了するまで待ちます。
1. 前の設定を Azure のものに戻します。
    * <bpt id="p1">**</bpt>[enable_auto_log_backup]<ept id="p1">**</ept> を <bpt id="p2">**</bpt>[Yes]<ept id="p2">**</ept> に設定します。
    * <bpt id="p1">**</bpt>[log_backup_using_backint]<ept id="p1">**</ept> を <bpt id="p2">**</bpt>[True]<ept id="p2">**</ept> に設定します。
    * <bpt id="p1">**</bpt>[catalog_backup_using_backint]<ept id="p1">**</ept> を <bpt id="p2">**</bpt>[True]<ept id="p2">**</ept> に設定します。

## <a name="next-steps"></a>次のステップ

* [Azure VM で稼働している SAP HANA データベースを復元する](./sap-hana-db-restore.md)方法を学習する
* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](./sap-hana-db-manage.md)方法を学習する
