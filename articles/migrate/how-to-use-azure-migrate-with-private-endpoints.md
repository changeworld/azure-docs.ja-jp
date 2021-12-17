---
title: プライベート エンドポイントで Azure Migrate を使用する
description: Azure Migrate のプライベート リンクのサポートを利用して、Azure Private Link を使った検出、評価、移行を行います。
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: c581e89a8252ea8e084a48dc827d75aba90bc670
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500490"
---
# <a name="use-azure-migrate-with-private-endpoints"></a>プライベート エンドポイントで Azure Migrate を使用する

この記事では、[Azure Private Link](../private-link/private-endpoint-overview.md) を使用したプライベート ネットワーク上で、Azure Migrate を使用してサーバーの検出、評価、移行を行う方法について説明します。

Private Link を使用すると、Azure ExpressRoute のプライベート ピアリングまたはサイト間 (S2S) VPN 接続で [Azure Migrate: Discovery and Assessment](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) および [Azure Migrate: Server Migration](./migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用して、Azure Migrate にプライベートかつ安全に接続することができます。

公衆ネットワークを経由することなく Azure Migrate やその他の Azure リソースにアクセスするという組織的な要件がある場合には、プライベート エンドポイントの接続方法をお勧めします。 Private Link を使用し、既にある ExpressRoute プライベート ピアリング回線を使用すると、帯域幅や待機時間の要件を満たしやすくなります。

## <a name="support-requirements"></a>サポートの要件

次の必要なアクセス許可と、サポートされているシナリオとツールを確認します。

### <a name="supported-geographies"></a>サポートされている地域

この機能は、現在サポートされている[パブリック クラウド](./migrate-support-matrix.md#supported-geographies-public-cloud)と[政府機関のクラウド地域でプレビュー段階にあります。](./migrate-support-matrix.md#supported-geographies-azure-government)

### <a name="required-permissions"></a>必要なアクセス許可

サブスクリプションの共同作成者 + ユーザー アクセス管理者または所有者のアクセス許可が必要です。

### <a name="supported-scenarios-and-tools"></a>サポートされるシナリオとツール

**デプロイ** | **詳細** | **ツール**
--- | --- | ---
**検出と評価** | 任意のプラットフォーム上で動作するサーバーの検出と評価を大規模にエージェントレスで行います。 例として、ハイパーバイザー プラットフォーム ([VMware vSphere](./tutorial-discover-vmware.md) や [Microsoft Hyper-V](./tutorial-discover-hyper-v.md) など)、パブリック クラウド ([AWS](./tutorial-discover-aws.md) や [GCP](./tutorial-discover-gcp.md) など)、さらには[ベアメタル サーバー](./tutorial-discover-physical.md)があります。 | Azure Migrate: Discovery and Assessment <br/>
**ソフトウェア インベントリ** | VMware VM 上で実行されているアプリ、ロール、機能を検出します。 | Azure Migrate: Discovery and Assessment
**依存関係の視覚化** | 依存関係分析機能を使用して、サーバー間の依存関係を特定、把握します。 <br/> Azure Migrate のプライベート リンク サポートでは、[エージェントレスの依存関係の視覚化](./how-to-create-group-machine-dependencies-agentless.md)がネイティブでサポートされます。 <br/>[エージェントベースの依存関係の視覚化](./how-to-create-group-machine-dependencies.md)には、インターネット接続が必要となります。 [エージェントベースの依存関係の視覚化にプライベート エンドポイント](../azure-monitor/logs/private-link-security.md)を使用する方法を参照してください。 | Azure Migrate: Discovery and Assessment |
**移行** | [エージェントレスの Hyper-V 移行](./tutorial-migrate-hyper-v.md)を実行するか、エージェントベースの方法を使用して [VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理サーバー](./tutorial-migrate-physical-virtual-machines.md)、[AWS で実行されている VM](./tutorial-migrate-aws-virtual-machines.md)、[GCP で実行されている VM](./tutorial-migrate-gcp-virtual-machines.md)、または別の仮想化プロバイダー上で実行されている VM を移行します。 | Azure Migrate: Server Migration

>[!Note]
> [エージェントレスの VMware 移行](./tutorial-migrate-vmware.md)には、インターネット アクセスまたは ExpressRoute Microsoft ピアリングによる接続が必要です。 [ExpressRoute プライベート ピアリングまたは S2S VPN 接続でプライベート エンドポイントを使用してレプリケーションを実行する](./replicate-using-expressroute.md)方法を参照してください。

#### <a name="other-integrated-tools"></a>その他の統合ツール

その他の移行ツールでは、公衆ネットワーク アクセスが無効になっていると、利用状況データを Azure Migrate プロジェクトにアップロードできない場合があります。 他の Microsoft オファリングまたは外部の[独立系ソフトウェア ベンダー (ISV)](./migrate-services-overview.md#isv-integration) のオファリングからデータを受信するには、すべてのネットワークからのトラフィックを許可するように Azure Migrate プロジェクトを構成する必要があります。

Azure Migrate プロジェクトの公衆ネットワーク アクセスを有効にするには、Azure portal にサインインし、ポータルで **Azure Migrate の [プロパティ]** ページに移動し、 **[いいえ]**  >  **[保存]** を選択します。

![ネットワーク アクセス モードの変更方法を示すスクリーンショット。](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>その他の考慮事項

**考慮事項** | **詳細**
--- | ---
**料金** | 価格情報については、「[Azure ページ BLOB の価格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)」および「[Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)」を参照してください。
**仮想ネットワークの要件** | 選択した仮想ネットワーク内またはそれに接続された仮想ネットワーク内に、ExpressRoute または VPN Gateway のエンドポイントが存在する必要があります。 仮想ネットワークには約 15 個の IP アドレスが必要となる場合があります。

## <a name="create-a-project-with-private-endpoint-connectivity"></a>プライベート エンドポイント接続を使用してプロジェクトを作成する

新しい Azure Migrate プロジェクトを設定するには、「[プロジェクトを作成して管理する](./create-manage-projects.md#create-a-project-for-the-first-time)」を参照してください。

> [!Note]
> 既存の Azure Migrate プロジェクトの接続方法をプライベート エンドポイント接続に変更することはできません。

Azure Migrate プロジェクトのプライベート エンドポイントを作成するには、 **[詳細]** 構成セクションに次の情報を入力します。
1. **[接続方法]** で **[プライベート エンドポイント]** を選択します。
1. **[Disable public endpoint access]\(パブリック エンドポイント アクセスを無効にする\)** では、既定の設定である **[いいえ]** のままにします。 移行ツールによっては、公衆ネットワーク アクセスが無効になっていると、利用状況データを Azure Migrate プロジェクトにアップロードできない場合があります。 [その他の統合ツール](#other-integrated-tools)の詳細を参照してください。
1. **[仮想ネットワークのサブスクリプション]** で、プライベート エンドポイントの仮想ネットワークのサブスクリプションを選択します。
1. **[仮想ネットワーク]** で、プライベート エンドポイントの仮想ネットワークを選択します。 Azure Migrate プロジェクトに接続する必要のある Azure Migrate アプライアンスおよびその他のソフトウェア コンポーネントは、このネットワークまたは接続された仮想ネットワーク上に存在する必要があります。
1. **[サブネット]** で、プライベート エンドポイントのサブネットを選択します。

   ![[プロジェクトの作成] ページの [詳細] セクションを示すスクリーンショット。](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

1. **[作成]** を選択すると移行プロジェクトが作成され、それにプライベート エンドポイントがアタッチされます。 Azure Migrate プロジェクトがデプロイされるまで数分待ちます。 プロジェクトの作成中にこのページを閉じないでください。

## <a name="discover-and-assess-servers-for-migration-by-using-private-link"></a>Private Link を使用して移行するサーバーを検出して評価する

このセクションでは、Azure Migrate アプライアンスを設定する方法について説明します。 次に、これを使用して、移行するサーバーを検出して評価します。

### <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスを設定する

1. **[マシンの検出]**  >  **[お使いのマシンは仮想化されていますか?]** で、サーバーの種類を選択します。
1. **[Azure Migrate プロジェクト キーを生成します]** で、Azure Migrate アプライアンスの名前を入力します。
1. **[キーの生成]** を選択して、必要な Azure リソースを作成します。

    > [!Important]
    > リソースの作成中に **[マシンの検出]** ページを閉じないでください。
    - この手順では、キー コンテナー、ストレージ アカウント、Recovery Services コンテナー (エージェントレス VMware 移行の場合のみ) のほか、いくつかの内部リソースが Azure Migrate によって作成されます。 Azure Migrate によって、各リソースにプライベート エンドポイントがアタッチされます。 プライベート エンドポイントは、プロジェクトの作成時に選択した仮想ネットワーク内に作成されます。
    - プライベート エンドポイントが作成されると、Azure Migrate リソース用の DNS CNAME リソース レコードが、*privatelink* というプレフィックスが付いたサブドメイン内のエイリアスに更新されます。 また、既定では、Azure Migrate によって *privatelink* サブドメインに対応するプライベート DNS ゾーンがリソースの種類ごとに作成され、関連付けられたプライベート エンドポイントの DNS A レコードが挿入されます。 この操作により、ソース ネットワークに存在する Azure Migrate アプライアンスおよびその他のソフトウェア コンポーネントは、プライベート IP アドレス上にある Azure Migrate リソースのエンドポイントに到達できるようになります。
    - また、Azure Migrate は、移行プロジェクトの[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にし、ストレージ アカウントに安全にアクセスするためのアクセス許可をそのマネージド ID に付与します。

1. キーが正常に生成されたら、そのキーの詳細をコピーして、アプライアンスの構成と登録を行います。

#### <a name="download-the-appliance-installer-file"></a>アプライアンスのインストーラー ファイルをダウンロードする

Azure Migrate の検出および評価では、軽量の Azure Migrate アプライアンスを使用します。 このアプライアンスによって、サーバー検出が実行され、サーバーの構成とパフォーマンス メタデータが Azure Migrate に送信されます。

> [!Note]
> テンプレート (VMware 環境上のサーバーの場合は OVA、Hyper-V 環境の場合は VHD) を使用してアプライアンスをデプロイした場合は、同じアプライアンスを使用して、プライベート エンドポイント接続で Azure Migrate プロジェクトに登録できます。

アプライアンスを設定するには:
  1. インストーラー スクリプトを含んだ ZIP ファイルをポータルからダウンロードします。
  1. アプライアンスのホストとなるサーバー上に ZIP ファイルをコピーします。
  1. ZIP ファイルをダウンロードした後、ファイルのセキュリティを確認します。
  1. インストーラー スクリプトを実行して、アプライアンスを展開します。

#### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
3.  最新のアプライアンス バージョンとハッシュ値を確認します。

    **ダウンロード** | **ハッシュ値**
    --- | ---
    [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2160648) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

> [!NOTE]
> 同じスクリプトを使用すると、選択したシナリオ (VMware、Hyper-V、物理など) のいずれかでプライベート エンドポイント接続を使用してアプライアンスを設定し、必要な構成でアプライアンスをデプロイすることができます。

選択したシナリオ (VMware、Hyper-V、物理など) の[ハードウェア要件](./migrate-appliance.md)をサーバーが満たしていること、また、[必要な URL](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) に接続できることを確認します。

#### <a name="run-the-azure-migrate-installer-script"></a>Azure Migrate インストーラー スクリプトを実行する

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。  既存の Azure Migrate アプライアンスが存在するサーバー上でスクリプトを実行しないよう注意してください。

2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。

3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。

4. 次のコマンドを実行して `AzureMigrateInstaller.ps1` という名前のスクリプトを実行します。

   `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1`

5. シナリオ、クラウド、接続性からそれぞれオプションを選択して、必要な構成でアプライアンスをデプロイします。 たとえば、以下に示す選択内容の場合、アプライアンスは、**Azure パブリック クラウド** で **プライベート エンドポイント接続** を使用して、**VMware 環境で実行されているサーバー** を検出して Azure Migrate プロジェクトに対して評価するように設定されます。

   :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-inline.png" alt-text="必要なプライベート エンドポイント向けの構成でアプライアンスを設定する方法を示すスクリーンショット。" lightbox="./media/how-to-use-azure-migrate-with-private-endpoints/script-vmware-private-expanded.png":::

スクリプトが正常に実行されると、アプライアンス構成マネージャーが自動的に起動します。

> [!NOTE]
> 問題が発生した場合は、トラブルシューティングのために、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log のスクリプト ログにアクセスできます。

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>アプライアンスを構成して継続的な検出を開始する

アプライアンス サーバーに接続できるいずれかのマシンでブラウザーを開きます。 アプライアンス構成マネージャーの URL (`https://appliance name or IP address: 44368`) を開きます。 または、アプライアンス サーバーのデスクトップから構成マネージャーへのショートカットを選択して、構成マネージャーを開くこともできます。

#### <a name="set-up-prerequisites"></a>前提条件の設定

1. サードパーティの情報を確認し、**ライセンス条項** に同意します。

1. 構成マネージャーの **[前提条件のセットアップ]** で、以下を実行します。
   - **接続**: 必要な URL へのアクセスがアプライアンスによってチェックされます。 サーバーでプロキシを使用する場合は、次の操作を行います。
     - **[プロキシの設定]** を選択し、プロキシ アドレス (`http://ProxyIPAddress` または `http://ProxyFQDN`) とリッスン ポートを指定します。
     - プロキシで認証が必要な場合は、資格情報を指定します。 サポートされるのは HTTP プロキシのみです。
     - プロキシ サーバーをバイパスする URL または IP アドレスのリストを追加できます。
        ![バイパス プロキシ リストに追加する](./media/how-to-use-azure-migrate-with-private-endpoints/bypass-proxy-list.png)
     - プロキシ サーバーの詳細を更新した場合やプロキシをバイパスするために URL または IP アドレスを追加した場合は、 **[保存]** を選択して構成を登録します。

        > [!Note]
        > 接続チェック中に aka.ms/* リンクでエラーが発生し、アプライアンスからインターネット経由でこの URL にアクセスできないようにしたい場合は、アプライアンスの自動更新サービスを無効にします。 「[自動更新をオフにする](./migrate-appliance.md#turn-off-auto-update)」の手順に従います。 自動更新を無効にすると、aka.ms/* URL の接続チェックがスキップされます。

   - **時刻同期**:検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
   - **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** を選択して、アプライアンス サーバーで実行されているサービスの状態とバージョンを確認します。
        > [!Note]
        > アプライアンスの自動更新を無効にした場合は、アプライアンスのサービスを手動で最新版に更新することができます。 「[古いバージョンを手動で更新する](./migrate-appliance.md#manually-update-an-older-version)」の手順に従います。
   - **VDDK のインストール**: ("_VMware アプライアンスでのみ必要_") VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが、アプライアンスによってチェックされます。 インストールされていない場合は、VMware から VDDK 6.7 をダウンロードします。 インストール手順に記載されているとおりに、ダウンロードした ZIP コンテンツをアプライアンス上の指定された場所に抽出します。

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>アプライアンスを登録して継続的な検出を開始する

前提条件の確認が完了したら、手順に従ってアプライアンスを登録し、各シナリオの継続的な検出を開始します。
- [VMware VM](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)
- [Hyper-V VM](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate)
- [物理サーバー](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate)
- [AWS VM](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate)
- [GCP VM](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate)

>[!Note]
> アプライアンスの登録中や検出の開始時に DNS 解決の問題が発生した場合は、Azure Migrate アプライアンスをホストするオンプレミス サーバーから、ポータルの **[キーの生成]** ステップで作成した Azure Migrate リソースに到達できることを確認してください。 [ネットワーク接続](./troubleshoot-network-connectivity.md)の確認方法の詳細を参照してください。

### <a name="assess-your-servers-for-migration-to-azure"></a>Azure への移行に備えてサーバーを評価する
検出が完了したら、Azure Migrate: Discovery and Assessment ツールを使用して、Azure VM または Azure VMware Solution への移行に備えてサーバー ([VMware VM](./tutorial-assess-vmware-azure-vm.md)、[Hyper-V VM](./tutorial-assess-hyper-v.md)、[物理サーバー](./tutorial-assess-vmware-azure-vm.md)、[AWS VM](./tutorial-assess-aws.md)、[GCP VM](./tutorial-assess-gcp.md) など) を評価します。

Azure Migrate: Discovery and Assessment ツールで、インポートした CSV ファイルを使用して、[オンプレミス マシンを評価](./tutorial-discover-import.md#prepare-the-csv)することもできます。

## <a name="migrate-servers-to-azure-by-using-private-link"></a>Private Link を使用してサーバーを Azure に移行する

以降のセクションでは、ExpressRoute プライベート ピアリングまたは VPN 接続を使用した移行に Azure Migrate と[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用するために必要な手順を説明します。

この記事では、Azure プライベート エンドポイントを使用して [VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理サーバー](./tutorial-migrate-physical-virtual-machines.md)、[AWS で実行されている VM](./tutorial-migrate-aws-virtual-machines.md)、[GCP で実行されている VM](./tutorial-migrate-gcp-virtual-machines.md)、または別の仮想化プロバイダー上で実行されている VM を移行するためのエージェントベースのレプリケーションの概念実証のデプロイ パスを示しています。 Private Link を使用した[エージェントレスの Hyper-V 移行](./tutorial-migrate-hyper-v.md)も、同様の方法で実行できます。

>[!Note]
>[エージェントレスの VMware 移行](./tutorial-assess-physical.md)には、インターネット アクセスまたは ExpressRoute Microsoft ピアリングによる接続が必要です。

### <a name="set-up-a-replication-appliance-for-migration"></a>移行に備えてレプリケーション アプライアンスを設定する

次の図は、Azure Migrate: Server Migration ツールを使用した、プライベート エンドポイントによるエージェントベースのレプリケーション ワークフローを示しています。

![レプリケーション アーキテクチャを示す図。](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

このツールは、レプリケーション アプライアンスを使用して、サーバーを Azure にレプリケートします。 [レプリケーション アプライアンス用のマシンの準備と設定](./tutorial-migrate-physical-virtual-machines.md#prepare-a-machine-for-the-replication-appliance)の方法の詳細を参照してください。

レプリケーション アプライアンスの設定後は、次の手順に従って、移行に必要なリソースを作成してください。

1. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** を選択します。
1. **[ターゲット リージョン]** で、マシンの移行先にする Azure リージョンを選択、確認します。
1. **[リソースの作成]** を選択して、必要な Azure リソースを作成します。 リソースの作成中にページを閉じないでください。
    - この手順により、バックグラウンドで Recovery Services コンテナーが作成され、そのコンテナーのマネージド ID が有効になります。 Recovery Services コンテナーは、サーバーのレプリケーション情報を含むエンティティであり、レプリケーション操作をトリガーするために使用されます。
    - Azure Migrate プロジェクトにプライベート エンドポイント接続がある場合は、Recovery Services コンテナーにプライベート エンドポイントが作成されます。 この手順により、プライベート エンドポイントには、完全修飾ドメイン名 (FQDN) が 5 つ (Recovery Services コンテナーにリンクされたマイクロサービスごとに 1 つ) 追加されます。
    - 5 つのドメイン名はこのパターンで書式設定されます。 <br/> _{Vault-ID}-asr-pod01-{type}-.{target-geo-code}_ .privatelink.siterecovery.windowsazure.com
    - 既定では、Azure Migrate によって自動的にプライベート DNS ゾーンが作成され、Recovery Services コンテナーのマイクロサービス用に DNS A レコードが追加されます。 その後、このプライベート DNS は、プライベート エンドポイントの仮想ネットワークにリンクされます。

1. レプリケーション アプライアンスを登録する前に、レプリケーション アプライアンスをホストするマシンからコンテナーのプライベート リンク FQDN に到達できることを確認します。 オンプレミスのレプリケーション アプライアンスがプライベート IP アドレスへのプライベート リンク FQDN を解決するために、追加の DNS 構成が必要になる場合があります。 [ネットワーク接続の確認方法](./troubleshoot-network-connectivity.md#verify-dns-resolution)の詳細を参照してください。

1. 接続を確認したら、アプライアンスのセットアップおよびキー ファイルをダウンロードし、インストール プロセスを実行して、Azure Migrate にそのアプライアンスを登録します。 [レプリケーション アプライアンスを設定する](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)方法の詳細を参照してください。 レプリケーション アプライアンスの設定後、これらの手順に従って、移行対象のマシンに[モビリティ サービスをインストール](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service)します。

### <a name="replicate-servers-to-azure-by-using-private-link"></a>Private Link を使用してサーバーを Azure にレプリケートする

[これらの手順](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)に従って、レプリケーションの対象となるサーバーを選択します。

**[レプリケート]**  >  **[ターゲット設定]**  >  **[キャッシュ/レプリケーション ストレージ アカウント]** のドロップダウン リストを使用して、プライベート リンク経由でレプリケートするためのストレージ アカウントを選択します。

Azure Migrate プロジェクトにプライベート エンドポイント接続がある場合は、Azure Migrate に必要なストレージ アカウントにアクセスするための[アクセス許可を Recovery Services コンテナーのマネージド ID に付与](#grant-access-permissions-to-the-recovery-services-vault)する必要があります。

プライベート リンクを介したレプリケーションを有効にするために、[ストレージ アカウント用のプライベート エンドポイントを作成](#create-a-private-endpoint-for-the-storage-account-optional)します。

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Recovery Services コンテナーにアクセス許可を付与する

キャッシュ ストレージ アカウントまたはレプリケーション ストレージ アカウントに対する認証済みアクセスのために、Recovery Services コンテナーにアクセス許可を付与する必要があります。

Azure Migrate によって作成された Recovery Services コンテナーを特定し、必要なアクセス許可を付与するには、次の手順を実行します。

**Recovery Services コンテナーとマネージド ID のオブジェクト ID を特定する**

Recovery Services コンテナーの詳細は、Azure Migrate: Server Migration の **[プロパティ]** ページで確認できます。

1. **Azure Migrate** ハブに移動し、**Azure Migrate: Server Migration** タイルの **[概要]** を選択します。

    ![Azure Migrate ハブの [概要] ページを示すスクリーンショット。](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

1. 左側のウィンドウで、**[プロパティ]** を選択します。 Recovery Services コンテナーの名前とマネージド ID をメモします。 コンテナーの **[接続の種類]** は **[プライベート エンドポイント]** に、 **[レプリケーションの種類]** は **[その他]** になります。 コンテナーにアクセス権を提供する際に、この情報が必要になります。

    ![Azure Migrate: Server Migration の [プロパティ] ページを示すスクリーンショット。](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**ストレージ アカウントにアクセスするためのアクセス許可**

 コンテナーのマネージド ID には、レプリケーションに必要なストレージ アカウントに対する以下のロールのアクセス許可を付与する必要があります。 この場合は、ストレージ アカウントを事前に作成しておく必要があります。

>[!Note]
> Private Link を使用して Hyper-V VM を Azure に移行する場合は、レプリケーション ストレージ アカウントとキャッシュ ストレージ アカウントの両方に対するアクセス権を付与する必要があります。

Azure Resource Manager のロールのアクセス許可は、ストレージ アカウントの種類によって異なります。

|**ストレージ アカウントの種類** | **ロールのアクセス許可**|
|--- | ---|
|標準の型 | [Contributor](../role-based-access-control/built-in-roles.md#contributor)<br>[ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|
|プレミアムの型 | [Contributor](../role-based-access-control/built-in-roles.md#contributor)<br>[ストレージ BLOB データ所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. レプリケーション用に選択したレプリケーション ストレージ アカウントまたはキャッシュ ストレージ アカウントに移動します。 左側のペインで **[アクセス制御 (IAM)]** を選択します。

1. **[+ 追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。

   ![[ロールの割り当ての追加] を示すスクリーンショット。](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)

1. **[ロールの割り当ての追加]** ページの **[ロール]** ボックスで、前述のアクセス許可リストから適切なロールを選択します。 先ほどメモしたコンテナーの名前を入力し、 **[保存]** を選択します。

    ![[ロールの割り当てを追加する] ページを示すスクリーンショット。](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

1. これらのアクセス許可に加え、Microsoft の信頼済みサービスへのアクセスも許可する必要があります。 ネットワーク アクセスが特定のネットワークに制限されている場合は、 **[ネットワーク]** タブの **[例外]** セクションで、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択します。

   ![[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します] オプションを示すスクリーンショット。](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)

### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>ストレージ アカウントのプライベート エンドポイントを作成する (省略可)

プライベート ピアリングによる ExpressRoute を使用してレプリケートを行うには、キャッシュ ストレージ アカウントまたはレプリケーション ストレージ アカウントの [プライベート エンドポイントを作成](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint)します (対象サブリソース: _BLOB_)。

>[!Note]
> プライベート エンドポイントは、汎用 v2 のストレージ アカウントでのみ作成できます。 価格情報については、「[Azure ページ BLOB の価格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)」および「[Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)」を参照してください。

ストレージ アカウント用のプライベート エンドポイントは、Azure Migrate プロジェクトのプライベート エンドポイントと同じ仮想ネットワーク内、またはそのネットワークに接続されている別の仮想ネットワーク内に作成します。

**[はい]** を選択して、プライベート DNS ゾーンと統合します。 プライベート DNS ゾーンは、プライベート リンク上で、仮想ネットワークからストレージ アカウントに接続をルーティングする際に利用されます。 **[はい]** を選択すると、DNS ゾーンが仮想ネットワークに自動的にリンクされます。 また、作成される新しい IP と FQDN を解決するために DNS レコードも追加されます。 [プライベート DNS ゾーン](../dns/private-dns-overview.md)の詳細を参照してください。

プライベート エンドポイントを作成したユーザーがストレージ アカウント所有者でもある場合、プライベート エンドポイントの作成は自動承認されます。 そうでない場合、ストレージ アカウントの所有者がプライベート エンドポイントの使用を承認する必要があります。 要求されたプライベート エンドポイント接続を承認または拒否するには、ストレージ アカウント ページの **[ネットワーク]** にある **[プライベート エンドポイント接続]** に移動します。

続行する前に、プライベート エンドポイントの接続状態を確認します。

![プライベート エンドポイントの承認状態を示すスクリーンショット。](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

プライベート エンドポイントの作成後、 **[レプリケート]**  >  **[ターゲット設定]**  >  **[キャッシュ ストレージ アカウント]** のドロップダウン リストを使用して、プライベート リンクでレプリケートするためのストレージ アカウントを選択します。

オンプレミスのレプリケーション アプライアンスのプライベート エンドポイントに、ストレージ アカウントへのネットワーク接続があることを確認します。 [ネットワーク接続](./troubleshoot-network-connectivity.md)の確認方法の詳細を参照してください。

>[!Note]
> Hyper-V VM を Azure に移行するケースで、レプリケーション ストレージ アカウントの種類が _Premium_ である場合、キャッシュ ストレージ アカウントには、_Standard_ タイプの別のストレージ アカウントを選択する必要があります。 このケースでは、レプリケーション ストレージ アカウントとキャッシュ ストレージ アカウントの両方のプライベート エンドポイントを作成する必要があります。

その後、[レプリケーションの確認と開始](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)および[移行の実行](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration)に関する手順に従ってください。

## <a name="next-steps"></a>次の手順

- [移行プロセス](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration)を完了します。
- [移行後のベスト プラクティス](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices)を確認します。