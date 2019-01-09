---
title: 'チュートリアル: Azure File Sync を使用して Windows ファイル サーバーを拡張する | Microsoft Docs'
description: Azure File Sync を使用して Windows ファイル サーバーを拡張する方法について、開始から完了まで説明します。
services: storage
author: wmgries
ms.service: storage
ms.topic: tutorial
ms.date: 10/23/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 3ebf450f4e84fed572307a18f20f36013e32c7a5
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53630701"
---
# <a name="tutorial-extend-windows-file-servers-with-azure-file-sync"></a>チュートリアル:Azure File Sync を使用して Windows ファイル サーバーを拡張する
このチュートリアルでは、Azure File Sync を使用して Windows Server のストレージ容量を拡張するための基本的な手順を示します。このチュートリアルで使用しているのは Windows Server Azure VM ですが、通常はオンプレミス サーバーに対してこのプロセスを実行します。 ご自分の環境に Azure File Sync をデプロイする準備ができている場合は、代わりに「[Azure File Sync のデプロイ](storage-sync-files-deployment-guide.md)」の記事を使用してください。

> [!div class="checklist"]
> * ストレージ同期サービスのデプロイ
> * Azure File Sync で使用する Windows Server の準備
> * Azure File Sync エージェントをインストールする
> * Windows Server をストレージ同期サービスに登録する
> * 同期グループとクラウド エンドポイントを作成する
> * サーバー エンドポイントを作成する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン
Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="prepare-your-environment"></a>環境を準備する
Azure File Sync をデプロイする前に、このチュートリアルのためにいくつかの設定を行う必要があります。Azure ストレージ アカウントとファイル共有を作成し、Windows Server 2016 Datacenter VM を作成して、そのサーバーを Azure File Sync 用に準備します。

### <a name="create-a-folder-and-txt-file"></a>フォルダーと .txt ファイルを作成する

ローカル コンピューターに *FilesToSync* という名前の新しいフォルダーを作成し、*mytestdoc.txt* という名前のテキスト ファイルを追加します。 このチュートリアルの後の方で、そのファイルをファイル共有にアップロードします。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

### <a name="create-a-file-share"></a>ファイル共有を作成する
次に、ファイル共有を作成します。

1. Azure ストレージ アカウントのデプロイが完了したら、**[リソースに移動]** をクリックします。
1. ストレージ アカウントのウィンドウで **[ファイル]** をクリックします。

    ![[ファイル] をクリックする](./media/storage-sync-files-extend-servers/click-files.png)

1. **+ [ファイル共有]** をクリックします。

    ![ファイル共有の追加ボタンをクリックする](./media/storage-sync-files-extend-servers/create-file-share-portal2.png)

1. 新しいファイル共有に *afsfileshare* という名前を付け、**[クォータ]** に「1」と入力してから、**[作成]** をクリックします。 クォータは最大で 5 TiB にすることができますが、このチュートリアルでは 1 GB しか必要ありません。

    ![新しいファイル共有に名前とクォータを指定する](./media/storage-sync-files-extend-servers/create-file-share-portal3.png)

1. 新しいファイル共有を選択し、ファイル共有上の場所に対して **[アップロード]** をクリックします。

    ![ファイルをアップロードする](./media/storage-sync-files-extend-servers/create-file-share-portal5.png)

1. .txt ファイルを作成した *FilesToSync* フォルダーを参照し、*mytestdoc.txt* を選択して **[アップロード]** をクリックします。

    ![ファイル共有を参照する](./media/storage-sync-files-extend-servers/create-file-share-portal6.png)

この時点で、Azure ストレージ アカウントと、1 つのファイルを含む Azure のファイル共有を作成できました。 次に、このチュートリアルでのオンプレミス サーバーを表す、Windows Server 2016 Datacenter がある Azure VM を作成します。

### <a name="deploy-a-vm-and-attach-a-data-disk"></a>VM をデプロイしてデータ ディスクを接続する

