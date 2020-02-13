---
title: Azure Data Box を使用したオフライン バックアップ
description: Azure Data Box を使用して、MARS エージェントから Azure Recovery Services コンテナーに、大規模な初期バックアップ データをオフラインでシード処理する方法について説明します。
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026495"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Azure Data Box を使用した Azure Backup のオフライン バックアップ

[Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) サービスを使用して、大規模な初期 MARS バックアップを (ネットワークを使用せずに) オフラインで Azure Recovery Services コンテナーにシード処理することができます。  これにより、待機時間の長いネットワークを介して大量のバックアップ データをオンラインで移動する場合に消費される時間とネットワーク帯域幅の両方を節約することができます。 この拡張機能は、現在プレビュー段階です。 Azure Data Box ベースのオフライン バックアップには、[Azure Import/Export サービスベースのオフライン バックアップ](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)に対して 2 つの明確な利点があります。

1. Azure と互換性のある独自のディスクとコネクタを用意する必要がありません。 Azure Data Box サービスは、選択された [Data Box SKU](https://azure.microsoft.com/services/databox/data/) に関連するディスクを発送します。

2. Azure Backup (MARS エージェント) は、Azure Data Box のサポートされている SKU に直接バックアップ データを書き込むことができます。 これにより、初期バックアップ データのステージング場所をプロビジョニングする必要がなくなり、ユーティリティーでそのデータをフォーマットしてディスクにコピーする必要がなくなります。

## <a name="azure-data-box-with-mars-agent"></a>Azure Data Box と MARS エージェント

この記事では、Azure Data Box を使用して、MARS エージェントから Azure Recovery Services コンテナーに、大規模な初期バックアップ データをオフラインでシード処理する方法について説明します。 この記事は、次の部分に分かれています。

* サポートされているプラットフォーム
* サポートされている Data Box SKU
* 前提条件
* MARS エージェントをセットアップする
* Azure Data Box をセットアップする
* Azure Data Box へのバックアップ データの転送
* バックアップ後の手順

## <a name="supported-platforms"></a>サポートされているプラットフォーム

Azure Data Box を使用して MARS エージェントからデータをシード処理するプロセスは、次の Windows SKU でサポートされています。

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **ワークステーション**                        |                                                              |
| Windows 10 64 ビット                     | Enterprise、Pro、Home                                       |
| Windows 8.1 64 ビット                    | Enterprise、Pro                                             |
| Windows 8 64 ビット                      | Enterprise、Pro                                             |
| Windows 7 64 ビット                      | Ultimate、Enterprise、Professional、Home Premium、Home Basic、Starter |
| **[サーバー]**                             |                                                              |
| Windows Server 2019 64 ビット            | Standard、Datacenter、Essentials                            |
| Windows Server 2016 64 ビット            | Standard、Datacenter、Essentials                            |
| Windows Server 2012 R2 64 ビット         | Standard、Datacenter、Foundation                            |
| Windows Server 2012 64 ビット            | Datacenter、Foundation、Standard                            |
| Windows Storage Server 2016 64 ビット    | Standard、Workgroup                                         |
| Windows Storage Server 2012 R2 64 ビット | Standard、Workgroup、Essential                              |
| Windows Storage Server 2012 64 ビット    | Standard、Workgroup                                         |
| Windows Server 2008 R2 SP1 64 ビット     | Standard、Enterprise、Datacenter、Foundation                |
| Windows Server 2008 SP2 64 ビット        | Standard、Enterprise、Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>バックアップ データのサイズとサポートされている Data Box SKU

| サーバー 1 台あたりのバックアップ データ サイズ (MARS による圧縮後)* | サポートされている Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7.2 TB                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB および <= 80 TB**                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* 一般的な圧縮率は 10 - 20% の間になります <br>
** 1 台の MARS サーバーの初期バックアップ データが 80 TB を超えると予想される場合は、[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) にご連絡ください。

>[!IMPORTANT]
>1 台のサーバーの初期バックアップ データは、1 つの Azure Data Box または Azure Data Box Disk 内に含まれている必要があり、同じまたは異なる SKU の複数のデバイス間で共有することはできません。 ただし、Azure Data Box デバイスには、複数のサーバーからの初期バックアップを含めることができます。

## <a name="pre-requisites"></a>前提条件

### <a name="azure-subscription-and-required-permissions"></a>Azure サブスクリプションと必要なアクセス許可

* このプロセスには、Azure サブスクリプションが必要です。
* このプロセスでは、オフライン バックアップ ポリシーを実行するよう指定されたユーザーが Azure サブスクリプションの "所有者" である必要があります。
* Data Box ジョブと、(データをシード処理する必要がある) Recovery Services コンテナーは、同じサブスクリプション内に存在する必要があります。
* Azure Data Box ジョブと Recovery Services コンテナーに関連するターゲット ストレージ アカウントは、同じリージョンに配置することをお勧めします。 ただし、これは必須ではありません。

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 を入手する

**これは、このプロセスにとって最も重要な前提条件です**。 Azure PowerShell (バージョン 3.7.0) をインストールする前に、次の確認を実行します**

#### <a name="step-1-check-powershell-version"></a>手順 1:PowerShell のバージョンを確認する

* Windows PowerShell を開き、次のコマンドを実行します。

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* 出力に **3.7.0** よりも高いバージョンが表示された場合は、次の手順 2 を実行します。 それ以外の場合は、手順 3 に進みます。

#### <a name="step-2-uninstall-the-powershell-version"></a>手順 2:PowerShell のバージョンをアンインストールする

次の操作を行って、PowerShell の現在のバージョンをアンインストールします。

* PowerShell で次のコマンドを実行して、依存モジュールを削除します。

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* 次のコマンドを実行して、すべての依存モジュールが正常に削除されたことを確認します。

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>手順 3:PowerShell バージョン 3.7.0 をインストールする

AzureRM モジュールが存在しないことを確認したら、次のいずれかの方法を使用してバージョン 3.7.0 をインストールします。

* GitHub から、[リンク](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

OR

* PowerShell ウィンドウで次のコマンドを実行します。

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell は、msi ファイルを使用してインストールされている可能性もあります。 それを削除するには、コントロール パネルの [プログラムのアンインストール] オプションを使用してアンインストールします。

### <a name="order-and-receive-the-data-box-device"></a>Data Box デバイスを注文して受け取る

MARS と Azure Data Box を使用するオフライン バックアップ プロセスでは、MARS エージェントを使用したオフライン バックアップを開始する前に、必要な Data Box デバイスが "配送済み" 状態である必要があります。 要件に最も適した SKU を注文するには、「[バックアップ データ サイズとサポートされる Data Box SKU](#backup-data-size-and-supported-data-box-skus)」を参照してください。 Data Box デバイスを注文して受け取るには、[こちらの記事](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered)の手順に従ってください。

>[!IMPORTANT]
>アカウントの種類として [BlobStorage] を選択しないでください。 MARS エージェントには、ページ BLOB をサポートするアカウントが必要ですが、これは、[BlobStorage] が選択されているとサポートされません。 Azure Data Box ジョブ用のターゲット ストレージ アカウントを作成する場合は、アカウントの種類として [*Storage V2* (*汎用 v2*)] を選択することを強くお勧めします。

![インスタンスの詳細でアカウントの種類を選択する](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>MARS エージェントをインストールしてセットアップする

* 以前にインストールした MARS エージェントをすべてアンインストールします。

* [こちら](https://aka.ms/azurebackup_agent)から、最新の MARS エージェントをダウンロードします。

* *MARSAgentInstaller.exe* を実行し、[エージェントをインストールしてバックアップの格納先の Recovery Services コンテナーに登録する](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent)手順 ***のみ**を実行します。

  >[!NOTE]
  > Recovery Services コンテナーは、Azure Data Box ジョブと同じサブスクリプションに含まれている必要があります。

* エージェントが Recovery Services コンテナーに登録されたら、次のセクションの手順に従います。

## <a name="setup-azure-data-box-devices"></a>Azure Data Box デバイスをセットアップする

注文した Azure Data Box SKU に応じて、以下の該当するセクションで説明されている手順を実行して、MARS エージェントが初期バックアップ データを特定して転送するための Data Box デバイスを設定して準備します。

### <a name="setup-azure-data-box-disk"></a>Azure Data Box Disk をセットアップする

1 つ以上の Azure Data Box Disk (それぞれ最大 8 TB) を注文した場合は、ここに記載されている手順に従って、[Data Box Disk を開梱、接続、ロック解除](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)してください。

>[!NOTE]
>MARS エージェントがあるサーバーに USB ポートがない可能性があります。 そのような状況では、Azure Data Box Disk を別のサーバー/クライアントに接続し、デバイスのルートをネットワーク共有として公開することができます。

### <a name="setup-azure-data-box"></a>Azure Data Box をセットアップする

Azure Data Box (最大 100 TB) を注文した場合は、こちらに記載されている手順に従って [Data Box をセットアップ](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)してください。

#### <a name="mount-your-azure-data-box-as-local-system"></a>Azure Data Box をローカル システムとしてマウントする

MARS エージェントはローカル システム コンテキストで動作するため、Azure Data Box が接続されているマウント パスに提供されるのと同じレベルの特権が必要です。 次の手順に従って、NFS プロトコルを使用して Data Box デバイスをローカル システムとしてマウントできることを確認します。

* (MARS エージェントがインストールされている) Windows Server で、NFS クライアント機能を有効にします。<br>
  代替ソースを指定します: *WIM:D:\Sources\Install.wim:4*

* <https://download.sysinternals.com/files/PSTools.zip> から MARS エージェントがインストールされているサーバーに PSExec をダウンロードします。

* 管理者特権でのコマンド プロンプトを開き、PSExec.exe を含むディレクトリを現在のディレクトリとして次のコマンドを実行します。

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* 上記のコマンドの結果として開かれるコマンド ウィンドウは、ローカル システム コンテキストにあります。 このコマンド ウィンドウを使用して、Azure ページ BLOB 共有をネットワーク ドライブとして Windows Server にマウントする手順を実行します。

* [こちら](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box)の手順に従って、MARS エージェントがあるサーバーを NFS 経由で Data Box デバイスに接続し、ローカル システムのコマンド プロンプトで次のコマンドを実行して、Azure ページ BLOB 共有をマウントします。

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* マウントしたら、お使いのサーバーから X: にアクセスできるか確認してください。 「はい」の場合は、この記事の次のセクションに進んでください。

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Azure Data Box デバイスに初期バックアップ データを転送する

* お使いのサーバーで **Microsoft Azure Backup** アプリケーションを開きます。

* **[アクション]** ペインで、 **[バックアップのスケジュール]** をクリックします。

    ![[バックアップのスケジュール] をクリックする](./media/offline-backup-azure-data-box/schedule-backup.png)

* **バックアップのスケジュール ウィザード**の手順に従います。

* 項目の合計サイズが、注文して受け取った [Azure Data Box SKU でサポートされているサイズ制限](#backup-data-size-and-supported-data-box-skus)内になるように、**項目を追加**します。

    ![項目をバックアップに追加する](./media/offline-backup-azure-data-box/add-items.png)

* [ファイルとフォルダー] および [システム状態] の適切なバックアップ スケジュールと保持ポリシーを選択します (システム状態は、Windows Server にのみ適用され、Windows クライアントには適用されません)。

* ウィザードの **[初期バックアップの種類の選択 (ファイルとフォルダー)]** 画面で、 **[Transfer using Microsoft Azure Data Box disks]\(Microsoft Azure Data Box ディスクを使用して転送\)** オプションを選択し、 **[次へ]** をクリックします。

    ![初期バックアップの種類を選択する](./media/offline-backup-azure-data-box/initial-backup-type.png)

* メッセージが表示されたら、Azure サブスクリプションの所有者アクセス権があるユーザー資格情報を使用して Azure にサインインします。 成功すると、次のような画面が表示されます。

    ![リソースの作成と必要なアクセス許可の適用](./media/offline-backup-azure-data-box/creating-resources.png)

* 次に、MARS エージェントが、"配送済み" 状態にあるサブスクリプション内の Data Box ジョブをフェッチします。

    ![サブスクリプション ID の Data Box ジョブのフェッチ](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Data Box ディスクを開梱し、接続し、ロックを解除した、正しい Data Box の注文を選択します。 **[次へ]** をクリックします。

    ![Data Box の注文を選択する](./media/offline-backup-azure-data-box/select-databox-order.png)

* **[Data Box デバイスの検出]** 画面で **[デバイスを検出する]** をクリックします。 これにより、MARS エージェントは、ローカルに接続された Azure Data Box ディスクをスキャンして、次に示すように検出します。

    ![Data Box デバイスの検出](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Azure Data Box をネットワーク共有として接続している場合 (USB ポートが使用できないか、100 TB の Data Box デバイスを注文してマウントしたことがその理由)、最初は検出に失敗しますが、次に示すように、Data Box デバイスへのネットワーク パスを入力するオプションが提供されます。

    ![ネットワーク パスを入力する](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Azure Data Box ディスクのルート ディレクトリへのネットワーク パスを指定します。 このディレクトリには、次に示すように、*PageBlob* という名前のディレクトリが含まれている必要があります。
    >
    >![Azure Data Box ディスクのルート ディレクトリ](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >たとえば、ディスクのパスが `\\mydomain\myserver\disk1\` で、*disk1* に *PageBlob* というディレクトリが含まれている場合、MARS エージェント ウィザードで指定するパスは `\\mydomain\myserver\disk1\` になります。
    >
    >[Azure Data Box の 100 TB のデバイスをセットアップ](#setup-azure-data-box)する場合は、デバイスへのネットワーク パスとして `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` を指定します。

* **[次へ]** をクリックし、次の画面で **[完了]** をクリックして、Azure Data Box を使用するオフライン バックアップの構成とともにバックアップと保持のポリシーを保存します。

* 次の画面で、ポリシーが正常に保存されていることを確認します。

    ![ポリシーが正常に保存された](./media/offline-backup-azure-data-box/policy-saved.png)

* 上の画面で **[閉じる]** をクリックします。

* MARS エージェント コンソールの **[アクション]** ペインで * **[今すぐバックアップ]** をクリックし、次に示すようにウィザード画面で **[バックアップ]** をクリックします。

    ![今すぐバックアップ ウィザード](./media/offline-backup-azure-data-box/backup-now.png)

* MARS エージェントが、選択したデータの Azure Data Box デバイスへのバックアップを開始します。 これは、ファイルの数や、MARS エージェントがあるサーバーと Azure Data Box Disk の間の接続速度によっては、数時間から数日かかることがあります。

* データのバックアップが完了すると、次のような画面が MARS エージェントに表示されます。

    ![バックアップの進行状況の表示](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>バックアップ後の手順

このセクションでは、Azure Data Box Disk へのデータのバックアップが正常に完了した後に実行する手順について説明します。

* こちらの記事の手順に従って、[Azure Data Box Disk を Azure に発送](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up)します。 Azure Data Box の 100 TB のデバイスを使用した場合は、こちらの手順に従って [Azure Data Box を Azure に発送](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)してください。

* Azure portal で [Data Box ジョブを監視](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify)します。 Azure Data Box ジョブが "完了" になると、MARS エージェントは、次回のスケジュールされたバックアップ時に、ストレージ アカウントから Recovery Services コンテナーにデータを自動的に移動します。 その後、回復ポイントが正常に作成されると、そのバックアップ ジョブは "完了したジョブ" としてマークされます。

    >[!NOTE]
    >MARS エージェントは、ポリシー作成時にスケジュールされた時刻にバックアップをトリガーします。 ただし、これらのジョブは、ジョブが完了するまで "Waiting for Azure Data Box job to be completed (Azure Data Box ジョブが完了するのを待機しています)" というフラグを設定します。

* MARS エージェントが最初のバックアップに対応する回復ポイントを正常に作成したら、Azure Data Box ジョブに関連するストレージ アカウント (または特定のコンテンツ) を削除できます。

## <a name="troubleshooting"></a>トラブルシューティング

Microsoft Azure Backup (MAB) エージェントは、テナントで自動的に Azure AD アプリケーションを作成します。 このアプリケーションには、オフライン シード処理ポリシーを構成しているときに作成およびアップロードされる認証用の証明書が必要です。 証明書の作成と Azure AD アプリケーションへのアップロードには、Azure PowerShell を使用します。

### <a name="issue"></a>問題

オフライン バックアップを構成するときに、Azure PowerShell コマンドレットのバグが原因で、MAB エージェントによって作成された同じ Azure AD アプリケーションに複数の証明書を追加できないという問題が発生する可能性があります。 これは、同じまたは別のサーバーに対してオフライン シード処理ポリシーを構成している場合に影響があります。

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>問題がこの特定の根本原因によって発生しているかどうかを確認する方法

上記の問題が原因でエラーが発生していることを確認するには、次のいずれかの手順を実行します。

#### <a name="step-1"></a>手順 1

オフライン バックアップの構成時に MAB コンソールに次のエラー メッセージが表示されるかどうかを確認します。

![Unable to create Offline Backup policy for the current Azure account (現在の Azure アカウントのオフライン バックアップ ポリシーを作成できません)](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>手順 2.

* インストール パスにある **Temp** フォルダーを開きます (既定の temp フォルダーのパスは *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp* です)。 **CBUICurr** ファイルを探し、そのファイルを開きます。

* **CBUICurr** ファイルで、最後の行までスクロールし、エラーの原因が `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` であるかどうかを確認します。

### <a name="workaround"></a>回避策

この問題を解決するための回避策として、次の手順を実行して、ポリシーの構成を再試行します。

#### <a name="first-step"></a>最初の手順

インポート エクスポート ジョブが作成されるサブスクリプションで、管理者アクセス権がある別のアカウントを使用して、MAB UI に表示される PowerShell にサインインします。

#### <a name="second-step"></a>2 番目の手順

オフライン シード処理が構成されている他のサーバーがなく、`AzureOfflineBackup_<Azure User Id>` アプリケーションに依存する他のサーバーがない場合は、このアプリケーションを **Azure portal** >  **[Azure Active Directory]**  >  **[アプリの登録]** から削除します。

>[!NOTE]
> アプリケーション `AzureOfflineBackup_<Azure User Id>` に他のオフライン シード処理が構成されていないこと、およびこのアプリケーションに依存している他のサーバーがないことを確認します。 **[設定]**  >  **[キー]** の **[公開キー]** セクションの下に移動します。そこに他の公開キーが追加されていてはなりません。 参考のために次のスクリーンショットをご覧ください。
>
>![公開キー](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>3 番目の手順

オフライン バックアップを構成しようとしているサーバーから、次の操作を実行します。

1. **[Manage computer certificate application]\(コンピューター証明書の管理アプリケーション\)**  >  **[個人用]** タブの順に開いて、`CB_AzureADCertforOfflineSeeding_<ResourceId>` という名前の証明書を探します。

2. 上記の証明書を選択し、 **[すべてのタスク]** を右クリックして、秘密キーなしの .cer 形式で **[エクスポート]** します。

3. **ポイント 2** に記載されている Azure オフライン バックアップ アプリケーションにアクセスします。 **[設定]**  >  **[キー]**  >  **[公開キーのアップロード]** で、前の手順でエクスポートされた証明書をアップロードします。

    ![公開キーのアップロード](./media/offline-backup-azure-data-box/upload-public-key.png)

4. サーバーで、実行ウィンドウに **regedit** と入力してレジストリを開きます。

5. レジストリ *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider* に移動します。 **[CloudBackupProvider]** を右クリックして、`AzureADAppCertThumbprint_<Azure User Id>` という名前の新しい文字列値を追加します。

    >[!NOTE]
    > Azure ユーザー ID を取得するには、次の操作のいずれかを実行します。
    >
    >1. Azure に接続された PowerShell から、`Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”` コマンドを実行します。
    > 2. 次のレジストリ パスに移動します: *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup*; 名前:*CurrentUserId*

6. 前の手順で追加した文字列を右クリックして、 **[変更]** を選択します。 値として、**ポイント 2** でエクスポートした証明書のサムプリントを入力し、 **[OK]** をクリックします。

7. サムプリントの値を取得するには、証明書をダブルクリックして、 **[詳細]** タブを選択し、[サムプリント] フィールドが表示されるまで下方向にスクロールします。 **[サムプリント]** をクリックして、値をコピーします。

    ![証明書の [サムプリント] フィールド](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>疑問がある場合

発生した問題に関するご質問や説明については、[AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) までお問い合わせください。
