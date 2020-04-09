---
title: VMware 向け Azure Migrate アプライアンスを設定する
description: Azure Migrate アプライアンスを設定して VMware VM を評価し移行する方法について説明します。
ms.topic: article
ms.date: 03/23/2020
ms.openlocfilehash: 7a7d0007d2824abc781411f9529f9fa4ac89e55c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336788"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>VMware VM のアプライアンスを設定する

この記事では、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールによる評価のため、および [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用したエージェントレス移行のために、Azure Migrate アプライアンスを設定する方法について説明します。

[Azure Migrate アプライアンス](migrate-appliance.md) は、エージェントレス移行時にオンプレミスの VMware VM の検出、VM のメタデータ/パフォーマンス データの Azure への送信、VMware VM のレプリケーションを行うために、Azure Migrate:Server Assessment および Server Migration によって使用される軽量のアプライアンスです。

ダウンロードした OVA テンプレートを使用するか、または PowerShell インストール スクリプトを使用して、VMware VM 評価用に Azure Migrate アプライアンスを設定できます。 この記事では、OVA テンプレートを使用してアプライアンスを設定する方法について説明します。 スクリプトを使用してアプライアンスを設定する場合は、[この記事](deploy-appliance-script.md)の手順に従ってください。


## <a name="appliance-deployment-ova"></a>アプライアンスのデプロイ (OVA)

OVA テンプレートを使用してアプライアンスを設定するには:
- OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。
- アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。
- アプライアンスを初めて構成し、Azure Migrate プロジェクトに登録します。

## <a name="download-the-ova-template"></a>OVA テンプレートをダウンロードする

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** をクリックします。
3. **[ダウンロード]** をクリックして、.OVA テンプレート ファイルをダウンロードします。

  ![OVA ファイルをダウンロードするための選択](./media/tutorial-assess-vmware/download-ova.png)

### <a name="verify-security"></a>セキュリティを確認する

OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、OVA のハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. アプライアンスの最新のバージョンでは、生成されたハッシュがこれらの[設定](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware#verify-security)と一致する必要があります。



## <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。
![OVF テンプレートをデプロイするためのメニュー コマンド](./media/tutorial-assess-vmware/deploy-ovf.png)

2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、OVA ファイルの場所を指定します。
3. **[名前]** と **[場所]** で、VM のフレンドリ名を指定します。 VM がホストされるインベントリ オブジェクトを選択します。
5. **[Host/Cluster]\(ホスト/クラスター\)** で、VM が実行されるホストまたはクラスターを指定します。
6. **[ストレージ]** で、VM の保存先を指定します。
7. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
8. **[ネットワーク マッピング]** で、VM の接続先となるネットワークを指定します。 そのネットワークには、Azure Migrate Server Assessment にメタデータを送信するためのインターネット接続が必要です。
9. 設定を再確認したら、 **[Finish]\(完了\)** をクリックします。


### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

アプライアンス VM が [Azure URL](migrate-appliance.md#url-access) に接続できることを確認します。


## <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。 OVA テンプレートの代わりにスクリプトを使用してアプライアンスをデプロイする場合、この手順の最初の 2 つの操作は必要ありません。

1. vSphere Client コンソールで、[VM] を右クリックして **[Open Console]\(コンソールを開く\)** を選択します。
2. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
3. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリ ショートカットをクリックして、アプライアンス デスクトップからアプリを開くこともできます。
4. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
    - **ライセンス**:ライセンス条項に同意し、サード パーティの情報を確認します。
    - **接続**:VM でインターネットにアクセスできることが、アプリによって確認されます。 VM でプロキシを使用する場合:
        - **[プロキシの設定]** をクリックし、 http://ProxyIPAddress または http://ProxyFQDN の形式で、プロキシ アドレスとリスニング ポートを指定します。
        - プロキシで認証が必要な場合は、資格情報を指定します。
        - サポートされるのは HTTP プロキシのみです。
    - **時刻同期**:時刻が確認されます。 検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
    - **更新プログラムのインストール**:Azure Migrate では、最新のアプライアンス更新プログラムがインストールされていることが確認されます。
    - **Install VDDK\(VDDK のインストール\)** :Azure Migrate では、VMWare vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが確認されます。
        - Azure Migrate では、Azure への移行中に VDDK を使用してマシンがレプリケートされます。
        - VMware から VDDK 6.7 をダウンロードし、ダウンロードした zip コンテンツをアプライアンス上の指定された場所に抽出します。

## <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. **[ログイン]** をクリックします。 表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
2. 新しいタブで、自分の Azure 資格情報を使用してサインインします。
    - 自分のユーザー名とパスワードを使用してサインインします。
    - PIN を使用したサインインはサポートされていません。
3. 正常にサインインした後、Web アプリに戻ります。
2. Azure Migrate プロジェクトが作成されたサブスクリプションを選択します。 その後、プロジェクトを選択します。
3. アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
4. **[登録]** をクリックします。


## <a name="start-continuous-discovery-by-providing-vcenter-server-and-vm-credential"></a>vCenter Server と VM の資格情報を指定して継続的な検出を開始する

VM の構成データとパフォーマンス データを検出するには、アプライアンスを vCenter Server に接続する必要があります。

### <a name="specify-vcenter-server-details"></a>vCenter Server の詳細を指定する
1. **[vCenter Server の詳細を指定する]** で、vCenter Server の名前 (FQDN) または IP アドレスを指定します。 既定のポートをそのまま使用することも、vCenter Server でリッスンするカスタム ポートを指定することもできます。
2. **[ユーザー名]** と **[パスワード]** で、アプライアンスで vCenter Server の VM の検出に使用される読み取り専用の資格情報を指定します。 vCenter アカウントへのアクセスを制限することで、検出のスコープを指定できます。 [詳細については、こちらを参照してください](set-discovery-scope.md)。
3. **[接続の検証]** をクリックし、アプライアンスが vCenter Server に接続できることを確認します。

### <a name="specify-vm-credentials"></a>VM の資格情報を指定する
アプリケーション、ロール、機能を検出し、VM 間の依存関係を視覚化するには、VMware VM にアクセスできる VM 資格情報を指定できます。 Windows VM 用に資格情報を 1 つ、Linux VM 用に資格情報を 1 つ追加できます。 必要なアクセス特権の詳細については、[こちらを参照](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware)してください。

> [!NOTE]
> この入力は省略可能ですが、アプリケーションの検出とエージェントレスな依存関係の視覚化を有効にする際に必要になります。

1. **[VM でのアプリケーションと依存関係の検出]** で、 **[資格情報の追加]** をクリックします。
2. **[オペレーティング システム]** を選択します。
3. 資格情報のフレンドリ名を指定します。
4. **[ユーザー名]** と **[パスワード]** に、その VM に対するゲスト アクセス権を少なくとも持っているアカウントを指定します。
5. **[追加]** をクリックします。

vCenter Server の資格情報と VM の資格情報 (オプション) を指定したら、 **[保存して検出を開始]** をクリックすると、オンプレミス環境の検出が開始されます。

検出された VM のメタデータがポータルに表示されるまでに、約 15 分かかります。 インストールされているアプリケーション、ロール、機能の検出にはいくらか時間がかかります。所要時間は、検出する VM の数によって異なります。 500 台の VM がある場合、アプリケーション インベントリが Azure Migrate ポータルに表示されるまでに約 1 時間かかります。

## <a name="next-steps"></a>次のステップ

[VMware の評価](tutorial-assess-vmware.md)と[エージェントレスの移行](tutorial-migrate-vmware.md)に関するチュートリアルを確認します。
