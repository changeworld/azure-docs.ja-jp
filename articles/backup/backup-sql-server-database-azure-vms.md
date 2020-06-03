---
title: Azure VM での SQL Server データベースのバックアップ
description: この記事では、Azure Backup を使用して Azure 仮想マシン上の SQL Server データベースをバックアップする方法について説明します。
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 3fd94dc6332d96f875c164dfeadff3a8ab2cad4e
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715598"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Azure VM での SQL Server データベースのバックアップ

SQL Server データベースは、低い回復ポイントの目標値 (RPO) と長期のリテンション期間を必要とする重要なワークロードです。 [Azure Backup](backup-overview.md) を使用して、Azure 仮想マシン (VM) 上で稼働している SQL Server データベースをバックアップできます。

この記事では、Azure VM 上で稼働している SQL Server データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について説明します。

この記事では、次の方法について学習します。

> [!div class="checklist"]
>
> * コンテナーの作成と構成。
> * データベースの検出とバックアップの設定。
> * データベースに対する自動保護の設定。

>[!NOTE]
>**Azure VM での SQL Server の論理的な削除と Azure VM ワークロードでの SAP HANA の論理的な削除**が、プレビューで利用できるようになりました。<br>
>プレビュー用にサインアップするには、AskAzureBackupTeam@microsoft.com 宛てにご連絡ください

## <a name="prerequisites"></a>前提条件

SQL Server データベースをバックアップする前に、次の基準を確認してください。