1. 次に、ポータルの左側にあるメニューを展開して、Azure portal の左上隅にある **[リソースの作成]** を選択します。
1. **Azure Marketplace** リソースの一覧の上にある検索ボックスで **Windows Server 2016 Datacenter** を検索して選択し、**[作成]** を選択します。
1. **[基本]** タブの **[プロジェクトの詳細]** で、このチュートリアルのために作成したリソース グループを選択します。

   ![ポータルのブレードで VM に関する基本情報を入力する](./media/storage-sync-files-extend-servers/vm-resource-group-and-subscription.png)

1. **[インスタンスの詳細]** で、VM の名前を指定します (*myVM* など)。
1. **[リージョン]**、**[可用性オプション]**、**[イメージ]**、**[サイズ]** は、既定の設定のままにしておきます。
1. **[管理者アカウント]** で、VM の **[ユーザー名]** と **[パスワード]** を指定します。
1. **[受信ポートの規則]** で、**[Allow selected ports] (選択されたポートを許可する)** を選択してから、ドロップダウンから **[RDP (3389)]** と **[HTTP]** を選択します。

   VM を作成する前に、データ ディスクを作成する必要があります。

1. **[Next:Disks]\(次へ: ディスク\)** をクリックします

   ![データ ディスクを追加する](./media/storage-sync-files-extend-servers/vm-add-data-disk.png)

1. **[ディスク]** タブの **[ディスクのオプション]** は、既定値のままにしておきます。
1. **[データ ディスク]** で **[新しいディスクを作成し接続する]** をクリックします。

1. このチュートリアルのために **[サイズ (GiB)]** を **1 GB** に変更する以外は、既定値のままにしておきます。

   ![データ ディスクの詳細](./media/storage-sync-files-extend-servers/vm-create-new-disk-details.png)

1. Click **OK**.
1. **[Review + create]\(レビュー + 作成\)** をクリックします。
1. **Create** をクリックしてください。

   **[通知]** アイコンをクリックすると、**デプロイの進行状況**を見ることができます。 新しい VM の作成が完了するまでに数分かかります。

1. VM のデプロイが完了したら、**[リソースに移動]** をクリックします。

   ![リソースに移動](./media/storage-sync-files-extend-servers/vm-gotoresource.png)

   この時点で、新しい仮想マシンを作成し、データ ディスクを接続できました。 次に、VM に接続する必要があります。

### <a name="connect-to-your-vm"></a>VM に接続する

1. Azure portal の仮想マシンのプロパティ ページで、**[接続]** をクリックします。

   ![ポータルから Azure VM に接続する](./media/storage-sync-files-extend-servers/connect-vm.png)

1. **[Connect to virtual machine]\(仮想マシンへの接続\)** ページで、ポート 3389 を介して **IP アドレス**を使って接続する既定のオプションをそのまま使用して、**[RDP ファイルのダウンロード]** をクリックします。

   ![RDP ファイルのダウンロード](./media/storage-sync-files-extend-servers/download-rdp.png)

1. ダウンロードした RDP ファイルを開き、プロンプトが表示されたら **[接続]** をクリックします。
1. **[Windows セキュリティ]** ウィンドウで、**[その他]**、**[別のアカウントを使用する]** の順に選択します。 ユーザー名として「*localhost\username*」と入力し、仮想マシン用に作成したパスワードを入力して、**[OK]** をクリックします。

   ![その他](./media/storage-sync-files-extend-servers/local-host2.png)

1. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** をクリックして接続を作成します。

### <a name="prepare-the-windows-server"></a>Windows Server を準備する
**Windows Server 2016 Datacenter** サーバーで、**[Internet Explorer セキュリティ強化の構成]** を無効にします。 この手順は、初回のサーバー登録でのみ必要です。 サーバーの登録後に再び有効にできます。

**Windows Server 2016 Datacenter** VM で、**サーバー マネージャー**が自動的に開かれます。  **サーバー マネージャー**が既定で開かれない場合は、エクスプローラーでそれを検索します。

