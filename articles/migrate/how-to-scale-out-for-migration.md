---
title: エージェントレスの VMware 移行用に Azure Migrate スケールアウト アプライアンスを設定する
description: Azure Migrate スケールアウト アプライアンスを設定して Hyper-V VM を移行する方法について説明します。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 5ca821cb4f85deb77595e4a9029cc10298dbb884
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611975"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>VMware 仮想マシンの Azure へのエージェントレス移行をスケーリングする

この記事では、VMware VM の移行のために Azure Migrate Server Migration ツールのエージェントレスの移行方法を使用して、多数の VMware 仮想マシン (VM) を Azure に移行するためのスケールアウト アプライアンスの使用方法について説明します。

VMware 仮想マシンに対してエージェントレスの移行方法を使用すると、次のことができます。

- 1 つの Azure Migrate アプライアンスを使用して、1 つの vCenter サーバーから最大 300 個の VM を同時にレプリケートする。
- 移行用の 2 つ目のスケールアウト アプライアンスをデプロイすることによって、1 つの vCenter サーバーから最大 500 個の VM を同時にレプリケートする。

この記事では、次のことについて説明します。

- VMware 仮想マシンのエージェントレス移行用のスケールアウト アプライアンスを追加する
- スケールアウト アプライアンスを使用して最大 500 個の VM を同時に移行する。

##  <a name="prerequisites"></a>前提条件

始める前に、次の手順を実行する必要があります。

- Azure Migrate プロジェクトを作成します。
- Azure Migrate アプライアンス (プライマリ アプライアンス) をデプロイし、vCenter サーバーによって管理される VMware 仮想マシンの検出を完了します。
- 移行する 1 つ以上の仮想マシンのレプリケーションを構成します。
> [!IMPORTANT]
> 移行用のスケールアウト アプライアンスを追加する前に、レプリケートする仮想マシンをプロジェクトに少なくとも 1 つ配置する必要があります。

前の手順を実行する方法については、「[VMware 仮想マシンの Azure へのエージェントレス移行](./tutorial-migrate-vmware.md)」に関するチュートリアルを参照してください。

## <a name="deploy-a-scale-out-appliance"></a>スケールアウト アプライアンスをデプロイする

スケールアウト アプライアンスを追加するには、次の手順に従います。

1. **[検出]**  >  **[お使いのマシンは仮想化されていますか?]** をクリックします 
1. **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
1. 次のステップでエージェントレス レプリケーションを選択します。
1. アプライアンスの種類の選択メニューで **[Scale-out an existing primary appliance]\(既存のプライマリ アプライアンスをスケールアウトする\)** を選択します。
1. スケールアウトするプライマリ アプライアンス (検出の実行に使用したアプライアンス) を選択します。

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="スケールアウトのオンボード用の [マシンの検出] ページ":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Azure Migrate プロジェクト キーを生成する

1. **[Azure Migrate プロジェクト キーを生成します]** で、スケールアウト アプライアンスのサフィックス名を指定します。 サフィックスに使用できるのは英数字のみで、長さの制限は 14 文字です。
2. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [検出] ページを閉じないでください。
3. 生成されたキーをコピーします。 このキーは、後でスケールアウト アプライアンスの登録を完了するために必要になります。

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2.スケールアウト アプライアンスのインストーラーをダウンロードする

**[Azure Migrate アプライアンスをダウンロードします]** で、 **[ダウンロード]** をクリックします。 PowerShell インストーラー スクリプトをダウンロードして、スケールアウト アプライアンスをWindows Server 2016 を実行している既存のサーバーにデプロイする必要があります。対象は、必要なハードウェア構成 (32 GB RAM、8 つの vCPU、約 80 GB のディスク ストレージ、直接またはプロキシ経由のインターネット アクセス) を満たしている必要があります。
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="スケールアウト アプライアンス用のスクリプトをダウンロードする":::

