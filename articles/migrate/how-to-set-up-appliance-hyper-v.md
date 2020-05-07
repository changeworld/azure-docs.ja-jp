---
title: Hyper-V 向け Azure Migrate アプライアンスを設定する
description: Azure Migrate アプライアンスを設定して Hyper-V VM を評価し移行する方法について説明します。
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 77c13a3a8c87d116bd0863324d28669185c53c84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81538292"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Hyper-V VM のアプライアンスを設定する

この記事を読み、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールによる Hyper-V VM の評価のために、Azure Migrate アプライアンスを設定してください。

[Azure Migrate アプライアンス](migrate-appliance.md)は、Azure Migrate:Server Assessment/Migration によって、オンプレミス Hyper-V VM を検出し、VM メタデータ/パフォーマンス データを Azure に送信するために使用される軽量アプライアンスです。

アプライアンスは、次の 2 とおりの方法でデプロイできます。

- ダウンロードした VHD を使用して Hyper-V VM 上に設定できます。 この記事で説明しているのは、こちらの方法です。
- PowerShell インストーラー スクリプトを使用して Hyper-V VM 上または物理マシン上に設定します。 VHD を使用して VM を設定できない場合や、Azure Government をご利用の場合は、[この方法](deploy-appliance-script.md)を使用してください。

アプライアンスの作成後、Azure Migrate:Server Assessment に接続できることを確認し、最初の構成を行い、Azure Migrate プロジェクトに登録します。

## <a name="appliance-deployment-vhd"></a>アプライアンスのデプロイ (VHD)

VHD テンプレートを使用してアプライアンスを設定するには:

- Azure portal から圧縮された Hyper-V VHD をダウンロードします。
- アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。
- アプライアンスを初めて構成し、Azure Migrate プロジェクトに登録します。

## <a name="download-the-vhd"></a>VHD をダウンロードする

アプライアンス用の圧縮された VHD テンプレートをダウンロードします。

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。Hyper-V を使用します]** をクリックします。
3. **[ダウンロード]** をクリックして、VHD ファイルをダウンロードします。

    ![VM をダウンロードする](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、VHD のハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  アプライアンス バージョン 2.19.11.12 の場合は、生成されたハッシュがこれらの[設定](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v#verify-security)と一致する必要があります。




## <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. アプライアンス VM をホストする Hyper-V ホスト上のフォルダーに、圧縮された VHD ファイルを抽出します。 3 つのフォルダーが抽出されます。
2. Hyper-V マネージャーを開きます。 **[アクション]** で **[仮想マシンのインポート]** をクリックします。

    ![VHD をデプロイする](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. 仮想マシンのインポート ウィザードの **[開始する前に]** で、 **[次へ]** をクリックします。
3. **[フォルダーの検索]** で、抽出された VHD が含まれているフォルダーを指定します。 続けて、 **[次へ]** をクリックします。
1. **[仮想マシンを選択します]** で、 **[次へ]** をクリックします。
2. **[インポートの種類の選択]** で、 **[仮想マシンをコピーする (新しい一意な ID を作成する)]** をクリックします。 続けて、 **[次へ]** をクリックします。
3. **[移動先の選択]** は、既定の設定のままにします。 **[次へ]** をクリックします。
4. **[保存フォルダー]** は、既定の設定のままにします。 **[次へ]** をクリックします。
5. **[ネットワークの選択]** で、VM によって使用される仮想スイッチを指定します。 このスイッチには、Azure にデータを送信するためのインターネット接続が必要です。
6. **[概要]** で、設定を確認します。 **[完了]** をクリックします。
7. Hyper-V マネージャーの **[仮想マシン]** で、VM を起動します。


### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンス VM から接続できることを確認します。

## <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。 VHD でなく、スクリプトを使用してアプライアンスをデプロイする場合、この手順の最初の 2 つの操作は必要ありません。

1. Hyper-V マネージャーの **[仮想マシン]** で、VM を右クリックして **[接続]** を選択します。
2. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
3. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリ ショートカットをクリックして、アプライアンス デスクトップからアプリを開くこともできます。
1. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
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


### <a name="delegate-credentials-for-smb-vhds"></a>SMB VHD の資格情報を委任する

SMB 上で VHD を実行している場合は、アプライアンスから Hyper-V ホストへの資格情報の委任を有効にする必要があります。 アプライアンスからこれを行うには、次のようにします。

1. アプライアンス VM 上で、このコマンドを実行します。 HyperVHost1/HyperVHost2 は、ホスト名の例です。

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. または、アプライアンス上のローカル グループ ポリシー エディターでこれを行います。
    - **[ローカル コンピューター ポリシー]**  >  **[コンピューターの構成]** で、 **[管理用テンプレート]**  >  **[システム]**  >  **[資格情報の委任]** の順にクリックします。
    - **[新しい資格情報の委任を許可する]** をダブルクリックし、 **[有効]** を選択します。
    - **[オプション]** で **[表示]** をクリックし、検出したい各 Hyper-V ホストを、プレフィックスとして **wsman/** を付けて、一覧に追加します。
    - **[資格情報の委任]** で **[NTLM のみのサーバー認証で新しい資格情報の委任を許可する]** をダブルクリックします。 もう一度、検出したい各 Hyper-V ホストを、プレフィックスとして **wsman/** を付けて、一覧に追加します。

## <a name="start-continuous-discovery"></a>継続的な検出を開始する

アプライアンスから Hyper-V ホストまたはクラスターに接続し、VM の検出を開始します。

1. **[ユーザー名]** と **[パスワード]** に、VM を検出するためにアプライアンスによって使用されるアカウント資格情報を指定します。 資格情報のフレンドリ名を指定し、 **[詳細の保存]** をクリックします。
2. **[ホストの追加]** をクリックし、Hyper-V のホスト/クラスターの詳細を指定します。
3. **[検証]** をクリックします。 検証後、各ホスト/クラスター上で検出できる VM の数が表示されます。
    - ホストで検証が失敗した場合は、 **[状態]** 列のアイコンをポイントしてエラーを確認します。 問題を修正し、もう一度検証します。
    - ホストまたはクラスターを削除するには、 **[削除]** を選択します。
    - クラスターから特定のホストを削除することはできません。 削除できるのは、クラスター全体のみです。
    - クラスター内の特定のホストに問題がある場合でも、クラスターを追加できます。
4. 検証後、 **[保存して検出を開始]** をクリックして、検出プロセスを開始します。

これで検出が開始されます。 検出された VM のメタデータが Azure portal に表示されるまでに、約 15 分かかります。

## <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

検出の完了後、VM がポータルに表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、 **[検出済みサーバー]** の数を表示するアイコンをクリックします。


## <a name="next-steps"></a>次のステップ

Azure Migrate Server Assessment による [Hyper-V 評価](tutorial-assess-hyper-v.md)を試します。
