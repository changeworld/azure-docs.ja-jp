---
title: 物理サーバー用に Azure Migrate アプライアンスを設定する
description: 物理サーバー検出および評価用 Azure Migrate アプライアンスの設定方法を学習します。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: 171b03e53266949f1ceae79dd0bbeae7ab146eda
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500452"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>物理サーバー用にアプライアンスを設定する

この記事では、Azure Migrate: Discovery and Assessment ツールで物理サーバーを評価する場合に、Azure Migrate アプライアンスを設定する方法について説明します。

Azure Migrate アプライアンスは、次の操作を行うために Azure Migrate: Discovery and Assessment によって使用される軽量アプライアンスです。

- オンプレミスのサーバーを検出する。
- 検出されたサーバーのメタデータとパフォーマンス データを、Azure Migrate: Discovery and Assessment に送信する。

Azure Migrate アプライアンスに関する[詳細を確認](migrate-appliance.md)します。


## <a name="appliance-deployment-steps"></a>アプライアンスのデプロイ手順

アプライアンスを設定するには、次のようにします。

1. ポータルで、アプライアンス名を指定してプロジェクト キーを生成します。
2. Azure portal から、Azure Migrate インストーラー スクリプトが含まれた ZIP ファイルをダウンロードします。
3. ZIP ファイルの内容を抽出します。 管理特権で PowerShell コンソールを起動します。
4. PowerShell スクリプトを実行して、アプライアンス構成マネージャーを起動します。
5. 初回のアプライアンス構成を行い、プロジェクト キーを使用してプロジェクトに登録します。

### <a name="generate-the-project-key"></a>プロジェクト キーを生成する

1. **[移行の目標]**  >  **[Windows、Linux、SQL Server]**  >  **[Azure Migrate: Discovery and Assessment]\(Azure Migrate: 検出および評価\)** で、 **[検出]** を選択します。
2. **[Discover servers]\(サーバーの検出\)**  >  **[お使いのサーバーは仮想化されていますか?]** で、 **[物理またはその他 (AWS、GCP、Xen など)]** を選択します。
3. **[1: プロジェクト キーを生成します]** で、物理または仮想サーバーの検出用に設定する Azure Migrate アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
1. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [Discover Servers]\(サーバーの検出\) ページを閉じないでください。
1. Azure リソースが正常に作成されると、**プロジェクト キー** が生成されます。
1. このキーはアプライアンスを設定する際、登録を完了するために必要なので、コピーしておきます。

   ![[キーの生成] の選択](./media/tutorial-assess-physical/generate-key-physical-1.png)

### <a name="download-the-installer-script"></a>インストーラー スクリプトをダウンロードする

**[2:Azure Migrate アプライアンスをダウンロードします]** で、 **[ダウンロード]** をクリックします。

### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  最新のアプライアンス バージョンとハッシュ値を確認します。

    **ダウンロード** | **ハッシュ値**
    --- | ---
    [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140334) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> 同じスクリプトを使用して、Azure パブリックまたは Azure Government クラウドに物理アプライアンスを設定できます。

### <a name="run-the-azure-migrate-installer-script"></a>Azure Migrate インストーラー スクリプトを実行する

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。  既存の Azure Migrate アプライアンスが存在するサーバー上でスクリプトを実行しないよう注意してください。
2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。
4. 次のコマンドを実行して、**AzureMigrateInstaller.ps1** という名前のスクリプトを実行します。

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. シナリオ、クラウド、接続性からそれぞれオプションを選択して、必要な構成でアプライアンスをデプロイします。 たとえば、以下に示す選択内容の場合は、**Azure パブリック クラウド** 上の **既定の _(パブリック エンドポイント)_ 接続性** を使用して、**物理サーバー** _(または AWS、GCP、Xen などの他のクラウドで実行されているサーバー)_ を検出し、Azure Migrate プロジェクトに評価するようにアプライアンスが設定されます。

    :::image type="content" source="./media/tutorial-discover-physical/script-physical-default-1.png" alt-text="必要な構成でアプライアンスを設定する方法を示すスクリーンショット。":::