1. **サーバー マネージャー**で **[ローカル サーバー]** をクリックします。

   ![サーバー マネージャー UI の左側にある [ローカル サーバー]](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-1.png)

1. **[プロパティ]** サブウィンドウで、**[IE セキュリティ強化の構成]** リンクを選択します。  

    ![サーバー マネージャー UI の [IE セキュリティ強化の構成] ウィンドウ](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-2.png)

1. **[Internet Explorer セキュリティ強化の構成]** ダイアログ ボックスで、**[管理者]** と **[ユーザー]** について **[オフ]** を選択します。

    ![[オフ] が選択された [Internet Explorer セキュリティ強化の構成] ポップアップ ウィンドウ](media/storage-sync-files-extend-servers/prepare-server-disable-ieesc-3.png)

   これで、データ ディスクを VM に追加することができます。

### <a name="add-the-data-disk"></a>データ ディスクを追加する

1. まだ **Windows Server 2016 Datacenter** VM 上で、**[Files and storage services]\(ファイル サービスおよびストレージ サービス\)** > **[ボリューム]** > **[ディスク]** の順にクリックします。

    ![データ ディスク](media/storage-sync-files-extend-servers/your-disk.png)

1. **Msft Virtual Disk** という名前の 1 GB のディスクを右クリックし、**[新しいボリューム]** をクリックします。
1. 既定値をそのままにしてウィザードを完了し、割り当てられたドライブ文字を書き留め、**[作成]** をクリックします。
1. **[閉じる]** をクリックします。

   この時点で、ディスクをオンラインにし、ボリュームを作成できました。 VM 上でエクスプローラーを開き、新しいドライブが存在していることを確認することで、データ ディスクの追加が成功したことを確認できます。

1. VM 上のエクスプローラーで **[この PC]** を展開し、新しいドライブをダブルクリックします。 この例では、F: ドライブです。
1. 右クリックし、**[新規作成]** > **[フォルダー]** の順に選択します。 フォルダーに *FilesToSync* という名前を付けます。
1. **FilesToSync** フォルダーをダブルクリックします。
1. 右クリックし、**[新規作成]** > **[テキスト ドキュメント]** の順に選択します。 テキスト ファイルに *MyTestFile* という名前を付けます。

    ![新しいテキスト ファイルを追加する](media/storage-sync-files-extend-servers/new-file.png)

1. **エクスプローラー**と**サーバー マネージャー**を閉じます。

### <a name="download-the-azure-powershell-module"></a>Azure PowerShell モジュールをダウンロードする
次に、**Windows Server 2016 Datacenter** VM で、**Azure PowerShell モジュール**をサーバーにインストールします。

1. VM で管理者特権の PowerShell ウィンドウを開きます
1. 次のコマンドを実行します。

   ```powershell
   Install-Module -Name Az -AllowClobber
   ```

   > [!NOTE]
   > ご利用の NuGet のバージョンが 2.8.5.201 未満である場合、最新バージョンの NuGet をダウンロードしてインストールするように求められます。

   既定では、PowerShell ギャラリーは、PowerShellGet の信頼できるリポジトリとしては構成されません。 PSGallery の初回使用時には、次のプロンプトが表示されます。

   ```output
   Untrusted repository

   You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the Set-PSRepository cmdlet.

   Are you sure you want to install the modules from 'PSGallery'?
   [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):
   ```

1. インストールを続行するには、`Yes` または `Yes to All` を選択します。

`Az` モジュールは、Azure PowerShell コマンドレットのロールアップ モジュールです。 これをインストールすると、利用可能なすべての Azure Resource Manager モジュールがダウンロードされ、コマンドレットを使用できるようになります。

この時点で、チュートリアルのための環境設定が完了し、**ストレージ同期サービス**のデプロイを開始する準備が整いました。

