---
title: VMware 環境でのサーバー評価のために Azure Migrate アプライアンスを設定する
description: Azure Migrate アプライアンスを設定して VMware 環境でサーバーを評価して移行する方法について説明します。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/12/2021
ms.openlocfilehash: 1082b63bc6f12531d502e75c1c096639b0eab00b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719276"
---
# <a name="set-up-an-appliance-for-servers-in-a-vmware-environment"></a>VMware 環境でサーバーのアプライアンスを設定する

この記事では、[Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) ツールを使用して、評価のために Azure Migrate アプライアンスを設定する方法について説明します。

[Azure Migrate アプライアンス](migrate-appliance.md)は、vCenter Server で実行しているサーバーを検出し、サーバー構成とパフォーマンス メタデータを Azure に送信するために Azure Migrate: Discovery and Assessment ツールで使用される軽量のアプライアンスです。

## <a name="set-up-the-appliance"></a>アプライアンスを設定する

Azure Migration アプライアンスは、次の 2 とおりの方法でデプロイできます。

- ダウンロードした OVA テンプレートを使用して、vCenter Server VM にサーバーを作成します。 この記事で説明しているのは、こちらの方法です。
- PowerShell インストーラー スクリプトを使用して、既存のサーバーにアプライアンスを設定します。 OVA テンプレートを使用できない場合、または Azure Government にいる場合は、[PowerShell スクリプトを実行](deploy-appliance-script.md)する必要があります。

アプライアンスの作成後、アプライアンスが Azure Migrate: Discovery and Assessment に接続できることを確認し、アプライアンスをプロジェクトに登録し、検出を開始するようにアプライアンスを構成します。

### <a name="deploy-by-using-an-ova-template"></a>OVA テンプレートを使用してデプロイする

OVA テンプレートを使用してアプライアンスを設定するには、このセクションで詳しく説明されているこれらの手順を完了します。

1. ポータルで、アプライアンス名を指定してプロジェクト キーを生成します。
1. OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。 OVA が安全であることを確認します。
1. OVA ファイルからアプライアンスを作成します。 アプライアンスが Azure Migrate に接続できることを確認します。
1. アプライアンスを初めて構成します。 
1. プロジェクト キーを使用して、プロジェクトにアプライアンスを登録します。

#### <a name="generate-the-project-key"></a>プロジェクト キーを生成する

1. **[移行の目標]** で、 **[サーバー]**  >  **[Azure Migrate: Discovery and Assessment]**  >  **[検出]** の順に選択します。
1. **サーバーの検出** で、 **[お使いのサーバーは仮想化されていますか?]** >  **[はい。VMware vSphere Hypervisor を使用します]** の順に選択します。
1. **[1: プロジェクト キーを生成します]** で、VMware 環境内のサーバーを検出するために設定する Azure Migrate アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
1. 必要な Azure リソースの作成を開始するには、 **[キーの生成]** を選択します。 リソースの作成中は、 **[検出]** ペインを閉じないようにしてください。
1. Azure リソースが正常に作成された後、"*プロジェクト キー*" が生成されます。
1. キーをコピーします。 キーは、アプライアンスの構成時にアプライアンスの登録を完了するために使用します。

#### <a name="download-the-ova-template"></a>OVA テンプレートをダウンロードする

**[2: Azure Migrate アプライアンスをダウンロードする]** で、OVA ファイルを選択してから **[ダウンロード]** を選択します。

##### <a name="verify-security"></a>セキュリティを確認する

OVA ファイルをデプロイする前に、ファイルが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、 **[管理者として実行]** オプションを使用してコマンド プロンプト ウィンドウを開きます。
1. OVA ファイルのハッシュを生成するには、次のコマンドを実行します。
  
    ```bash
    C:\>CertUtil -HashFile <file_location> <hashing_agorithm>
    ```
   
    例: `C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256`