> [!TIP]
> 次の手順を実行して、ダウンロードした zip ファイルのチェックサムを検証することができます。
>
> 1. 管理者としてコマンド プロンプトを開きます
> 2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例 (パブリック クラウドの場合): ```C:\>Get-FileHash -Path .\AzureMigrateInstaller-VMware-Public-Scaleout.zip -Algorithm SHA256 ```
> 3. 計算されたハッシュ値が次の文字列と一致しない場合は、ポータルから最新バージョンのスケールアウト アプライアンス インストーラーをダウンロードします。1E6B6E3EE8B2A800818B925F5DA67EF7874DAD87E32847120B32F3E21F5960F9

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Azure Migrate インストーラー スクリプトを実行する
インストーラー スクリプトでは以下が実行されます。

- より多くの同時サーバー レプリケーションを実行するために、ゲートウェイ エージェントとアプライアンス構成マネージャーをインストールする。
- Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
- IIS 書き込み可能モジュールをダウンロードしてインストールする。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
- パスに次のファイルを作成する。
    - **構成ファイル**: %Programdata%\Microsoft Azure\Config
    - **ログ ファイル**: %Programdata%\Microsoft Azure\Logs

次のようにスクリプトを実行します。

1. スケールアウト アプライアンスをホストするサーバー上のフォルダーに zip ファイルを抽出します。  既存の Azure Migrate アプライアンスが存在するサーバー上でスクリプトを実行しないよう注意してください。
2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。
3. PowerShell のディレクトリを、ダウンロードした zip ファイルの内容が抽出されたフォルダーに変更します。
4. 次のコマンドを実行して、**AzureMigrateInstaller.ps1** という名前のスクリプトを実行します。

    - パブリック クラウドの場合: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    スクリプトの実行が完了すると、アプライアンス構成マネージャーが起動します。

問題が発生した場合は、トラブルシューティングのために次のスクリプト ログにアクセスできます: <br/> C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log。


### <a name="4-configure-the-appliance"></a>4. アプライアンスを構成する