## <a name="deploy-the-service"></a>サービスをデプロイする 
Azure File Sync のデプロイでは最初に、選択したサブスクリプションのリソース グループに**ストレージ同期サービス** リソースを配置します。 ストレージ同期サービスは、お客様がデプロイしたサブスクリプションとリソース グループからアクセス許可を継承します。

1. Azure portal で **[リソースの作成]** をクリックし、次に **Azure File Sync** を検索します。
1. 検索結果で、**[Azure File Sync]** を選択します。
1. **[作成]** を選択して、**[ストレージ同期のデプロイ]** タブを開きます。

   ![ストレージ同期のデプロイ](media/storage-sync-files-extend-servers/afs-info.png)

   開いたウィンドウに、次の情報を入力します。

   | 値 | [説明] |
   | ----- | ----- |
   | **名前** | ストレージ同期サービスの (サブスクリプションごとに) 一意の名前。<br><br>このチュートリアルでは *afssyncservice02* を使用しています。 |
   | **サブスクリプション** | このチュートリアルで使用しているサブスクリプション。 |
   | **[リソース グループ]** | このチュートリアルで使用しているリソース グループ。<br><br>このチュートリアルでは、*afsresgroup101918* を使用しています。 |
   | **場所** | 米国東部 |

1. 完了したら、**[作成]** を選択して、**ストレージ同期サービス**をデプロイします。
1. **[通知]** タブ、**[リソースに移動]** の順にクリックします。

## <a name="install-the-agent"></a>エージェントをインストールする
Azure File Sync エージェントは、Windows Server を Azure ファイル共有と同期できるようにするダウンロード可能なパッケージです。