1. 上記のコマンドの結果と[こちら](./tutorial-discover-vmware.md#verify-security)に記載されている値を比較して、最新のハッシュ値を確認します。

#### <a name="create-the-appliance-server"></a>アプライアンス サーバーを作成する

ダウンロードしたファイルをインポートして、VMware 環境にサーバーを作成します。

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順に選択します。
1. OVF テンプレートのデプロイ ウィザードで、 **[ソース]** を選択して OVA ファイルの場所を入力します。
1. **[Name]\(名前\)** にサーバーの名前を入力します。 **[Location]\(場所\)** で、サーバーがホストされるインベントリ オブジェクトを選択します。
1. **[Host/Cluster]\(ホスト/クラスター\)** で、サーバーが実行されるホストまたはクラスターを選択します。
1. **[Storage]\(ストレージ\)** で、サーバーの保存先を指定します。
1. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを選択します。
1. **[Network Mapping]\(ネットワーク マッピング\)** で、サーバーの接続先となるネットワークを選択します。 ネットワークには、Azure Migrate にメタデータを送信するためのインターネット接続が必要です。
1. 設定を確認し、 **[Finish]\(完了\)** を選択します。

#### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック クラウド](migrate-appliance.md#public-cloud-urls)および[政府機関向けクラウド](migrate-appliance.md#government-cloud-urls)の Azure URL にアプライアンス サーバーから接続できることを確認します。

<a name="4-configure-the-appliance"></a>

### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定するには:

> [!NOTE]
> ダウンロードした OVA テンプレートではなく [PowerShell スクリプト](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、最初の 2 つの手順はスキップしてかまいません。

1. vSphere Client で、サーバーを右クリックしてから **[Open Console]\(コンソールを開く\)** を選択します。
1. アプライアンスの言語、タイム ゾーン、パスワードを選択または入力します。
1. アプライアンス サーバーに接続できるいずれかのサーバーでブラウザーを開きます。 次に、アプライアンス構成マネージャーの URL (`https://appliance name or IP address: 44368`) を開きます。

     または、アプライアンス サーバーのデスクトップから構成マネージャーへのショートカットを選択して、構成マネージャーを開くこともできます。
1. ライセンス条項に同意し、サードパーティの情報に目を通します。
1. 構成マネージャーで、 **[前提条件のセットアップ]** を選択し、これらの手順を完了します。
    1. **接続**:サーバーがインターネットにアクセスできることが、アプライアンスによって確認されます。 サーバーでプロキシを使用する場合は、次の操作を行います。
        1. **[プロキシを設定する]** を選択して、プロキシのアドレス (`http://ProxyIPAddress` または `http://ProxyFQDN` の形式。*FQDN* は "*完全修飾ドメイン名*" を指す) とリスニング ポートを指定します。
        1.  プロキシで認証が必要な場合は資格情報を入力します。
        1. プロキシの詳細を追加した場合、あるいはプロキシまたは認証を無効にした場合は、 **[保存]** を選択し、接続をトリガーしてもう一度接続を確認します。

            サポートされるのは HTTP プロキシのみです。
    1. **時刻同期**: 検出が正常に機能するように、アプライアンス上の時刻がインターネットの時刻と同期していることを確認します。
    1. **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了すると、 **[View appliance services]\(アプライアンス サービスを表示\)** を選択して、アプライアンス サーバーで実行されているサービスの状態とバージョンを確認できます。
    1. **Install VDDK (VDDK のインストール)** : VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることがアプライアンスによって確認されます。 VDDK がインストールされていない場合は、VMware から VDDK 6.7 をダウンロードします。 "*インストール手順*" に記載されているとおりに、ダウンロードした zip ファイルの内容をアプライアンス上の指定された場所に抽出します。

        Azure Migrate Server Migration では、Azure への移行時に VDDK を使用してサーバーがレプリケートされます。 
1. アプライアンスの構成中ならいつでも "*前提条件を再実行*" して、アプライアンスがすべての前提条件を満たしているかどうかを確認できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="アプライアンス構成マネージャーでの前提条件の設定方法を示すスクリーンショット。":::

#### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーしたプロジェクト キーを貼り付けます。 キーがない場合は、 **[検出および評価]**  >  **[検出]**  >  **[既存のアプライアンスの管理]** の順に移動します。 プロジェクト キーを生成したときに指定したアプライアンスの名前を選択して、表示されたキーをコピーします。
1. Azure による認証にはデバイス コードが必要です。 **[ログイン]** を選択します。 **[Azure ログインの続行]** で、 **[Copy code & Login]\(コードのコピーとログイン\)** を選択してデバイス コードをコピーし、新しいブラウザー タブで Azure ログイン プロンプトを開きます。プロンプトが表示されるように、ブラウザーでポップアップ ブロックを無効にしておいてください。

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="デバイス コードをコピーしてログインする場所を示すスクリーンショット。":::

1. ブラウザーの新しいタブで、デバイス コードを貼り付け、自分の Azure ユーザー名とパスワードを使用してサインインします。 PIN を使用したサインインはサポートされていません。

    ログインせずにログイン タブを誤って閉じた場合は、アプライアンス構成マネージャーのブラウザー タブを最新の情報に更新して、デバイス コードと **[Copy code & Login]\(コードのコピーとログイン\)** ボタンを表示します。
1. 正常にログインしたら、アプライアンス構成マネージャーが表示されているブラウザー タブに戻ります。 ログインに使用した Azure ユーザー アカウントに、キーの生成時に作成された Azure リソースに対する必要なアクセス許可がある場合は、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** を選択して、登録の詳細を確認できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="アプライアンスが正常に登録されたことを示す [Azure Migrate への登録] ペインのスクリーンショット。":::

## <a name="start-continuous-discovery"></a>継続的な検出を開始する

アプライアンス構成マネージャーでの設定手順を完了し、検出を準備して開始します。

### <a name="provide-vcenter-server-details"></a>vCenter Server の詳細を指定する

サーバーの構成およびパフォーマンス データを検出するには、アプライアンスから vCenter Server に接続する必要があります。

1. **[Step 1: Provide vCenter Server credentials]\(手順 1: vCenter Server の資格情報を指定する\)** で、 **[資格情報の追加]** を選択して資格情報の名前を入力します。 vCenter Server で実行中のサーバーを検出するためにアプライアンスで使用される vCenter Server アカウントのユーザー名とパスワードを追加します。
    - この記事で前に述べたように、必要なアクセス許可を備えたアカウントを設定する必要があります。
    - 検出のスコープを特定の VMware オブジェクト (vCenter Server データセンター、クラスター、ホスト、クラスターまたはホストのフォルダー、あるいは個々のサーバー) に設定する場合は、[検出のスコープを設定](set-discovery-scope.md)する手順を参照し、Azure Migrate によって使用されるアカウントを制限してください。
1. **[Step 2: Provide vCenter Server details]\(手順 2: vCenter Server の詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** を選択して、ドロップダウン リストから資格情報の名前を選択します。 vCenter Server の IP アドレスまたは FQDN を選択します。 ポートを既定値 (443) のままにしておくことも、vCenter Server でリッスンするカスタム ポートを指定することもできます。 **[保存]** を選択します。
1. アプライアンスでは、資格情報を使用して、vCenter Server を実行しているサーバーへの接続の検証を試行します。 資格情報テーブルに vCenter Server の IP アドレスまたは FQDN の検証状態が表示されます。
1. 検出を開始する前に、vCenter Server への接続はいつでも "*再検証*" できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="アプライアンス構成マネージャーで vCenter Server の資格情報と検出ソースを管理する方法を示すスクリーンショット。":::

### <a name="provide-server-credentials"></a>サーバーの資格情報を指定する

「**手順 3: ソフトウェア インベントリ、エージェントレスの依存関係の分析、SQL Server インスタンスとデータベースの検出、VMware 環境での ASP.NET Web アプリの検出を実行するためのサーバー資格情報を指定する**」で、複数のサーバー資格情報を指定できます。 これらのアプライアンス機能を使用しない場合は、この手順をスキップして、vCenter Server の検出に進むことができます。 このオプションはいつでも変更できます。

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="ソフトウェア インベントリ、依存関係分析、S Q L サーバー検出を行うための資格情報を指定する方法を示すスクリーンショット。":::

これらの機能を使用する場合、次の手順を実行して、サーバーの資格情報を指定します。 検出機能を実行するために、サーバーへの資格情報の自動マップがアプライアンスによって試行されます。

サーバーの資格情報を追加するには:

1. **[Add Credentials]\(資格情報の追加\)** を選択します。
1. ドロップダウン メニューで、 **[資格情報の種類]** を選択します。
    
    ドメイン、Windows (ドメイン以外)、Linux (ドメイン以外)、SQL Server の認証資格情報を指定できます。 [資格情報を指定する](add-server-credentials.md)方法とそれらを処理する方法を確認してください。
1. それぞれの種類の資格情報について、以下を入力します。
    * フレンドリ名。
    * "ユーザー名"。
    * パスワード。
    **[保存]** を選択します。

    ドメイン資格情報を使用することにした場合は、ドメインの FQDN の入力も必要です。 FQDN は、そのドメイン内の Active Directory インスタンスと共に資格情報の信頼性を検証するために必要です。
1. 「手順 3: ソフトウェア インベントリ、エージェントレスの依存関係の分析、SQL Server インスタンスとデータベースの検出、ASP.NET Web アプリの検出を実行するためのサーバー資格情報を指定する」で、アカウントに対する[必要なアクセス許可](add-server-credentials.md#required-permissions)を確認します。
1. 一度に複数の資格情報を追加するには、 **[さらに追加]** を選択して資格情報を保存し、さらに資格情報を追加します。
    **[保存]** または **[さらに追加]** を選択すると、アプライアンスによって、認証のためにドメインの Active Directory インスタンスと共にドメイン資格情報が検証されます。 検証は、アカウントのロックアウトを避けるため、追加が行われるたびに実行されます。これは、各サーバーへの資格情報のマップがアプライアンスによって繰り返されるためです。

ドメイン資格情報の検証を確認するには:

構成マネージャーの資格情報テーブルで、ドメイン資格情報の **検証状態** を確認します。 ドメイン資格情報のみ検証されます。

検証が失敗した場合は、**失敗** の状態を選択して検証エラーを確認できます。 問題を修正し、 **[資格情報の再検証]** を選択して資格情報の検証を再試行します。

:::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="複数の資格情報の指定と検証を示すスクリーンショット。":::

### <a name="start-discovery"></a>検出を開始する

vCenter Server の検出を開始するには、「**手順 3: ソフトウェア インベントリ、エージェントレスの依存関係の分析、SQL Server インスタンスとデータベースの検出、VMware 環境での ASP.NET Web アプリの検出を実行するためのサーバー資格情報を指定する**」で、 **[検出の開始]** を選択します。 検出が正常に開始された後、ソース テーブルで vCenter Server の IP アドレスまたは FQDN を見て検出状態を確認できます。

## <a name="how-discovery-works"></a>検出のしくみ

* 検出されたサーバーのインベントリが Azure portal に表示されるには、約 15 分かかります。
* サーバーの資格情報を指定してある場合、vCenter Server を実行しているサーバーの検出が完了したときに、ソフトウェア インベントリ (インストールされているアプリケーションの検出) は自動的に開始されます。 ソフトウェア インベントリは、12 時間ごとに実行されます。
* [ソフトウェア インベントリ](how-to-discover-applications.md)では、サーバー上で実行されている SQL Server インスタンスを特定します。 そこで収集された情報を使用し、アプライアンスで指定された Windows 認証資格情報または SQL Server 認証資格情報を介して、アプライアンスから SQL Server インスタンスへの接続が試行されます。 次に、SQL Server データベースとそのプロパティに関するデータが収集されます。 SQL Server の検出は 24 時間ごとに実行されます。
* [ソフトウェア インベントリ](how-to-discover-applications.md)により、サーバー上で Web サーバーの役割が識別されます。 そこで収集された情報を使用し、アプライアンスで提供された Windows 認証資格情報を介して、アプライアンスから IIS Web サーバーへの接続が試行されます。 次に、Web アプリ上でデータが収集されます。 Web アプリ検出は 24 時間ごとに実行されます。
* インストールされているアプリケーションの検出には、15 分以上かかることがあります。 期間は検出されたサーバーの数によって異なります。 500 台のサーバーでは、検出されたインベントリがポータルの Azure Migrate プロジェクトに表示されるまで約 1 時間かかります。
* ソフトウェア インベントリの間、エージェントレスの依存関係分析のために、追加されたサーバー資格情報がサーバーに対して繰り返され、検証されます。 サーバーの検出が完了すると、ポータルでサーバーに対するエージェントレスの依存関係分析を有効にできます。 エージェントレスの依存関係分析を有効にするよう選択できるのは、検証が成功したサーバーだけです。
* 検出を開始してから 24 時間以内に、SQL Server インスタンス、データベースのデータ、Web アプリのデータがポータルに表示され始めます。

## <a name="next-steps"></a>次のステップ

[VMware の評価](./tutorial-assess-vmware-azure-vm.md)と[エージェントレスの移行](tutorial-migrate-vmware.md)に関するチュートリアルを確認します。