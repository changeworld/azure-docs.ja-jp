---
title: Azure Backup を使用して Azure に SAP HANA データベースをバックアップする
description: この記事では、Azure Backup サービスを使用して SAP HANA データベースを Azure 仮想マシンにバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: c5df198d009f0d4a9f37a68d6b21386f06842722
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2020
ms.locfileid: "75753969"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Azure VM での SAP HANA データベースのバックアップ

SAP HANA データベースは、低い回復ポイントの目標値 (RPO) と長期リテンション期間を必要とする重要なワークロードです。 [Azure Backup](backup-overview.md) を使用して、Azure 仮想マシン (VM) 上で稼働している SAP HANA データベースをバックアップできます。

この記事では、Azure VM 上で稼働している SAP HANA データベースを Azure Backup Recovery Services コンテナーにバックアップする方法について説明します。

この記事では、次のことについて説明します。
> [!div class="checklist"]
>
> * コンテナーを作成して構成する
> * データベースを検出する
> * バックアップを構成する
> * オンデマンド バックアップ ジョブを実行する

## <a name="prerequisites"></a>前提条件

バックアップ用にデータベースを設定するには、「[前提条件](tutorial-backup-sap-hana-db.md#prerequisites)」と「[アクセス許可の設定](tutorial-backup-sap-hana-db.md#setting-up-permissions)」のセクションを参照してください。

### <a name="set-up-network-connectivity"></a>ネットワーク接続を設定する

すべての操作において、SAP HANA VM では、Azure パブリック IP アドレスへの接続を必要とします。 Azure パブリック IP アドレスへの接続がないと、VM の操作 (データベースの検出、バックアップの構成、バックアップのスケジュール、復旧ポイントの復元など) が失敗します。

次のオプションのいずれかを使用して接続を確立します。

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Azure データセンターの IP 範囲を許可する

このオプションは、ダウンロードされたファイルで [IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を許可します。 ネットワーク セキュリティ グループ (NSG) にアクセスするには、Set-AzureNetworkSecurityRule コマンドレットを使用します。 信頼できる受信者のリストにリージョン固有の IP のみが含まれている場合は、認証を有効にするために Azure Active Directory (Azure AD) サービス タグでその信頼できる受信者のリストを更新する必要があります。

#### <a name="allow-access-using-nsg-tags"></a>NSG タグを使用してアクセスを許可する

NSG を使用して接続を制限する場合は、AzureBackup サービス タグを使用して Azure Backup への発信アクセスを許可する必要があります。 さらに、Azure AD と Azure Storage に対して[規則](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)を使用することで、認証とデータ転送のための接続を許可する必要もあります。 これは、Azure portal または PowerShell から実行できます。

ポータルを使用して規則を作成するには、次のようにします。

  1. **[すべてのサービス]** で、 **[ネットワーク セキュリティ グループ]** に移動して、ネットワーク セキュリティ グループを選択します。
  2. **[設定]** で **[送信セキュリティ規則]** を選択します。
  3. **[追加]** を選択します。 [セキュリティ規則の設定](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings)の説明に従って、新しい規則を作成するために必要なすべての詳細を入力します。 オプション **[宛先]** が **[サービス タグ]** に、 **[宛先サービス タグ]** が **[AzureBackup]** に設定されていることを確認します。
  4. **[追加]** をクリックして、新しく作成した送信セキュリティ規則を保存します。

PowerShell を使用してルールを作成するには、次のようにします。

 1. Azure アカウントの資格情報を追加して各国のクラウドを更新する<br/>
      `Add-AzureRmAccount`<br/>

 2. NSG サブスクリプションを選択する<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. NSG を選択する<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Azure Backup サービス タグの発信許可規則を追加する<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. ストレージ サービス タグの発信許可規則を追加する<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. AzureActiveDirectory サービス タグの発信許可規則を追加する<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. NSG を保存する<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Azure Firewall タグを使用してアクセスを許可する**。 Azure Firewall を使用している場合は、AzureBackup [FQDN タグ](https://docs.microsoft.com/azure/firewall/fqdn-tags)を使用してアプリケーション規則を作成します。 これにより、Azure Backup への発信アクセスを許可します。

**トラフィックをルーティングするために HTTP プロキシ サーバーをデプロイする**。 Azure VM 上の SAP HANA データベースをバックアップする場合、VM 上のバックアップ拡張機能によって HTTPS API が使用され、管理コマンドが Azure Backup に送信されてデータが Azure Storage に送信されます。 また、バックアップ拡張機能では、認証に Azure AD を使用します。 HTTP プロキシ経由でこれらの 3 つのサービスのバックアップ拡張機能のトラフィックをルーティングします。 パブリック インターネットにアクセスできるように構成されたコンポーネントはバックアップ拡張機能のみです。

接続オプションには、次の長所と短所があります。

**オプション** | **長所** | **短所**
--- | --- | ---
IP 範囲を許可する | 追加のコストが発生しない | IP アドレス範囲が時間の経過と共に変化するため、管理が複雑である <br/><br/> Azure Storage だけでなく、Azure 全体へのアクセスを提供することになる
NSG サービス タグを使用する | 範囲の変更が自動的にマージされるため管理しやすい <br/><br/> 追加のコストが発生しない <br/><br/> | NSG でのみ使用可能 <br/><br/> サービス全体へのアクセスを提供する
Azure Firewall の FQDN タグを使用する | 必要な FQDN が自動的に管理されるため管理しやすい | Azure Firewall でのみ使用可能
HTTP プロキシを使用する | 許可するストレージ URL をプロキシで詳細に制御可能 <br/><br/> VM に対するインターネット アクセスを単一の場所で実現 <br/><br/> Azure の IP アドレスの変更の影響を受けない | プロキシ ソフトウェアで VM を実行するための追加のコストが発生する

## <a name="onboard-to-the-public-preview"></a>パブリック プレビューにオンボードする

以下のように、パブリック プレビューにオンボードします。

* ポータルで、[この記事に従って](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)、Recovery Services サービス プロバイダーにご利用のサブスクリプション ID を登録します。
* PowerShell の "Az" モジュールでは、このコマンドレットを実行します。 "登録済み" として完了するはずです。

    ```powershell
    Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
* PowerShell で "AzureRM" モジュールを使用している場合は、このコマンドレットを実行します。 "登録済み" として完了するはずです。

    ```powershell
    Register-AzureRmProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
    ```
    

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>データベースを検出する

1. コンテナーの **[作業の開始]** で、 **[バックアップ]** をクリックします。 **[ワークロードはどこで実行されていますか?]** で、 **[Azure VM の SAP HANA]** を選択します。
2. **[検出の開始]** をクリックします。 これで、コンテナー リージョン内の保護されていない Linux VM の検出が開始されます。

   * 検出後、保護されていない VM は、ポータルで名前およびリソース グループ別に一覧表示されます。
   * VM が予期したとおりに一覧表示されない場合は、それが既にコンテナーにバックアップされているかどうかを確認してください。
   * 複数の VM を同じ名前にすることはできますが、それらは異なるリソース グループに属しています。

3. **[仮想マシンの選択]** で、リンクをクリックして、データベースの検出のために SAP HANA VM へのアクセス許可を Azure Backup サービスに提供するスクリプトをダウンロードします。
4. バックアップする SAP HANA データベースをホストしている各 VM でスクリプトを実行します。
5. VM でスクリプトを実行した後、 **[仮想マシンの選択]** で、VM を選択します。 その後、 **[DB の検出]** をクリックします。
6. Azure Backup によって、VM 上のすべての SAP HANA データベースが検出されます。 検出中に、Azure Backup によって VM がコンテナーに登録され、VM に拡張機能がインストールされます。 エージェントはデータベースにインストールされません。

    ![SAP HANA データベースを検出する](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>バックアップの構成  

ここでバックアップを有効にします。

1. [手順 2] で、 **[バックアップの構成]** をクリックします。

    ![バックアップの構成](./media/backup-azure-sap-hana-database/configure-backup.png)
2. **[バックアップする項目の選択]** で、保護するデータベースをすべて選択し、 **[OK]** を選びます。

    ![バックアップする項目を選択する](./media/backup-azure-sap-hana-database/select-items.png)
3. **[バックアップ ポリシー]**  >  **[バックアップ ポリシーの選択]** で、以下の手順に従って、データベースの新しいバックアップ ポリシーを作成します。

    ![バックアップ ポリシーを選択する](./media/backup-azure-sap-hana-database/backup-policy.png)
4. ポリシーを作成した後、 **[バックアップ]** メニューの **[バックアップの有効化]** をクリックします。

    ![バックアップの有効化](./media/backup-azure-sap-hana-database/enable-backup.png)
5. ポータルの **[通知]** 領域で、バックアップ構成の進行状況を追跡します。

### <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保持される期間を定義します。

* ポリシーはコンテナー レベルで作成されます。
* 複数のコンテナーでは同じバックアップ ポリシーを使用できますが、各コンテナーにバックアップ ポリシーを適用する必要があります。

次のように、ポリシー設定を指定します。

1. **[ポリシー名]** に新しいポリシーの名前を入力します。

   ![ポリシー名を入力する](./media/backup-azure-sap-hana-database/policy-name.png)
2. **完全バックアップのポリシー**で **[バックアップ頻度]** を選択し、 **[毎日]** または **[毎週]** を選びます。
   * **日次**:バックアップ ジョブが開始される、時刻とタイム ゾーンを選択します。
       * 完全バックアップを実行する必要があります。 このオプションをオフにすることはできません。
       * **[完全バックアップ]** をクリックし、ポリシーを表示します。
       * 日次の完全バックアップを選択する場合は、差分バックアップを作成できません。
   * **毎週**:バックアップ ジョブが実行される、曜日、時刻、およびタイム ゾーンを選択します。

   ![バックアップの頻度を選択する](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. **[リテンション期間]** で、完全バックアップのリテンション期間の設定を構成します。
    * 既定では、すべてのオプションが選択されています。 使用しないリテンション期間の制限をすべてクリアして、使用するものを設定します。
    * あらゆる種類のバックアップ (完全、差分、ログ) の最小保持期間は 7 日間です。
    * 復旧ポイントは、そのリテンション期間の範囲に基づいて、リテンション期間に対してタグ付けされます。 たとえば、日次での完全バックアップを選択した場合、日ごとにトリガーされる完全バックアップは 1 回だけです。
    * 特定の曜日のバックアップがタグ付けされ、週次でのリテンション期間と設定に基づいて保持されます。
    * 月次および年次のリテンション期間の範囲でも、同様の動作になります。

4. **[完全バックアップのポリシー]** メニューで、 **[OK]** をクリックして設定を受け入れます。
5. **[差分バックアップ]** を選択して、差分ポリシーを追加します。
6. **差分バックアップのポリシー**で、 **[有効]** を選択して頻度とリテンション期間の制御を開きます。
    * 最多で、1 日に 1 回の差分バックアップをトリガーできます。
    * 差分バックアップは、最大 180 日間保持できます。 より長いリテンション期間が必要な場合は、完全バックアップを使用する必要があります。

    ![差分バックアップ ポリシー](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > 増分バックアップは現在、サポートされていません。

7. **[OK]** をクリックしてポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。
8. **[ログ バックアップ]** を選択し、トランザクション ログ バックアップ ポリシーを追加します。
    * **[ログ バックアップ]** で、 **[有効化]** を選択します。  SAP HANA ではすべてのログ バックアップが管理されるため、これを無効にすることはできません。
    * 頻度とリテンション期間の制御を設定します。

    > [!NOTE]
    > ログ バックアップでは、完全バックアップが正常に完了した後にのみ、フローが開始されます。

9. **[OK]** をクリックしてポリシーを保存し、 **[バックアップ ポリシー]** のメイン メニューに戻ります。
10. バックアップ ポリシーの定義が完了した後、 **[OK]** をクリックします。

> [!NOTE]
> 各ログ バックアップは、復旧チェーンを形成するために、以前の完全バックアップにチェーンされています。 この完全バックアップは、前回のログ バックアップのリテンション期間が終了するまで保持されます。 これは、完全バックアップのリテンション期間を追加して、すべてのログが確実に復旧されるようにすることを意味します。 ユーザーが、週単位の完全バックアップ、日単位の差分、2 時間ごとのログを実行しているとしましょう。 これらのすべてが 30 日間保持されます。 ただし、週単位の完全バックアップは、次の完全バックアップが利用可能になった後、すなわち 30 + 7 日後に、クリーンアップまたは削除することができます。 たとえば、週単位の完全バックアップが 11 月 16 日に行われたとします。 保持ポリシーに従って、12 月 16 日まで保持されます。 この完全バックアップに対する前回のログ バックアップは、11 月 22 日に予定されている次の完全バックアップの前に行われます。 このログが 12 月 22 日までに利用可能になるまでは、11 月 16 日の完全バックアップは削除されません。 そのため、11 月 16 日の完全バックアップは、12 月 22 日までは保持されます。

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

バックアップは、ポリシー スケジュールに従って実行されます。 次のように、バックアップ オンデマンを実行できます。

1. コンテナー メニューで **[バックアップ アイテム]** をクリックします。
2. **[バックアップ項目]** で、SAP HANA データベースを実行している VM を選択してから、 **[今すぐバックアップ]** をクリックします。
3. **[今すぐバックアップ]** で、カレンダー コントロールを使用して復旧ポイントを保持する最終日を選択します。 次に、 **[OK]** をクリックします
4. ポータルの通知を監視します。 コンテナー ダッシュボードの **[バックアップ ジョブ]**  >  **[進行中]** でジョブの進行状況を監視できます。 データベースのサイズによっては、最初のバックアップの作成に時間がかかる場合があります。

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Azure Backup が有効になっているデータベースで SAP HANA Studio のバックアップを実行する

Azure Backup でバックアップされているデータベースの (HANA Studio を使用する) ローカル バックアップを作成する場合は、次の操作を行います。

1. データベースの完全バックアップまたはログ バックアップがすべて完了するまで待ちます。 SAP HANA Studio/Cockpit で状態を確認します。
2. ログ バックアップを無効にし、関連するデータベースのファイル システムにバックアップ カタログを設定します。
3. これを行うには、**systemdb** >  **[構成]**  >  **[データベースの選択]**  >  **[Filter (Log)]\(フィルター (ログ)\)** の順にダブルクリックします。
4. **[enable_auto_log_backup]** を **[No]** に設定します。
5. **[log_backup_using_backint]** を **[False]** に設定します。
6. データベースのオンデマンド完全バックアップを作成します。
7. 完全バックアップとカタログ バックアップが完了するまで待ちます。
8. 前の設定を Azure のものに戻します。
    * **[enable_auto_log_backup]** を **[Yes]** に設定します。
    * **[log_backup_using_backint]** を **[True]** に設定します。

## <a name="next-steps"></a>次のステップ

* [Azure VM で稼働している SAP HANA データベースを復元する](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)方法を学習する
* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)方法を学習します