1. **Windows Server 2016 Datacenter** VM に戻り、**Internet Explorer** を開きます。
1. [Microsoft ダウンロード センター](https://go.microsoft.com/fwlink/?linkid=858257)に移動します。 **Azure File Sync エージェント**のセクションまで下へスクロールして、**[ダウンロード]** をクリックします。

   ![同期エージェントのダウンロード](media/storage-sync-files-extend-servers/sync-agent-download.png)

1. **StorageSyncAgent_V3_WS2016.EXE** のチェック ボックスをオンにし、**[次へ]** をクリックします。

   ![エージェントを選択する](media/storage-sync-files-extend-servers/select-agent.png)

1. ファイルに対して、**[一度のみ許可]** > **[実行]** > **[開く]** の順に選択します。
1. まだ PowerShell ウィンドウを閉じていない場合は閉じます。
1. **ストレージ同期エージェントのセットアップ ウィザード**で、既定値を受け入れます。
1. **[インストール]** をクリックします。
1. **[完了]** をクリックします。

Azure Sync Service をデプロイし、**Windows Server 2016 Datacenter** VM にエージェントをインストールしました。 次に、VM を**ストレージ同期サービス**に登録する必要があります。

## <a name="register-windows-server"></a>Windows Server を登録する
Windows Server をストレージ同期サービスに登録すると、サーバー (またはクラスター) とストレージ同期サービスの間に信頼関係が確立されます。 1 つのサーバーは、1 つのストレージ同期サービスにのみ登録でき、同じストレージ同期サービスに関連付けられている他のサーバーおよび Azure ファイル共有と同期できます。

**Azure File Sync エージェント**がインストールされると、サーバー登録 UI が自動的に開かれます。 開かない場合、これを次に示すファイルの場所から手動で開くことができます: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe。

1. VM でサーバー登録 UI が開いたら、**[OK]** をクリックします。
1. **[サインイン]** をクリックして開始します。
1. Azure アカウントの資格情報を使用してサインインし、**[サインイン]** をクリックします。
1. 次の情報を指定します。

   ![サーバー登録 UI のスクリーンショット](media/storage-sync-files-extend-servers/signin.png)

   | | |
   | ----- | ----- |
   | 値 | 説明 |
   | **Azure サブスクリプション** | このチュートリアルのストレージ同期サービスが含まれているサブスクリプション。 |
   | **リソース グループ** | このチュートリアルのストレージ同期サービスが含まれているリソース グループ。 このチュートリアル全体で、*afsresgroup101918* を使用してきました。 |
   | **ストレージ同期サービス** | このチュートリアルで使用したストレージ同期サービスの名前。 このチュートリアル全体で、*afssyncservice02* を使用してきました。 |

1. **[登録]** をクリックして、サーバーの登録を完了します。
1. 登録プロセスの一環として、追加のサインインを求められます。 サインインして **[次へ]** をクリックします。
1. Click **OK**.

## <a name="create-a-sync-group"></a>同期グループを作成する
同期グループは、一連のファイルの同期トポロジを定義します。 同期グループには、Azure ファイル共有と 1 つ以上のサーバー エンドポイントを表す、1 つのクラウド エンドポイントが含まれている必要があります。 サーバー エンドポイントは、登録されているサーバー上のパスを表します。

1. 同期グループを作成するには、[Azure portal](https://portal.azure.com/) で、このチュートリアルのために作成したストレージ同期サービスの **+ [同期グループ]** を選択します。 このチュートリアルでは、例として *afssyncservice02* を使用しました。

   ![Azure Portal で新しい同期グループを作成する](media/storage-sync-files-extend-servers/add-sync-group.png)

1. 表示されるウィンドウで次の情報を入力して、同期グループとクラウド エンドポイントを作成します。

   | 値 | 説明 |
   | ----- | ----- |
   | **同期グループ名** | この名前は、ストレージ同期サービス内で一意である必要がありますが、理にかなった任意の名前を指定できます。 このチュートリアルでは *afssyncgroup* を使用しています。|
   | **サブスクリプション** | このチュートリアルのストレージ同期サービスをデプロイしたサブスクリプション。 |
   | **ストレージ アカウント** |**[ストレージ アカウントの選択]** をクリックします。 表示されるウィンドウで、このチュートリアルのために作成した Azure ファイル共有のあるストレージ アカウントを選択します。 ここでは *afsstoracct101918* を使用しました。 |
   | **Azure ファイル共有** | このチュートリアルのために作成した Azure ファイル共有の名前。 ここでは *afsfileshare* を使用しました。 |

1. **Create** をクリックしてください。

同期グループを選択すると、1 つの**クラウド エンドポイント**ができたことを確認できます。

## <a name="add-a-server-endpoint"></a>サーバー エンドポイントを追加する
サーバー エンドポイントは、登録済みサーバー上の特定の場所を表します。たとえば、サーバー ボリュームのフォルダーなどです。

1. サーバー エンドポイントを追加するには、新しく作成した同期グループを選択し、**[サーバー エンドポイントの追加]** を選択します。

   ![[同期グループ] ウィンドウで新しいサーバー エンドポイントを追加する](media/storage-sync-files-extend-servers/add-server-endpoint.png)

1. **[サーバー エンドポイントの追加]** ウィンドウで、次の情報を入力してサーバー エンドポイントを作成します。

   | | |
   | ----- | ----- |
   | 値 | 説明 |
   | **登録済みサーバー** | このチュートリアルのために作成したサーバーの名前。 このチュートリアルでは *afsvm101918* を使用しました |
   | **パス** | このチュートリアルのために作成したドライブへの Windows Server パス。 この例では、*f:\filestosync* です。 |
   | **クラウドの階層化** | このチュートリアルでは、無効のままにします。 |
   | **ボリュームの空き領域** | このチュートリアルでは空白のままにします。 |

1. **Create** をクリックしてください。

Azure ファイル共有と Windows Server でファイルが同期されるようになりました。

![Azure Storage が正常に同期された](media/storage-sync-files-extend-servers/files-synced-in-azurestorage.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure File Sync を使用して Windows Server のストレージ容量を拡張するための基本的な手順を説明しました。Azure File Sync のデプロイ計画の全体像については、次のリンクに従ってください。

> [!div class="nextstepaction"]
> [Azure File Sync のデプロイの計画](./storage-sync-files-planning.md)
