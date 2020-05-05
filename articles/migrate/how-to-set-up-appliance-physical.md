---
title: 物理サーバー用に Azure Migrate アプライアンスを設定する
description: 物理サーバー評価用 Azure Migrate アプライアンスの設定方法を学習します。
ms.service: azure-migrate
ms.topic: article
ms.date: 04/15/2020
ms.openlocfilehash: ddc70ee9430d3a767ce01191824c150a4dbd5e6f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538275"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>物理サーバー用にアプライアンスを設定する

この記事では、Azure Migrate で物理サーバーを評価する場合に、Azure Migrate アプライアンスを設定する方法について説明します。Server Assessment ツールを追加済みであることを確認してください。

Azure Migrate アプライアンスは、次の操作を行うために Azure Migrate Server Assessment によって使用される軽量アプライアンスです。

- オンプレミスのサーバーを検出する。
- 検出されたサーバーのメタデータとパフォーマンス データを、Azure Migrate Server Assessment に送信する。

Azure Migrate アプライアンスに関する[詳細を確認](migrate-appliance.md)します。


## <a name="appliance-deployment-steps"></a>アプライアンスのデプロイ手順

アプライアンスを設定するには、次のようにします。
- Azure portal から、Azure Migrate インストーラー スクリプトが含まれた ZIP ファイルをダウンロードします。
- ZIP ファイルの内容を抽出します。 管理特権で PowerShell コンソールを起動します。
- PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。
- アプライアンスを初めて構成し、Azure Migrate プロジェクトに登録します。

## <a name="download-the-installer-script"></a>インストーラー スクリプトをダウンロードする

アプライアンスの ZIP ファイルをダウンロードします。

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** をクリックします。
3. **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。

    ![VM をダウンロードする](./media/tutorial-assess-physical/download-appliance.png)


### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例 (パブリック クラウドの場合): ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
    - 使用例 (政府機関向けクラウドの場合): ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  ハッシュ値を確認します。
 
    - パブリック クラウドの場合 (最新のアプライアンス バージョン):

        **アルゴリズム** | **ハッシュ値**
          --- | ---
          MD5 | 1e92ede3e87c03bd148e56a708cdd33f
          SHA256 | a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c

    - Azure Government の場合 (最新のアプライアンス バージョン):

        **アルゴリズム** | **ハッシュ値**
          --- | ---
          MD5 | f81c155fc4a1409901caea948713913f


## <a name="run-the-azure-migrate-installer-script"></a>Azure Migrate インストーラー スクリプトを実行する
インストーラー スクリプトでは以下が実行されます。

- エージェントと、物理サーバーの検出と評価のための Web アプリケーションをインストールする。
- Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
- IIS 書き込み可能モジュールをダウンロードしてインストールする。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
- パスに次のファイルを作成する。
    - **構成ファイル**: %Programdata%\Microsoft Azure\Config
    - **ログ ファイル**: %Programdata%\Microsoft Azure\Logs

次のようにスクリプトを実行します。

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。  既存の Azure Migrate アプライアンス マシンでスクリプトを実行しないよう注意してください。
2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。
4. 次のコマンドを実行して、**AzureMigrateInstaller.ps1** という名前のスクリプトを実行します。

    - パブリック クラウドの場合: ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller> AzureMigrateInstaller.ps1 ```
    - Azure Government の場合: ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>AzureMigrateInstaller.ps1 ```

    スクリプトが正常に終了すると、アプライアンス Web アプリケーションが起動します。

問題が発生した場合は、トラブルシューティングのために、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log のスクリプト ログにアクセスできます。



### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンス VM から接続できることを確認します。

## <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

1. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリのショートカットをクリックして、デスクトップからアプリを開くこともできます。
2. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
    - **ライセンス**:ライセンス条項に同意し、サード パーティの情報を確認します。
    - **接続**:VM でインターネットにアクセスできることが、アプリによって確認されます。 VM でプロキシを使用する場合:
        - **[プロキシの設定]** をクリックし、 http://ProxyIPAddress または http://ProxyFQDN の形式で、プロキシ アドレスとリスニング ポートを指定します。
        - プロキシで認証が必要な場合は、資格情報を指定します。
        - サポートされるのは HTTP プロキシのみです。
    - **時刻同期**:時刻が確認されます。 VM の検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
    - **更新プログラムのインストール**:Azure Migrate Server Assessment によって、アプライアンスに最新の更新プログラムがインストールされていることが確認されます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. **[ログイン]** をクリックします。 表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
2. 新しいタブで、自分の Azure 資格情報を使用してサインインします。
    - 自分のユーザー名とパスワードを使用してサインインします。
    - PIN を使用したサインインはサポートされていません。
3. 正常にサインインした後、Web アプリに戻ります。
4. Azure Migrate プロジェクトが作成されたサブスクリプションを選択します。 その後、プロジェクトを選択します。
5. アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
6. **[登録]** をクリックします。


## <a name="start-continuous-discovery"></a>継続的な検出を開始する

アプライアンスから物理サーバーに接続し、検出を開始します。

1. **[資格情報の追加]** をクリックして、アプライアンスがサーバーの検出に使用するアカウント資格情報を指定します。  
2. **オペレーティング システム**、資格情報のフレンドリ名、ユーザー名、パスワードを指定します。 **[追加]** をクリックします。
Windows および Linux サーバーごとに 1 セットの資格情報を追加できます。
4. **[サーバーの追加]** をクリックし、サーバーの詳細 (FQDN/IP アドレスと資格情報のフレンドリ名 (行ごとに 1 つのエントリ)) を指定してサーバーに接続します。
3. **[検証]** をクリックします。 検証後、検出可能なサーバーの一覧が表示されます。
    - サーバーの検証が失敗した場合は、 **[状態]** 列のアイコンをポイントしてエラーを確認します。 問題を修正し、もう一度検証します。
    - サーバーを削除するには、 **[削除]** を選択します。
4. 検証後、 **[保存して検出を開始]** をクリックして、検出プロセスを開始します。

これで検出が開始されます。 検出された VM のメタデータが Azure portal に表示されるまでに、約 15 分かかります。

## <a name="verify-servers-in-the-portal"></a>ポータルでサーバーを確認する

検出の完了後、サーバーがポータルに表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、 **[検出済みサーバー]** の数を表示するアイコンをクリックします。


## <a name="next-steps"></a>次のステップ

Azure Migrate Server Assessment で[物理サーバーの評価](tutorial-assess-physical.md)を試します。
