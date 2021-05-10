---
title: VMware 向け Azure Migrate アプライアンスを設定する
description: Azure Migrate アプライアンスを設定して VMware 環境でサーバーを評価して移行する方法について説明します。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: 685d7f0a0aaab2f38967e0eb6c32c3fb4067dbe3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612825"
---
# <a name="set-up-an-appliance-for-servers-in-vmware-environment"></a>VMware 環境でサーバーのアプライアンスを設定する

[Azure Migrate:Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) ツールによる評価と [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用したエージェントレス移行を行うには、この記事に沿って Azure Migrate アプライアンスを設定してください。

[Azure Migrate アプライアンス](migrate-appliance.md)は、Azure Migrate:Discovery and Assessment ツールと Server Migration ツールによって使用される軽量のアプライアンスです。vCenter Server で実行されているサーバーを検出し、サーバーの構成およびパフォーマンス メタデータを Azure に送信し、エージェントレスの移行によってサーバーのレプリケーションを行います。

アプライアンスは、次の 2 とおりの方法でデプロイできます。

- ダウンロードした OVA テンプレートを使用して、vCenter Server にサーバーを作成します。 この記事で説明しているのは、こちらの方法です。
- PowerShell インストーラー スクリプトを使用して、既存のサーバーにアプライアンスを設定します。 OVA テンプレートを使用できない場合、あるいは Azure Government をご利用の場合は、[こちらの方法](deploy-appliance-script.md)を使用してください。

アプライアンスの作成後、Azure Migrate: Discovery and Assessment に接続できることを確認し、プロジェクトに登録し、検出を開始するようにアプライアンスを構成します。

## <a name="deploy-with-ova"></a>OVA を使用してデプロイする

OVA テンプレートを使用してアプライアンスを設定するには:

1. ポータルで、アプライアンス名を指定してプロジェクト キーを生成します。
1. OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。 OVA がセキュリティで保護されていることを確認します。
1. OVA ファイルからアプライアンス VM を作成し、それが Azure Migrate に接続できることを確認します。
1. 初回のアプライアンス構成を行い、プロジェクト キーを使用してプロジェクトに登録します。

### <a name="1-generate-the-project-key"></a>1.プロジェクト キーを生成する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Discovery and Assessment]** で、 **[検出]** を選択します。
2. **[Discover Servers]\(サーバーの検出\)**  >  **[お使いのサーバーは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
3. **[1: プロジェクト キーを生成します]** で、VMware 環境でサーバーの検出用に設定する Azure Migrate アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
1. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [検出] ページを閉じないでください。
1. Azure リソースが正常に作成されると、プロジェクト キー** が生成されます。
1. このキーはアプライアンスを設定する際、登録を完了するために必要なので、コピーしておきます。

### <a name="2-download-the-ova-template"></a>2. OVA テンプレートをダウンロードする

**[2:Azure Migrate アプライアンスをダウンロードする]** で、.OVA ファイルを選択し、 **[ダウンロード]** をクリックします。

### <a name="verify-security"></a>セキュリティを確認する

OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. OVA ファイルのハッシュを生成するには、次のコマンドを実行します。
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   使用例: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    - Azure パブリック クラウドの場合:
    
        **アルゴリズム** | **ダウンロード** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

### <a name="3-create-the-appliance-server"></a>3. アプライアンス サーバーを作成する

ダウンロードしたファイルをインポートし、VMware 環境にサーバーを作成します

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。
2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、OVA ファイルの場所を指定します。
3. **[名前]** と **[場所]** で、サーバーのフレンドリ名を指定します。 サーバーがホストされるインベントリ オブジェクトを選択します。
5. **[Host/Cluster]\(ホスト/クラスター\)** で、サーバーが実行されるホストまたはクラスターを指定します。
6. **[ストレージ]** で、サーバーの保存先を指定します。
7. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
8. **[ネットワーク マッピング]** で、サーバーの接続先となるネットワークを指定します。 そのネットワークには、Azure Migrate にメタデータを送信するためのインターネット接続が必要です。
9. 設定を再確認したら、 **[Finish]\(完了\)** をクリックします。

### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls)および[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンス サーバーから接続できることを確認します。

### <a name="4-configure-the-appliance"></a>4. アプライアンスを構成する

アプライアンスを初めて設定します。

> [!NOTE]
> ダウンロードした OVA でなく、[**PowerShell スクリプト**](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、この手順の最初の 2 つは関係ありません。

1. vSphere クライアント コンソールで、サーバーを右クリックしてから、 **[コンソールを開く]** を選択します。
2. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
3. アプライアンス サーバーに接続できる任意のサーバー上でブラウザーを開き、アプライアンス構成マネージャーの URL (`https://appliance name or IP address: 44368`) を開きます。

   または、構成マネージャーのショートカットを選択し、アプライアンス サーバーのデスクトップから構成マネージャーを開くこともできます。
1. **ライセンス条項** に同意し、サード パーティの情報を確認します。
1. 構成マネージャー > **[前提条件のセットアップ]** で、以下を実行します。
   - **接続**:サーバーがインターネットにアクセスできることが、アプライアンスによって確認されます。 サーバーでプロキシを使用する場合は、次の操作を行います。
     - **[プロキシの設定]** をクリックし、`http://ProxyIPAddress` または `http://ProxyFQDN` の形式のプロキシ アドレス、およびリッスン ポートを指定します。
     - プロキシで認証が必要な場合は、資格情報を指定します。
     - サポートされるのは HTTP プロキシのみです。
     - プロキシの詳細を追加した場合、またはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
   - **時刻同期**:検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
   - **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、アプライアンス サーバーで実行されているサービスの状態とバージョンを確認できます。
   - **Install VDDK\(VDDK のインストール\)** :アプライアンスでは、VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが確認されます。 インストールされていない場合は、**インストール手順** に記載されているとおりに、VMware から VDDK 6.7 をダウンロードし、ダウンロードした zip コンテンツをアプライアンス上の指定された場所に抽出します。

     Azure Migrate Server Migration では、Azure への移行時に VDDK を使用してサーバーがレプリケートされます。 
1. 必要に応じて、アプライアンス構成中ならいつでも、**前提条件の再実行** を行って、アプライアンスがすべての前提条件を満たしているかどうかを確認できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="アプライアンス構成マネージャーのパネル 1":::

## <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーした **プロジェクト キー** を貼り付けます。 このキーがない場合は、 **[Discovery and assessment]\(検出および評価\) > [Discover]\(検出\) > [既存のアプライアンスの管理]** に移動して、キーの生成時に指定したアプライアンス名を選択して、対応するキーをコピーします。
1. Azure で認証するには、デバイス コードが必要です。 **[ログイン]** をクリックすると、次に示すように、デバイス コードを含むモーダルが開きます。

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="デバイス コードを示すモーダル":::

1. **[Copy code & Login]\(コードのコピーとログイン\)** をクリックしてデバイス コードをコピーし、新しいブラウザー タブで Azure ログイン プロンプトを開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、デバイス コードを貼り付け、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
3. ログインせずにログイン タブを誤って閉じた場合は、アプライアンス構成マネージャーのブラウザー タブを最新の情報に更新して、[ログイン] ボタンを再度有効にする必要があります。
1. 正常にログインしたら、アプライアンス構成マネージャーで前のタブに戻ります。
1. ログに使用した Azure ユーザー アカウントに、キーの生成時に作成した Azure リソースに対する正しいアクセス許可が付与されている場合、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** をクリックすることで登録の詳細を確認できるようになります。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="アプライアンス構成マネージャーのパネル 2":::

## <a name="start-continuous-discovery"></a>継続的な検出を開始する

### <a name="provide-vcenter-server-details"></a>vCenter Server の詳細を指定する

サーバーの構成およびパフォーマンス データを検出するには、アプライアンスを vCenter Server に接続する必要があります。

1. **[Step 1: Provide vCenter Server credentials]\(ステップ 1: vCenter Server の資格情報を指定する\)** で、 **[資格情報の追加]** をクリックして資格情報のフレンドリ名を指定し、vCenter Server で実行されているサーバーの検出にアプライアンスによって使用される vCenter Server アカウントの **[ユーザー名]** と **[パスワード]** を追加します。
    - この記事の前述のとおり、必要なアクセス許可があるアカウントを設定する必要があります。
    - 検出のスコープを特定の VMware オブジェクト (vCenter Server データセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々のサーバー) に設定する場合は、[こちらの記事](set-discovery-scope.md)の手順を参照し、Azure Migrate によって使用されるアカウントを制限してください。
1. **[Step 2: Provide vCenter Server details]\(ステップ 2: VCenter Server の詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、ドロップダウンから資格情報のフレンドリ名を選択し、vCenter Server の **IP アドレスまたは FQDN** を指定します。 **[ポート]** を既定 (443) のまま使用することも、vCenter Server でリッスンするカスタム ポートを指定して **[保存]** をクリックしることもできます。
1. **[保存]** をクリックすると、アプライアンスにより、指定された資格情報を使用して vCenter Server への接続の検証が試行され、vCenter Server の IP アドレスまたは FQDN に対して **検証状態** がテーブルに示されます。
1. 検出を開始する前に、vCenter Server への接続はいつでも **再検証** できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server の詳細に関するアプライアンス構成マネージャーのパネル":::

### <a name="provide-server-credentials"></a>サーバーの資格情報を指定する

**[Step 3: Provide server credentials to perform software inventory, agentless dependency analysis and discovery of SQL Server instances and databases]\(ステップ 3: サーバーの資格情報を指定して、ソフトウェア インベントリ、エージェントレスの依存関係分析、SQL Server インスタンスとデータベースの検出を実行する\)** では、複数のサーバー資格情報を指定するか、これらの機能を利用しない場合は、ステップをスキップして vCenter Server 検出を続行するかを選択できます。 この意図は後からいつでも変更できます。

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="サーバーの詳細に関するアプライアンス構成マネージャーのパネル 3":::


これらの機能を使用する場合、次の手順を実行して、サーバーの資格情報を提供することができます。 アプライアンスは自動的に資格情報をサーバーにマップして、検出機能の実行を試みます。

- サーバーの資格情報を追加するには、 **[資格情報の追加]** ボタンをクリックします。 これにより、モーダルが開き、ドロップダウンから **[資格情報の種類]** を選択できます。
- ドメイン、Windows (ドメイン以外)、Linux (ドメイン以外) または SQL Server の認証資格情報を指定できます。 資格情報の指定方法とそれらの処理方法の詳細については、[こちら](add-server-credentials.md)を参照してください。
- 資格情報の種類ごとに、資格情報のフレンドリ名を指定し、 **[ユーザー名]** と **[パスワード]** を追加して、 **[保存]** をクリックする必要があります。
- ドメイン資格情報を選択する場合は、ドメインの FQDN も指定する必要があります。 この FQDN は、そのドメインの Active Directory で資格情報の信頼性を検証するために必要です。
- インストールされているアプリケーションの検出、エージェントレスの依存関係分析または SQL Server インスタンスとデータベースの検出のために、アカウントに[必要なアクセス許可](add-server-credentials.md#required-permissions)があることを確認します。
- 複数の資格情報を一度に追加するには、 **[さらに追加]** をクリックして資格情報を保存して追加します。
- **[保存]** または **[さらに追加]** をクリックすると、アプライアンスによって、ドメインの Active Directory でドメインの資格情報の信頼性が検証されます。 これは、アプライアンスによって複数回反復処理され、それぞれのサーバーに資格情報がマップされるときにアカウントのロックアウトを回避するために行われます。
- 資格情報テーブル内のすべてのドメイン資格情報の **検証状態** を確認できます。 ドメイン資格情報のみが検証されます。
- 検証に失敗した場合は、 **[失敗]** 状態をクリックして発生したエラーを確認し、問題を修正した後に **[資格情報の再検証]** をクリックして、失敗と示されたドメイン資格情報をもう一度検証できます。
    :::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="複数の資格情報を提供するためのアプライアンス構成マネージャーのパネル 3":::

### <a name="start-discovery"></a>検出を開始する

1. vCenter Server の検出を開始するには、 **[検出の開始]** をクリックします。 検出が正常に開始された後、vCenter Server の IP アドレスまたは FQDN に対する検出状態をソース テーブルで確認できます。
1. サーバーの資格情報を指定した場合は、vCenter Server の検出が完了した後に、ソフトウェア インベントリ (インストールされているアプリケーションの検出) が自動的に開始されます。 ソフトウェア インベントリは 12 時間ごとに実行されます。
1. [ソフトウェア インベントリ](how-to-discover-applications.md)では、サーバー上で実行されている SQL Server インスタンスを特定し、その情報を使用して、アプライアンスに指定された Windows 認証または SQL Server 認証資格情報でインスタンスへの接続がそのアプライアンスによって試行され、SQL Server データベースとそれらのプロパティに関するデータが収集されます。 SQL 検出は 24 時間ごとに実行されます。
1. ソフトウェア インベントリ時には、追加されたサーバーの資格情報がサーバーに対して反復処理され、エージェントレスの依存関係の分析のために検証されます。 サーバーに対するエージェントレスの依存関係の分析は、ポータルから有効にすることができます。 検証に成功したサーバーのみを選択し、エージェントレスの依存関係の分析を有効にすることができます。

検出は次のように行われます。

- 検出されたサーバーのインベントリがポータルに表示されるまでに、約 15 分かかります。
- インストールされているアプリケーションの検出には時間がかかることがあります。 期間は検出されたサーバーの数によって異なります。 500 台のサーバーがある場合、検出されたインベントリが Azure Migrate ポータルに表示されるまでに約 1 時間かかります。
- サーバーの検出が完了した後、ポータルからサーバーでエージェントレスの依存関係分析を有効にすることができます。
- SQL Server インスタンスおよびデータベース データは、検出開始から 24 時間以内にポータルに表示され始めます。

## <a name="next-steps"></a>次のステップ

[VMware の評価](./tutorial-assess-vmware-azure-vm.md)と[エージェントレスの移行](tutorial-migrate-vmware.md)に関するチュートリアルを確認します。