6. インストーラー スクリプトでは以下が実行されます。

    - エージェントと Web アプリケーションをインストールします。
    - Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
    - IIS 書き込み可能モジュールをダウンロードしてインストールする。
    - Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
    - パスに次のファイルを作成する。
        - **構成ファイル**: %Programdata%\Microsoft Azure\Config
        - **ログ ファイル**: %Programdata%\Microsoft Azure\Logs

スクリプトが正常に実行されると、アプライアンス構成マネージャーが自動的に起動します。

> [!NOTE]
> 問題が発生した場合は、トラブルシューティングのために、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log のスクリプト ログにアクセスできます。

### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。

### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

1. アプライアンスに接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリのショートカットをクリックして、デスクトップからアプリを開くこともできます。
2. **ライセンス条項** に同意し、サード パーティの情報を確認します。
1. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
    - **接続**:サーバーがインターネットにアクセスできることが、アプリによって確認されます。 サーバーでプロキシを使用する場合は、次の操作を行います。
        - **[プロキシを設定する]** をクリックし、プロキシ アドレス (http://ProxyIPAddress または http://ProxyFQDN) の形式) とリッスン ポートを指定します。
        - プロキシで認証が必要な場合は、資格情報を指定します。
        - サポートされるのは HTTP プロキシのみです。
        - プロキシの詳細を追加した場合、またはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
    - **時刻同期**:時刻が確認されます。 サーバーの検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
    - **更新プログラムのインストール**: Azure Migrate の検出および評価によって、アプライアンスに最新の更新プログラムがインストールされていることが確認されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、アプライアンスで実行されているコンポーネントの状態とバージョンを確認できます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーした **プロジェクト キー** を貼り付けます。 このキーがない場合は、 **[Azure Migrate: Discovery and Assessment]\(Azure Migrate: 検出および評価\) > [検出] > [既存のアプライアンスの管理]** に移動して、キーの生成時に指定したアプライアンス名を選択して、対応するキーをコピーします。
1. Azure で認証するには、デバイス コードが必要です。 **[ログイン]** をクリックすると、次に示すように、デバイス コードを含むモーダルが開きます。

    ![デバイス コードを示すモーダル](./media/tutorial-discover-vmware/device-code.png)

1. **[Copy code & Login]\(コードのコピーとログイン\)** をクリックしてデバイス コードをコピーし、新しいブラウザー タブで Azure ログイン プロンプトを開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、デバイス コードを貼り付け、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
3. ログインせずにログイン タブを誤って閉じた場合は、アプライアンス構成マネージャーのブラウザー タブを最新の情報に更新して、[ログイン] ボタンを再度有効にする必要があります。
1. 正常にログインしたら、アプライアンス構成マネージャーで前のタブに戻ります。
4. ログに使用した Azure ユーザー アカウントに、キーの生成時に作成した Azure リソースに対する正しい[アクセス許可](./tutorial-discover-physical.md)が付与されている場合、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** をクリックすることで登録の詳細を確認できるようになります。


## <a name="start-continuous-discovery"></a>継続的な検出を開始する

次に、アプライアンスから検出対象の物理サーバーに接続し、検出を開始します。

1. **[ステップ 1:Provide credentials for discovery of Windows and Linux physical or virtual servers]\(Windows および Linux 物理サーバーまたは仮想サーバーを検出するための資格情報を指定する\)** で、 **[資格情報の追加]** をクリックします。
1. Windows サーバーの場合、ソースの種類として **[Windows サーバー]** を選択し、資格情報のフレンドリ名を指定して、ユーザー名とパスワードを追加します。 **[Save]** をクリックします。
1. Linux サーバーでパスワードベースの認証を使用している場合は、ソースの種類として **[Linux サーバー (パスワードベース)]** を選択し、資格情報のフレンドリ名を指定して、ユーザー名とパスワードを追加します。 **[Save]** をクリックします。
1. Linux サーバーで SSH キーベースの認証を使用している場合は、ソースの種類として **[Linux サーバー (SSH キーベース)]** を選択し、資格情報のフレンドリ名を指定して、ユーザー名を追加します。SSH 秘密キー ファイルを参照して選択します。 **[Save]** をクリックします。

    - Azure Migrate では、RSA、DSA、ECDSA、ed25519 の各アルゴリズムを使用して、ssh-keygen コマンドによって生成された SSH 秘密キーがサポートされています。
    - 現在、Azure Migrate では、パスフレーズベースの SSH キーはサポートされていません。 パスフレーズなしで SSH キーを使用してください。
    - 現在、Azure Migrate では、PuTTY によって生成された SSH 秘密キー ファイルはサポートされていません。
    - Azure Migrate では、次に示すように、OpenSSH 形式の SSH 秘密キー ファイルがサポートされています。
    
    ![SSH 秘密キーのサポートされている形式](./media/tutorial-discover-physical/key-format.png)
1. 複数の資格情報を一度に追加するには、 **[さらに追加]** をクリックして資格情報を保存して追加します。 物理サーバーの検出では、複数の資格情報がサポートされています。
1. **[Step 2:Provide physical or virtual server details]\(ステップ 2:物理サーバーまたは仮想サーバーの詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、サーバーの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** と、サーバーに接続するための資格情報のフレンドリ名を指定します。
1. 一度に **1 つの項目を追加** するか、一括で **複数の項目を追加** することができます。 また、 **[Import CSV]\(CSV のインポート\)** を使用して、サーバーの詳細を指定することもできます。

    ![検出ソースを追加するための選択](./media/tutorial-assess-physical/add-discovery-source-physical.png)

    - **[Add single item]\(1 つの項目を追加\)** を選択した場合は、OS のタイプ、資格情報のフレンドリ名を指定し、サーバーの **IP アドレスまたは FQDN** を追加して、 **[保存]** をクリックします。
    - **[複数の項目を追加]** を選択した場合は、テキスト ボックスでサーバーの **[IP アドレスまたは FQDN]** を資格情報のフレンドリ名と共に指定することで、一度に複数のレコードを追加できます。 追加したレコードを確認し、 **[保存]** をクリックします。
    - **[Import CSV]\(CSV のインポート\)** _(既定で選択)_ を選択した場合は、CSV テンプレート ファイルをダウンロードし、そのファイルにサーバーの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** および資格情報のフレンドリ名を入力します。 その後、ファイルをアプライアンスにインポートし、ファイル内のレコードを **[Verify]\(検証\)** して、 **[保存]** をクリックします。

1. [保存] をクリックすると、アプライアンスによって追加されたサーバーへの接続が検証され、各サーバーの **[Validation status]\(検証状態\)** が表に表示されます。
    - サーバーの検証に失敗した場合は、表の [状態] 列にある **[Validation failed]\(検証に失敗しました\)** をクリックしてエラーを確認します。 問題を修正し、もう一度検証してください。
    - サーバーを削除するには、 **[削除]** をクリックします。
1. 検出を開始する前に、サーバーへの接続はいつでも **再検証** できます。
1. 正常に検証されたサーバーの検出を開始するには **[Start discovery]\(検出の開始\)** をクリックします。 検出が正常に開始されたら、各サーバーに対する検出の状態を表で確認できます。


これで検出が開始されます。 検出されたサーバーのメタデータが Azure portal に表示されるまでにサーバーあたり約 2 分かかります。

## <a name="verify-servers-in-the-portal"></a>ポータルでサーバーを確認する

検出の完了後、サーバーがポータルに表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - Windows、Linux および SQL Server]**  >  **[Azure Migrate: Discovery and Assessment]\(Azure Migrate: 検出および評価\)** ページで、 **[検出済みサーバー]** の数が表示されているアイコンをクリックします。


## <a name="next-steps"></a>次のステップ

Azure Migrate: Discovery and Assessment で[物理サーバーの評価](tutorial-assess-physical.md)を試します。