開始する前に、[これらの Azure エンドポイント](migrate-appliance.md#public-cloud-urls)にスケールアウト アプライアンスからアクセスできることを確認してください。

- スケールアウト アプライアンス サーバーに接続できる任意のマシンでブラウザーを開き、アプライアンス構成マネージャーの URL を開きます (**https://*スケールアウト アプライアンスの名前または IP アドレス*: 44368**)。

   または、スケールアウト アプライアンス サーバーのデスクトップから構成マネージャーへのショートカットを使用して、構成マネージャーを開くこともできます。
- **ライセンス条項** に同意し、サード パーティの情報を確認します。
- 構成マネージャー > **[前提条件のセットアップ]** で、以下を実行します。
   - **接続**:サーバーがインターネットにアクセスできることが、アプライアンスによって確認されます。 サーバーでプロキシを使用する場合は、次の操作を行います。
     1. **[プロキシの設定]** をクリックし、 http://ProxyIPAddress または http://ProxyFQDN) の形式のプロキシ アドレス、およびリッスン ポートを指定します。
     2. プロキシで認証が必要な場合は、資格情報を指定します。
     3. サポートされるのは HTTP プロキシのみです。
     4. プロキシの詳細を追加した場合、またはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
   - **時刻同期**:検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
   - **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、アプライアンス サーバーで実行されているサービスの状態とバージョンを確認できます。
   - **Install VDDK\(VDDK のインストール\)** :アプライアンスでは、VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが確認されます。 インストールされていない場合は、アプライアンス構成マネージャー画面の **インストール手順** に記載されているとおりに、VMware から VDDK 6.7 をダウンロードし、ダウンロードした zip の内容をアプライアンス上の指定された場所に抽出します。


### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーした **Azure Migrate プロジェクト キー** を貼り付けます。 このキーがない場合は、 **[Server Assessment] > [検出] > [Manage existing appliances]\(既存のアプライアンスの管理\)** に移動して、プライマリ アプライアンス名を選択し、これに関連付けられたスケールアウト アプライアンスを見つけ、対応するキーをコピーします。
1. Azure で認証するには、デバイス コードが必要です。 **[ログイン]** をクリックすると、次に示すように、デバイス コードを含むモーダルが開きます。
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="デバイス コードを示すモーダル":::

1. **[Copy code & Login]\(コードのコピーとログイン\)** をクリックしてデバイス コードをコピーし、新しいブラウザー タブで Azure ログイン プロンプトを開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、デバイス コードを貼り付け、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
3. ログインせずにログイン タブを誤って閉じた場合は、アプライアンス構成マネージャーのブラウザー タブを最新の情報に更新して、[ログイン] ボタンを再度有効にする必要があります。
1. 正常にログインしたら、アプライアンス構成マネージャーで前のタブに戻ります。
1. ログに使用した Azure ユーザー アカウントに、キーの生成時に作成した Azure リソースに対する正しいアクセス許可が付与されている場合、アプライアンスの登録が開始されます。
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="アプライアンス構成マネージャーのパネル 2":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>プライマリ アプライアンスからアプライアンス構成をインポートする

スケールアウト アプライアンスの登録を完了するには、 **[インポート]** をクリックして、プライマリ アプライアンスから必要な構成ファイルを取得します。

1. **[インポート]** をクリックすると、プライマリ アプライアンスから必要な構成ファイルをインポートする方法を示すポップアップ ウィンドウが開きます。
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="[構成のインポート] モーダル":::
1. プライマリ アプライアンスに (リモート デスクトップで) ログインし、次の PowerShell コマンドを実行します。

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. 上記のコマンドを実行して作成した zip ファイルをスケールアウト アプライアンスにコピーします。 ｚip ファイルには、スケールアウト アプライアンスを登録するために必要な構成ファイルが含まれています。
1. 前の手順で開いたポップアップ ウィンドウで、コピーされた構成 zip ファイルの場所を選択し、 **[保存]** をクリックします。

ファイルが正常にインポートされると、スケールアウト アプライアンスの登録が完了し、最後に成功したインポートのタイムスタンプが表示されます。 **[詳細の表示]** をクリックして、登録の詳細を確認することもできます。
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="スクリーンショットは、Azure Migrate プロジェクトへのスケールアウト アプライアンス登録を示しています。":::

この時点で、スケールアウト アプライアンスが vCenter サーバーに接続できることを再検証する必要があります。 **[再検証]** をクリックして、スケールアウト アプライアンスから vCenter Server への接続を検証します。
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="スクリーンショットは、検証するビューの資格情報と検出のソースを示しています。":::

> [!IMPORTANT]
> プライマリ アプライアンスで vCenter Server の資格情報を編集する場合は、構成ファイルをスケールアウト アプライアンスに再度インポートして最新の構成を取得し、進行中のレプリケーションを続行するようにしてください。<br/> スケールアウト アプライアンスが不要な場合は、スケールアウト アプライアンスを無効にしてください。 不要なときにスケールアウト アプライアンスを無効にする方法について詳しくは、[**こちら**](./common-questions-appliance.md)をご覧ください。

## <a name="replicate"></a>レプリケート

1. スケール アウト アプライアンスが登録されたら、[Azure Migrate: Server Migration] タイルで **[レプリケート]** をクリックします。

2.  画面の手順に従って、より多くの仮想マシンのレプリケートを開始します。 

スケールアウト アプライアンスを配備すると、500 個の VM を同時にレプリケートできるようになります。 Azure portal を使用して、200 個のバッチ単位で VM を移行することもできます。

Azure Migrate Server Migration ツールでは、レプリケーションのためにプライマリ アプライアンスとスケールアウト アプライアンス間で仮想マシンの配布が行われます。 レプリケーションが完了したら、仮想マシンを移行できます。

> [!TIP]
> 多数の仮想マシンを移行する場合は、最適なパフォーマンスを得るために、200 個のバッチ単位で仮想マシンを移行することをお勧めします。
  
## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。
- スケールアウト アプライアンスを構成する方法
- スケールアウト アプライアンスを使用して VM をレプリケートする方法


Azure Migrate: Server Migration ツールを使用してサーバーを Azure に移行する方法については、[こちら](./tutorial-migrate-vmware.md)をご覧ください。