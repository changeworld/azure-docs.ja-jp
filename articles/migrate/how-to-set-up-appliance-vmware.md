---
title: VMware VM 用に Azure Migrate Server Assessment/Migration のアプライアンスを設定する | Microsoft Docs
description: Azure Migrate Server Assessment/Migration を使用する VMware VM の検出、評価、エージェントレス移行のためにアプライアンスを設定する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810198"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>VMware VM のアプライアンスを設定する

この記事では、Azure Migrate Server Assessment ツールで VMware VM を評価する場合、または Azure Migrate Server Migration ツールを使用してエージェントレス移行で VMware VM を Azure に移行する場合に Azure Migrate アプライアンスを設定する方法について説明します。

VMware VM アプライアンスは、次の操作を行うために Azure Migrate Server Assessment/Migration によって使用される軽量アプライアンスです。

- オンプレミスの VMware VM を検出します。
- 検出された VM のメタデータとパフォーマンス データを Azure Migrate Server Assessment/Migration に送信します。

Azure Migrate アプライアンスに関する[詳細を確認](migrate-appliance.md)します。


## <a name="appliance-deployment-steps"></a>アプライアンスのデプロイ手順

アプライアンスを設定するには、次のようにします。
- OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。
- アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。 
- アプライアンスを初めて構成し、Azure Migrate プロジェクトに登録します。

## <a name="download-the-ova-template"></a>OVA テンプレートをダウンロードする

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** をクリックします。
3. **[ダウンロード]** をクリックして、.OVA テンプレート ファイルをダウンロードします。



### <a name="verify-security"></a>セキュリティを確認する

OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、OVA のハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. アプライアンス バージョン 1.0.0.5 の場合、生成されたハッシュはこれらの設定と一致する必要があります。 

  **アルゴリズム** | **ハッシュ値**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。
2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、OVA ファイルの場所を指定します。
3. **[名前]** と **[場所]** で、VM のフレンドリ名を指定します。 VM がホストされるインベントリ オブジェクトを選択します。
5. **[Host/Cluster]\(ホスト/クラスター\)** で、VM が実行されるホストまたはクラスターを指定します。
6. **[ストレージ]** で、VM の保存先を指定します。
7. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
8. **[ネットワーク マッピング]** で、VM の接続先となるネットワークを指定します。 そのネットワークには、Azure Migrate Server Assessment にメタデータを送信するためのインターネット接続が必要です。
9. 設定を再確認したら、 **[Finish]\(完了\)** をクリックします。


### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

アプライアンス VM が [Azure URL](migrate-support-matrix-vmware.md#assessment-url-access-requirements) に接続できることを確認します。


## <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

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


## <a name="start-continuous-discovery"></a>継続的な検出を開始する

次に、アプライアンスから vCenter Server に接続し、VM の検出を開始します。 

1. **[vCenter Server の詳細を指定する]** で、vCenter Server の名前 (FQDN) または IP アドレスを指定します。 既定のポートをそのまま使用することも、vCenter Server でリッスンするカスタム ポートを指定することもできます。
2. **[ユーザー名]** と **[パスワード]** で、アプライアンスで vCenter Server の VM の検出に使用される読み取り専用の資格情報を指定します。 [検出に必要なアクセス許可](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions)がアカウントにあることを確認します。
3. **[接続の検証]** をクリックし、アプライアンスが vCenter Server に接続できることを確認します。
4. 接続が確立された後、 **[保存して検出を開始]** をクリックします。


これで検出が開始されます。 検出された VM のメタデータがポータルに表示されるまでに、約 15 分かかります。 


## <a name="next-steps"></a>次の手順

[VMware の評価](tutorial-assess-vmware.md)と[エージェントレスの移行](tutorial-migrate-vmware.md)に関するチュートリアルを確認します。