1. SQL Server インスタンスをホストする VM として、同じリージョンおよびサブスクリプションの [Recovery Services コンテナー](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault)を特定または作成する。
2. VM が[ネットワーク接続](backup-sql-server-database-azure-vms.md#establish-network-connectivity)を備えていることを確認する。
3. SQL Server データベースが、[Azure Backup のためのデータベースの命名に関するガイドライン](#database-naming-guidelines-for-azure-backup)に従っていることを確認する。
4. データベースに対して有効になっているバックアップ ソリューションが他にないことをチェックする。 データベースをバックアップする前に、他のすべての SQL Server バックアップを無効にします。

> [!NOTE]
> Azure VM に対してだけでなく、VM 上で稼働している SQL Server データベースに対しても、競合を発生させることなく Azure Backup を有効にすることができます。

### <a name="establish-network-connectivity"></a>ネットワーク接続を確立する

すべての操作において、SQL Server VM には Azure Backup サービス、Azure Storage、および Azure Active Directory への接続が必要です。 これは、プライベート エンドポイントを使用するか、必要なパブリック IP アドレスまたは FQDN へのアクセスを許可することによって実現できます。 必要な Azure サービスへの適切な接続を許可しないと、データベースの検出、バックアップの構成、バックアップの実行、データの復元などの操作の失敗につながる可能性があります。

次の表に、接続の確立に使用できるさまざまな選択肢を示します。

| **オプション**                        | **長所**                                               | **短所**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| プライベート エンドポイント                 | 仮想ネットワーク内のプライベート IP 経由でのバックアップを可能にする  <br><br>   ネットワークとコンテナーの側で詳細な制御を提供する | 標準のプライベート エンドポイント [コスト](https://azure.microsoft.com/pricing/details/private-link/)が発生する |
| NSG サービス タグ                  | 範囲の変更が自動的にマージされるため管理しやすい   <br><br>   追加のコストが発生しない | NSG でのみ使用可能  <br><br>    サービス全体へのアクセスを提供する |
| Azure Firewall の FQDN タグ          | 必要な FQDN が自動的に管理されるため管理しやすい | Azure Firewall でのみ使用可能                         |
| サービスの FQDN/IP へのアクセスを許可する | 追加のコストが発生しない   <br><br>  すべてのネットワーク セキュリティ アプライアンスとファイアウォールで動作する | 広範な IP または FQDN のセットへのアクセスが必要になる場合がある   |
| HTTP プロキシを使用する                 | VM に対するインターネット アクセスを単一の場所で実現                       | プロキシ ソフトウェアで VM を実行するための追加のコストが発生する         |

これらのオプションを使用する方法の詳細については、以下を参照してください。

#### <a name="private-endpoints"></a>プライベート エンドポイント

プライベート エンドポイントを使用すると、仮想ネットワーク内のサーバーから Recovery Services コンテナーに安全に接続できます。 プライベート エンドポイントでは、お使いのコンテナーの VNET アドレス空間からの IP アドレスが使用されます。 仮想ネットワーク内のリソースとコンテナー間のネットワーク トラフィックは、仮想ネットワークと Microsoft のバックボーン ネットワーク上のプライベート リンクを経由します。 これにより、パブリック インターネットへの露出が排除されます。 [こちら](https://docs.microsoft.com/azure/backup/private-endpoints)から、Azure Backup のプライベート エンドポイントの詳細を参照してください。

#### <a name="nsg-tags"></a>NSG タグ

ネットワーク セキュリティ グループ (NSG) を使用する場合は、*AzureBackup* サービス タグを使用して、Azure Backup への発信アクセスを許可します。 Azure Backup タグに加えて、*Azure AD* および *Azure Storage* に対して同様の [NSG 規則](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)を作成することによって、認証とデータ転送のための接続を許可する必要もあります。  次の手順では、Azure Backup タグの規則を作成するプロセスについて説明します。

1. **[すべてのサービス]** で、 **[ネットワーク セキュリティ グループ]** に移動して、ネットワーク セキュリティ グループを選択します。

1. **[設定]** で **[送信セキュリティ規則]** を選択します。

1. **[追加]** を選択します。 [セキュリティ規則の設定](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)の説明に従って、新しい規則を作成するために必要なすべての詳細を入力します。 オプション **[宛先]** が *[サービス タグ]* に、 **[宛先サービス タグ]** が *[AzureBackup]* に設定されていることを確認します。

1. **[追加]** をクリックして、新しく作成した送信セキュリティ規則を保存します。

Azure Storage と Azure AD に対する NSG 送信セキュリティ規則も、同様に作成できます。

#### <a name="azure-firewall-tags"></a>Azure Firewall タグ

Azure Firewall を使用している場合は、*AzureBackup* [Azure Firewall FQDN タグ](https://docs.microsoft.com/azure/firewall/fqdn-tags)を使用してアプリケーション規則を作成します。 これにより、Azure Backup へのすべての発信アクセスが許可されます。

#### <a name="allow-access-to-service-ip-ranges"></a>サービスの IP 範囲へのアクセスを許可する

サービスの IP へのアクセスを許可することを選択した場合は、[こちら](https://www.microsoft.com/download/confirmation.aspx?id=56519)から利用可能な IP 範囲の JSON ファイルを参照してください。 Azure Backup、Azure Storage、および Azure Active Directory に対応する IP へのアクセスを許可する必要があります。

#### <a name="allow-access-to-service-fqdns"></a>サービスの FQDN へのアクセスを許可する

次の FQDN を使用することで、サーバーから必要なサービスへのアクセスを許可することもできます。

| サービス    | アクセスするドメイン名                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | [この記事](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)に従って、セクション 56 および 59 の FQDN へのアクセスを許可します |

#### <a name="use-an-http-proxy-server-to-route-traffic"></a>トラフィックをルーティングするために HTTP プロキシ サーバーを使用する

Azure VM 上の SQL Server データベースをバックアップする場合、VM 上のバックアップ拡張機能によって HTTPS API が使用され、管理コマンドが Azure Backup に送信されてデータが Azure Storage に送信されます。 また、バックアップ拡張機能では、認証に Azure AD を使用します。 HTTP プロキシ経由でこれらの 3 つのサービスのバックアップ拡張機能のトラフィックをルーティングします。 必要なサービスへのアクセスを許可するには、上記で説明した IP と FQDN の一覧を使用します。 認証済みプロキシ サーバーはサポートされません。

### <a name="database-naming-guidelines-for-azure-backup"></a>Azure Backup のためのデータベースの命名に関するガイドライン

データベースの名前に次の要素を使用しないでください。

* 末尾および先頭のスペース
* 末尾の感嘆符 (!)
* 終わり角かっこ (])
* セミコロン ';'
* スラッシュ '/'

サポートされていない文字のエイリアス処理は用意されていますが、これらは使用しないことをお勧めします。 詳細については、「 [Table サービス データ モデルについて](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)」を参照してください。

>[!NOTE]
>名前に "+" や "&" などの特殊文字が含まれるデータベースに対する**保護の構成**操作はサポートされていません。 データベース名を変更するか、これらのデータベースを適切に保護できる**自動保護**を有効にできます。

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>SQL Server データベースを検出する

VM 上で稼働しているデータベースを検出する方法:

1. [Azure portal](https://portal.azure.com) で、データベースをバックアップするために使用する Recovery Services コンテナーを開きます。

2. **[Recovery Services コンテナー]** ダッシュボードで **[バックアップ]** を選択します。

   ![[バックアップ] を選択して [バックアップの目標] メニューを開く](./media/backup-azure-sql-database/open-backup-menu.png)

3. **[バックアップの目標]** で、 **[ワークロードはどこで実行されていますか?]** を **[Azure]** に設定します。

4. **[何をバックアップしますか?]** で、 **[Azure VM 内の SQL Server]** を選択します。

    ![バックアップ対象として [Azure VM 内の SQL Server] を選択する](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. **[バックアップの目標]**  >  **[VM 内のデータベースを検出]** で、 **[検出の開始]** を選択して、サブスクリプション内にある保護されていない VM を検索します。 サブスクリプション内にある保護されていない VM の数によっては、この検索に少し時間がかかる場合があります。

   * 保護されていない VM は検出後、名前およびリソース グループ別に一覧に表示されます。
   * VM が予想どおりに一覧表示されない場合、それが既にコンテナーにバックアップされていないかを確認してください。
   * 名前の同じ複数の VM が、異なるリソース グループに含まれる可能性があります。

     ![VM 内にある DB の検索中はバックアップが保留される](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. VM の一覧で、SQL Server データベースを稼働している VM を選択し、 **[Discover DBs]\(DB の検出\)** を選択します。

7. **[通知]** でデータベース検出を追跡します。 この操作に必要な時間は、VM のデータベースの数によって異なります。 選択したデータベースが検出されたら、成功のメッセージが表示されます。

    ![デプロイの成功メッセージ](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. Azure Backup によって、VM 上のすべての SQL Server データベースが検出されます。 検出中、バックグラウンドで以下の要素が発生します。

    * Azure Backup によって、ワークロード バックアップ用のコンテナーに VM が登録されます。 登録された VM 上のすべてのデータベースは、このコンテナーのみに対してバックアップできます。
    * Azure Backup によって、AzureBackupWindowsWorkload 拡張機能が VM にインストールされます。 エージェントは SQL データベースにインストールされません。
    * Azure Backup によって、サービス アカウント NT Service\AzureWLBackupPluginSvc が VM 上に作成されます。
      * すべてのバックアップおよび復元操作に、サービス アカウントを使用します。
      * NT Service\AzureWLBackupPluginSvc には、SQL sysadmin 権限が必要です。 Marketplace で作成されたすべての SQL Server VM には、SqlIaaSExtension が元からインストールされています。 AzureBackupWindowsWorkload 拡張機能は SQLIaaSExtension を使用して自動的に必要な権限を取得します。
    * VM を Marketplace から作成しなかった場合や、SQL 2008 および 2008 R2 を実行している場合、VM に SqlIaaSExtension がインストールされないことあり、検出操作はエラー メッセージ UserErrorSQLNoSysAdminMembership を示して失敗します。 この問題を修正するには、「[VM のアクセス許可を設定する](backup-azure-sql-database.md#set-vm-permissions)」の手順に従います。

        ![VM とデータベースを選択する](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>バックアップの構成  

1. **[バックアップの目標]**  >  **[手順 2: バックアップの構成]** で、 **[バックアップの構成]** を選択します。

   ![[バックアップの構成] を選択する](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. **[バックアップする項目の選択]** には、登録されている可用性グループおよびスタンドアロン SQL Server インスタンスがすべて表示されます。 行の左側にある矢印を選択して、そのインスタンスまたは Always On 可用性グループ内の保護されていないデータベースの全一覧を展開します。  

    ![スタンドアロン データベースであるすべての SQL Server インスタンスの表示](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. 保護したいデータベースをすべて選択してから、 **[OK]** を選択します。

   ![データベースの保護](./media/backup-azure-sql-database/select-database-to-protect.png)

   バックアップの負荷を最適化するために、Azure Backup では、1 つのバックアップ ジョブにおけるデータベースの最大数が 50 個に設定されています。

     * 50 個を超えるデータベースを保護するには、複数のバックアップを構成します。
     * インスタンス全体または AlwaysOn 可用性グループを[有効にする](#enable-auto-protection)には、 **[AUTOPROTECT]\(自動保護\)** ドロップダウン リストで **[オン]** を選択し、 **[OK]** を選択します。

    > [!NOTE]
    > [自動保護](#enable-auto-protection)機能では、既存のすべてのデータベースの保護が一度に有効になるだけでなく、そのインスタンスまたは可用性グループに追加されたすべての新しいデータベースも自動的に保護されます。  

4. **[OK]** を選択して **[バックアップ ポリシー]** を開きます。

    ![Always On 可用性グループで自動保護を有効にする](./media/backup-azure-sql-database/enable-auto-protection.png)

5. **[バックアップ ポリシー]** で、ポリシーを選択してから **[OK]** を選択します。

   * 既定のポリシーを HourlyLogBackup として選択します。
   * SQL 用に以前に作成された既存のバックアップ ポリシーを選択する。
   * RPO とリテンション期間の範囲に基づいて新しいポリシーを定義する。

     ![[バックアップ ポリシー] を選択する](./media/backup-azure-sql-database/select-backup-policy.png)

6. **[バックアップ]** で、 **[バックアップの有効化]** を選択します。

    ![選択したバックアップ ポリシーを有効にする](./media/backup-azure-sql-database/enable-backup-button.png)

7. ポータルの **[通知]** 領域で構成の進行状況を追跡します。

    ![[通知] 領域](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保持される期間を定義します。

* ポリシーはコンテナー レベルで作成されます。
* 複数のコンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。
* バックアップ ポリシーの作成時には、日次での完全バックアップが既定値になっています。
* 差分バックアップを追加することは可能ですが、週次で実行するように完全バックアップを構成する場合だけです。
* [さまざまな種類のバックアップ ポリシー](backup-architecture.md#sql-server-backup-types)について学習してください。

バックアップ ポリシーを作成するには:

1. コンテナーで、 **[バックアップ ポリシー]**  >  **[追加]** の順に選択します。
2. **[追加]** で、 **[Azure VM 内の SQL Server]** を選択してポリシーの種類を定義します。

   ![新しいバックアップ ポリシーのポリシーの種類を選択する](./media/backup-azure-sql-database/policy-type-details.png)

3. **[ポリシー名]** に新しいポリシーの名前を入力します。
4. **[Full Backup policy]\(完全バックアップのポリシー\)** で、 **[バックアップ頻度]** を選択します。 **[毎日]** または **[毎週]** を選択します。

   * **[毎日]** を選択する場合は、バックアップ ジョブが開始されるときに、時刻とタイム ゾーンを選択します。
   * **[毎週]** を選択する場合は、バックアップ ジョブが開始されるときに、曜日、時刻、およびタイム ゾーンを選択します。
   * **[完全バックアップ]** オプションをオフにすることはできないため、完全バックアップを実行します。
   * **[完全バックアップ]** を選択し、ポリシーを表示します。
   * 日次の完全バックアップを選択する場合は、差分バックアップを作成できません。

     ![バックアップ ポリシーの新しいフィールド](./media/backup-azure-sql-database/full-backup-policy.png)  

5. **[リテンション期間]** では、すべてのオプションが既定で選択されています。 使用しないリテンション期間の制限を解除してから、使用する間隔を設定します。

    * バックアップのすべての種類 (完全、差分、ログ) の最小リテンション期間は 7 日間です。
    * 復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。
    * 特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間の範囲と週次でのリテンション期間の設定に基づいて保持されます。
    * 月次および年次のリテンション期間の範囲でも、同様の動作になります。

       ![[リテンション期間] の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)

6. **完全バックアップのポリシー** メニューで、 **[OK]** を選択して設定を確定します。
7. 差分バックアップ ポリシーを追加するには、 **[差分バックアップ]** を選択します。

   ![リテンション期間の間隔の設定](./media/backup-azure-sql-database/retention-range-interval.png)
   ![差分バックアップのポリシー メニューを開く](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. **差分バックアップのポリシー**で、 **[有効]** を選択して頻度とリテンション期間の制御を開きます。

    * 差分バックアップは 1 日に 1 回のみトリガーできます。
    * 差分バックアップは、最大 180 日間保持できます。 リテンション期間をさらに長くするには、完全バックアップを使用します。

9. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。

10. トランザクション ログ バックアップ ポリシーを追加するには、 **[ログ バックアップ]** を選択します。
11. **[ログ バックアップ]** で、 **[有効]** を選択して、頻度とリテンション期間の制御を設定します。 ログ バックアップは、15 分ごとの頻度で実行でき、最大 35 日間保持することが可能です。
12. **[OK]** を選択してポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。

    ![ログ バックアップ ポリシーを編集する](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. **[バックアップ ポリシー]** メニューで、 **[SQL バックアップの圧縮]** を有効にするかどうかを選択します。 既定では、このオプションは無効になっています。 有効にすると、圧縮されたバックアップストリームが SQL Server によって VDI に送信されます。  Azure Backup は、このコントロールの値に応じて、COMPRESSION / NO_COMPRESSION 句を使用して、インスタンス レベルの既定値をオーバーライドすることに注意してください。

14. バックアップ ポリシーに対する編集が完了したら、 **[OK]** を選択します。

> [!NOTE]
> 各ログ バックアップは、復旧チェーンを形成するために、以前の完全バックアップにチェーンされています。 この完全バックアップは、前回のログ バックアップのリテンション期間が終了するまで保持されます。 これは、完全バックアップのリテンション期間を追加して、すべてのログが確実に復旧されるようにすることを意味します。 ユーザーが、週単位の完全バックアップ、日単位の差分、2 時間ごとのログを実行しているとしましょう。 これらのすべてが 30 日間保持されます。 ただし、週単位の完全バックアップは、次の完全バックアップが利用可能になった後、すなわち 30 + 7 日後に、クリーンアップまたは削除することができます。 たとえば、週単位の完全バックアップが 11 月 16 日に行われたとします。 保持ポリシーに従って、12 月 16 日まで保持されます。 この完全バックアップに対する前回のログ バックアップは、11 月 22 日に予定されている次の完全バックアップの前に行われます。 このログが 12 月 22 日までに利用可能になるまでは、11 月 16 日の完全バックアップは削除されません。 そのため、11 月 16 日の完全バックアップは、12 月 22 日までは保持されます。

## <a name="enable-auto-protection"></a>自動保護を有効にする  

自動保護を有効にして、すべての既存および将来のデータベースを、スタンドアロンの SQL Server インスタンスに、または Always On 可用性グループに自動的にバックアップすることができます。

* 自動保護のために一度に選択できるデータベースの数に制限はありません。
* 自動保護を有効化するときに、インスタンス内のデータベースを選んで保護したり保護から除外したりすることはできません。
* 保護されているデータベースが既にインスタンスに含まれる場合は、自動保護を有効にした後でも、それぞれのポリシーに従って保護されたままです。 後から追加された、保護されていないすべてのデータベースには、自動保護の有効化の時点で定義し、 **[バックアップの構成]** で一覧に表示される 1 つのポリシーのみが適用されます。 ただし、自動保護データベースに関連付けたポリシーは後から変更できます。  

自動保護を有効にするには:

  1. **[Items to backup]\(バックアップする項目\)** で、自動保護を有効にしたいインスタンスを選択します。
  2. **[AUTOPROTECT]\(自動保護\)** の下のドロップダウン リストを選択し、 **[ON]\(オン\)** を選択してから **[OK]** を選択します。

      ![可用性グループで自動保護を有効にする](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. バックアップはすべてのデータベースに対して一括で構成され、 **[バックアップ ジョブ]** で追跡できます。

自動保護を無効にする必要がある場合、 **[バックアップの構成]** のインスタンス名を選択してから、インスタンスについて **[自動保護を無効にする]** を選択します。 すべてのデータベースは引き続きバックアップされますが、今後のデータベースは自動的に保護されなくなります。

![そのインスタンスの自動保護を無効にする](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>次のステップ

具体的には、次の方法を学習します。

* [バックアップした SQL Server データベースの復元](restore-sql-database-azure-vm.md)
* [バックアップした SQL Server データベースの管理](manage-monitor-sql-database-backup.md)
