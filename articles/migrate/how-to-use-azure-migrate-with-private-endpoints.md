---
title: プライベート エンドポイントで Azure Migrate を使用する
description: Azure Migrate のプライベート リンクのサポートを利用して、プライベート リンクを使った検出、評価、移行を行います。
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: d1b3b7916e290f97d528928bf2635997989c09c2
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/11/2021
ms.locfileid: "109750827"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>プライベート エンドポイントで Azure Migrate を使用する  

この記事では、[Azure Private Link](../private-link/private-endpoint-overview.md) を使用したプライベート ネットワーク上で、Azure Migrate を使用してサーバーの検出、評価、移行を行う方法について説明します。

Azure Private Link を使用すると、ExpressRoute のプライベート ピアリングまたはサイト間 VPN 接続で [Azure Migrate: Discovery and Assessment](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) および [Azure Migrate: Server Migration](./migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用して、Azure Migrate サービスにプライベートかつ安全に接続することができます。

プライベート エンドポイントの接続方法は、公衆ネットワークを経由することなく Azure Migrate サービスやその他の Azure リソースにアクセスするという組織的な要件がある場合に推奨されます。 また、既にある ExpressRoute プライベート ピアリング回線を使用するためにプライベート リンクのサポートを利用すれば、帯域幅や待ち時間の要件も満たしやすくなります。

## <a name="support-requirements"></a>サポートの要件

### <a name="required-permissions"></a>必要なアクセス許可

サブスクリプションの **共同作成者 + ユーザー アクセス管理者** または **所有者** のアクセス許可。

### <a name="supported-scenarios-and-tools"></a>サポートされるシナリオとツール

**デプロイ** | **詳細** | **ツール**
--- | --- | ---
**検出と評価** | 任意のプラットフォーム上で動作するサーバーの検出と評価を大規模にエージェントレスで行います。ハイパーバイザー プラットフォーム ([VMware vSphere](./tutorial-discover-vmware.md) や [Microsoft Hyper-V](./tutorial-discover-hyper-v.md) など)、パブリック クラウド ([AWS](./tutorial-discover-aws.md) や [GCP](./tutorial-discover-gcp.md) など)、さらに[ベアメタル サーバー](./tutorial-discover-physical.md)が対象となります。 | Azure Migrate: Discovery and Assessment  <br/>
**ソフトウェア インベントリ** | VMware VM 上で実行されているアプリ、ロール、機能を検出します。 | Azure Migrate: Discovery and Assessment  
**依存関係の視覚化** | 依存関係分析機能を使用して、サーバー間の依存関係を特定、把握します。 <br/> Azure Migrate のプライベート リンク サポートでは、[エージェントレスの依存関係の視覚化](./how-to-create-group-machine-dependencies-agentless.md)がネイティブでサポートされます。 <br/>[エージェントベースの依存関係の視覚化](./how-to-create-group-machine-dependencies.md)には、インターネット接続が必要となります。 エージェントベースの依存関係の視覚化にプライベート エンドポイントを使用する[方法](../azure-monitor/logs/private-link-security.md)を参照してください。 | Azure Migrate: Discovery and Assessment |
**移行** | [エージェントレスの Hyper-V 移行](./tutorial-migrate-hyper-v.md)を実行するか、エージェントベースの方法を使用して [VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理サーバー](./tutorial-migrate-physical-virtual-machines.md)、[AWS で実行されている VM](./tutorial-migrate-aws-virtual-machines.md)、[GCP で実行されている VM](./tutorial-migrate-gcp-virtual-machines.md)、または別の仮想化プロバイダー上で実行されている VM を移行します。 | Azure Migrate: Server Migration

>[!Note]
>
> [エージェントレスの VMware 移行](./tutorial-migrate-vmware.md)には、インターネット アクセスまたは ExpressRoute Microsoft ピアリングによる接続が必要です。 <br/> ExpressRoute プライベート ピアリングまたはサイト間 (S2S) VPN 接続でプライベート エンドポイントを使用してレプリケーションを実行する[方法](./replicate-using-expressroute.md)を参照してください。  <br/><br/>

#### <a name="other-integrated-tools"></a>その他の統合ツール

その他の移行ツールでは、公衆ネットワーク アクセスが無効になっていると、利用状況データを Azure Migrate プロジェクトにアップロードできない場合があります。 他の Microsoft オファリングまたは外部の[独立系ソフトウェア ベンダー (ISV)](./migrate-services-overview.md#isv-integration) のオファリングからデータを受信するには、すべてのネットワークからのトラフィックを許可するように Azure Migrate プロジェクトを構成する必要があります。


Azure Migrate プロジェクトの公衆ネットワーク アクセスを有効にするには、Azure portal で Azure Migrate の **プロパティ ページ** に移動し、 **[いいえ]** を選択して、 **[保存]** を選択します。

![ネットワーク アクセス モードの変更方法を示す図](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>その他の注意事項   

**考慮事項** | **詳細**
--- | ---
**料金** | 価格情報については、[Azure BLOB の価格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)に関するページと「[Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)」を参照してください。  
**仮想ネットワークの要件** | 選択した仮想ネットワーク内またはそれに接続された仮想ネットワーク内に、ExpressRoute または VPN Gateway のエンドポイントが存在する必要があります。 仮想ネットワークには約 15 個の IP アドレスが必要となる場合があります。  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>プライベート エンドポイント接続を使用してプロジェクトを作成する

こちらの[記事](./create-manage-projects.md#create-a-project-for-the-first-time)を参照して、新しい Azure Migrate プロジェクトを設定します。

> [!Note]
> 既存の Azure Migrate プロジェクトの接続方法をプライベート エンドポイント接続に変更することはできません。

Azure Migrate プロジェクトのプライベート エンドポイントを作成するには、 **[詳細]** 構成セクションに以下の情報を入力します。
- **[接続方法]** で **[プライベート エンドポイント]** を選択します。
- **[Disable public endpoint access]\(パブリック エンドポイント アクセスを無効にする\)** では、既定の設定である **[いいえ]** のままにします。 移行ツールによっては、公衆ネットワーク アクセスが無効になっていると、利用状況データを Azure Migrate プロジェクトにアップロードできない場合があります。 [詳細情報。](#other-integrated-tools)
- **[仮想ネットワークのサブスクリプション]** で、プライベート エンドポイントの仮想ネットワークのサブスクリプションを選択します。
- **[仮想ネットワーク]** で、プライベート エンドポイントの仮想ネットワークを選択します。 Azure Migrate プロジェクトに接続する必要のある Azure Migrate アプライアンスおよびその他のソフトウェア コンポーネントは、このネットワークまたは接続された仮想ネットワーク上に存在する必要があります。
- **[サブネット]** で、プライベート エンドポイントのサブネットを選択します。

**［作成］** を選択します Azure Migrate プロジェクトがデプロイされるまで数分待ちます。 プロジェクトの作成中にこのページを閉じないでください。

![Create project](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)


これで、移行プロジェクトが作成され、それにプライベート エンドポイントがアタッチされます。

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Azure Private Link を使用して移行するサーバーを検出して評価する

### <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスを設定する

1. **[マシンの検出]**  >  **[お使いのマシンは仮想化されていますか?]** で、サーバーの種類を選択します。
2. **[Azure Migrate プロジェクト キーを生成します]** で、Azure Migrate アプライアンスの名前を入力します。
3. **[キーの生成]** を選択して、必要な Azure リソースを作成します。

    > [!Important]
    > リソースの作成中に [マシンの検出] ページを閉じないでください。  
    - この手順では、キー コンテナー、ストレージ アカウント、Recovery Services コンテナー (エージェントレス VMware 移行の場合のみ) のほか、いくつかの内部リソースが Azure Migrate によって作成され、各リソースにプライベート エンドポイントがアタッチされます。 プライベート エンドポイントは、プロジェクトの作成時に選択した仮想ネットワーク内に作成されます。  
    - プライベート エンドポイントが作成されると、Azure Migrate リソース用の DNS CNAME リソース レコードが、"privatelink" というプレフィックスが付いたサブドメイン内のエイリアスに更新されます。 また、既定では、"privatelink" サブドメインに対応するプライベート DNS ゾーンもリソースの種類ごとに作成され、関連付けられたプライベート エンドポイントの DNS A レコードが挿入されます。 これにより、ソース ネットワークに存在する Azure Migrate アプライアンスおよびその他のソフトウェア コンポーネントは、プライベート IP アドレス上にある Azure Migrate リソースのエンドポイントに到達できるようになります。  
    - また、Azure Migrate は、移行プロジェクトの[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にし、ストレージ アカウントに安全にアクセスするためのアクセス許可をそのマネージド ID に付与します。  

4. キーが正常に生成されたら、そのキーの詳細をコピーして、アプライアンスの構成と登録を行います。   

#### <a name="download-the-appliance-installer-file"></a>アプライアンスのインストーラー ファイルをダウンロードする  

Azure Migrate の検出および評価では、軽量の Azure Migrate アプライアンスを使用します。 このアプライアンスによって、サーバー検出が実行され、サーバーの構成とパフォーマンス メタデータが Azure Migrate に送信されます。  

> [!Note]
> テンプレートを使用してアプライアンスを展開するオプション (VMware 環境のサーバーと VHD の Hyper-V 環境の OVA) は、プライベート エンドポイント接続を使用した Azure Migrate プロジェクトではサポートされていません。

アプライアンスを設定するには、インストーラー スクリプトを含んだ ZIP ファイルをポータルからダウンロードします。 アプライアンスのホストとなるサーバー上に ZIP ファイルをコピーします。 ZIP ファイルのダウンロード後、ファイルのセキュリティを確認し、インストーラー スクリプトを実行してアプライアンスをデプロイします。

以下、各シナリオのダウンロード リンクを、そのハッシュ値と共に示します。

シナリオ | ダウンロード リンク | ハッシュ値
--- | --- | ---
Hyper-V | [AzureMigrateInstaller-HyperV-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160557) | 17EFA01E3A7683F1CE2A08E3A9197A27D8BD2CC03C3AB5C6E00E4261A822BDB3
物理 | [AzureMigrateInstaller-Physical-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160558) | 01028F92C2095452F2DDCB89986CDC1F177AAC58E150A5B219A69CF1B7DA3BE0
VMware | [AzureMigrateInstaller-VMware-public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160648) | 66D3217AEC1DE51D84EC608B22BDDA605EC9C4FBAB06FC69FEC985886627C224
VMware スケールアウト | [AzureMigrateInstaller-VMware-Public-Scaleout-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160811) | 42C1E8D5CF428E35E5B98E4E7465DD08439F0FD5C319340CE3E3ADC3DC1717A6

#### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します

    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例 (パブリック クラウドの場合): ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink.zip SHA256 ```

3.  前出の表のハッシュ値を比較して、アプライアンスの最新バージョンを検証します。

選択したシナリオ (VMware、Hyper-V、物理など) の[ハードウェア要件](./migrate-appliance.md)をサーバーが満たしていること、また、サーバーが必要な Azure URL ([パブリック](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) クラウドおよび[政府機関向け](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity)クラウド) に接続できることを確認します。


#### <a name="run-the-script"></a>スクリプトを実行する

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。
2. （昇格した）管理者権限を使用し、マシンで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink> .\AzureMigrateInstaller.ps1```

5. スクリプトが正常に実行されると、アプライアンスの構成マネージャーが起動し、アプライアンスを構成できるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>アプライアンスを構成して継続的な検出を開始する

アプライアンス サーバーに接続できる任意のマシン上でブラウザーを開き、アプライアンス構成マネージャーの URL (`https://appliance name or IP address: 44368`) を開きます。 または、構成マネージャーのショートカットを選択し、アプライアンス サーバーのデスクトップから構成マネージャーを開くこともできます。

#### <a name="set-up-prerequisites"></a>前提条件の設定

1. サードパーティの情報を確認し、**ライセンス条項** に同意します。    

2. 構成マネージャー > **[前提条件のセットアップ]** で、以下を実行します。
   - **接続**: 必要な URL へのアクセスがアプライアンスによってチェックされます。 サーバーでプロキシを使用する場合は、次の操作を行います。
     - **[プロキシの設定]** を選択し、プロキシ アドレス (`http://ProxyIPAddress` または `http://ProxyFQDN`) とリッスン ポートを指定します。
     - プロキシで認証が必要な場合は、資格情報を指定します。 サポートされるのは HTTP プロキシのみです。
     - 必要に応じて、プロキシ サーバーをバイパスする URL または IP アドレスのリストを追加できます。 ExpressRoute プライベート ピアリングを使用している場合は、これらの [URL](./replicate-using-expressroute.md#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations) を必ずバイパスするようにしてください。
     - プロキシ サーバーの詳細を更新した場合やプロキシをバイパスするために URL または IP アドレスを追加した場合は、 **[保存]** を選択して構成を登録する必要があります。

        > [!Note]
        > 接続チェック中に aka.ms/* リンクでエラーが発生し、アプライアンスからインターネット経由でこの URL にアクセスできないようにしたい場合は、[**こちら**](./migrate-appliance.md#turn-off-auto-update)の手順に従って、アプライアンスの自動更新サービスを無効にする必要があります。 自動更新を無効にすると、aka.ms/* URL の接続チェックがスキップされます。

   - **時刻同期**:検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
   - **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** を選択して、アプライアンス サーバーで実行されているサービスの状態とバージョンを確認できます。
        > [!Note]
        > アプライアンスの自動更新サービスを無効にした場合は、[**こちら**](./migrate-appliance.md#manually-update-an-older-version)の手順に従って、アプライアンスのサービスを手動で最新版に更新することができます。
   - **VDDK のインストール**: ("_VMware アプライアンスでのみ必要_") VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが、アプライアンスによってチェックされます。 インストールされていない場合は、**インストール手順** に記載されているとおりに、VMware から VDDK 6.7 をダウンロードし、ダウンロードした zip コンテンツをアプライアンス上の指定された場所に抽出します。

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>アプライアンスを登録して継続的な検出を開始する

前提条件の確認が完了したら、[VMware VM](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)、[Hyper-V VM](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate)、[物理サーバー](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate)、[AWS VM](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate)、[GCP VM](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate) のシナリオごとの手順に従って、アプライアンスを登録し、継続的な検出を開始します。


>[!Note]
> アプライアンスの登録中や検出の開始時に DNS 解決の問題が発生した場合は、Azure Migrate アプライアンスをホストするオンプレミス サーバーから、ポータルの **[キーの生成]** ステップで作成した Azure Migrate リソースに到達できることを確認してください。 [ネットワーク接続の確認方法の詳細を参照してください](#troubleshoot-network-connectivity)。

### <a name="assess-your-servers-for-migration-to-azure"></a>Azure への移行に備えてサーバーを評価する
検出が完了したら、Azure Migrate: Discovery and Assessment ツールを使用して、Azure VM または Azure VMware Solution (AVS) への移行に備えてサーバー ([VMware VM](./tutorial-assess-vmware-azure-vm.md)、[Hyper-V VM](./tutorial-assess-hyper-v.md)、[物理サーバー](./tutorial-assess-vmware-azure-vm.md)、[AWS VM](./tutorial-assess-aws.md)、[GCP VM](./tutorial-assess-gcp.md)) を評価します。

Azure Migrate: Discovery and Assessment ツールで、インポートしたコンマ区切り値 (CSV) ファイルを使用して、[オンプレミス マシンを評価](./tutorial-discover-import.md#prepare-the-csv)することもできます。   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Azure Private Link を使用してサーバーを Azure に移行する

以降のセクションでは、ExpressRoute プライベート ピアリングまたは VPN 接続を使用した移行に Azure Migrate と[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用するために必要な手順を説明します。  

この記事では、Azure プライベート エンドポイントを使用して [VMware VM](./tutorial-migrate-vmware-agent.md)、[Hyper-V VM](./tutorial-migrate-physical-virtual-machines.md)、[物理サーバー](./tutorial-migrate-physical-virtual-machines.md)、[AWS で実行されている VM](./tutorial-migrate-aws-virtual-machines.md)、[GCP で実行されている VM](./tutorial-migrate-gcp-virtual-machines.md)、または別の仮想化プロバイダー上で実行されている VM を移行するためのエージェントベースのレプリケーションの概念実証のデプロイ パスを示しています。 プライベート リンクを使用した[エージェントレスの Hyper-V 移行](./tutorial-migrate-hyper-v.md)も、同様の方法で実行できます。

>[!Note]
>[エージェントレスの VMware 移行](./tutorial-assess-physical.md)には、インターネット アクセスまたは ExpressRoute Microsoft ピアリングによる接続が必要です。 

### <a name="set-up-a-replication-appliance-for-migration"></a>移行に備えてレプリケーション アプライアンスを設定する

次の図は、Azure Migrate: Server Migration ツールを使用した、プライベート エンドポイントによるエージェントベースのレプリケーション ワークフローを示しています。  

![レプリケーションのアーキテクチャ](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

このツールは、レプリケーション アプライアンスを使用して、サーバーを Azure にレプリケートします。 [レプリケーション アプライアンス用のマシンの準備と設定](./tutorial-migrate-physical-virtual-machines.md#prepare-a-machine-for-the-replication-appliance)は、この記事を使用して行います。

レプリケーション アプライアンスの設定後は、次の手順に従って、移行に必要なリソースを作成してください。

1. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** を選択します。
2. **[ターゲット リージョン]** で、マシンの移行先にする Azure リージョンを選択、確認します。
3. **[リソースの作成]** を選択して、必要な Azure リソースを作成します。 リソースの作成中にページを閉じないでください。   
    - これにより、バックグラウンドで Recovery Services コンテナーが作成され、そのコンテナーのマネージド ID が有効になります。 Recovery Services コンテナーは、サーバーのレプリケーション情報を含むエンティティであり、レプリケーション操作をトリガーするために使用されます。  
    - Azure Migrate プロジェクトにプライベート エンドポイント接続がある場合は、Recovery Services コンテナーにプライベート エンドポイントが作成されます。 これにより、プライベート エンドポイントには、完全修飾プライベート名 (FQDN) が 5 つ (Recovery Services コンテナーにリンクされたマイクロサービスごとに 1 つ) 追加されます。   
    - 5 つのドメイン名はこのパターンで書式設定されます。 <br/> _{Vault-ID}-asr-pod01-{type}-.{target-geo-code}_ .privatelink.siterecovery.windowsazure.com  
    - 既定では、Azure Migrate によって自動的にプライベート DNS ゾーンが作成され、Recovery Services コンテナーのマイクロサービス用に DNS A レコードが追加されます。 その後、このプライベート DNS ゾーンは、プライベート エンドポイントの仮想ネットワークにリンクされます。 これで、オンプレミスのレプリケーション アプライアンスが、完全修飾ドメイン名をそのプライベート IP アドレスに解決できるようになります。

4. レプリケーション アプライアンスを登録する前に、レプリケーション アプライアンスをホストするマシンからコンテナーのプライベート リンク FQDN に到達できることを確認します。 [ネットワーク接続の確認方法の詳細を参照してください](#troubleshoot-network-connectivity)。

5. 接続を確認したら、アプライアンスのセットアップおよびキー ファイルをダウンロードし、インストール プロセスを実行して、Azure Migrate にそのアプライアンスを登録します。 [こちらの詳細な手順](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)を確認してください。 レプリケーション アプライアンスの設定後、これらの手順に従って、移行対象のマシンに[モビリティ サービスをインストール](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service)します。

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Azure Private Link を使用して Azure にサーバーをレプリケートする

今度は、[これらの手順](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)に従って、レプリケーションの対象となるサーバーを選択します。  

**[レプリケート]**  >  **[ターゲット設定]**  >  **[キャッシュ/レプリケーション ストレージ アカウント]** のドロップダウンを使用して、プライベート リンク経由でレプリケートするためのストレージ アカウントを選択します。  

Azure Migrate プロジェクトにプライベート エンドポイント接続がある場合は、Azure Migrate に必要なストレージ アカウントにアクセスするための[アクセス許可を Recovery Services コンテナーのマネージド ID に付与](#grant-access-permissions-to-the-recovery-services-vault)する必要があります。   

加えて、プライベート リンクを介したレプリケーションを有効にするために、[ストレージ アカウント用のプライベート エンドポイントを作成](#create-a-private-endpoint-for-the-storage-account-optional)します。

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Recovery Services コンテナーにアクセス許可を付与する

Recovery Services コンテナーのマネージド ID には、キャッシュ ストレージ アカウントまたはレプリケーション ストレージ アカウントに対する認証済みアクセスのための権限が必要となります。

以下のガイダンスに従って、Azure Migrate によって作成された Recovery Services コンテナーを特定し、必要なアクセス許可を付与してください。

"**_Recovery Services コンテナーとマネージド ID のオブジェクト ID を特定する_**"

Recovery Services コンテナーの詳細は、Azure Migrate: Server Migration の **[プロパティ]** ページで確認できます。

1. **Azure Migrate ハブ** に移動し、Azure Migrate: Server Migration タイルの **[概要]** を選択します。

    ![Azure Migrate ハブの [概要] ページ](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. 左側のペインで、 **[プロパティ]** を選択します。 Recovery Services コンテナーの名前とマネージド ID をメモします。 コンテナーの **[接続の種類]** は "_プライベート エンドポイント_" に、 **[レプリケーションの種類]** は "_その他_" になります。 コンテナーにアクセス権を提供する際に、この情報が必要になります。

    ![Azure Migrate: Server Migration の [プロパティ] ページ](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

"**_ストレージ アカウントにアクセスするために必要なアクセス許可を付与する_**"

 コンテナーのマネージド ID には、レプリケーションに必要なストレージ アカウントに対する以下のロールのアクセス許可を付与する必要があります。  この場合は、ストレージ アカウントを事前に作成しておく必要があります。

>[!Note]
> プライベート リンクを使用して Hyper-V VM を Azure に移行する場合は、レプリケーション ストレージ アカウントとキャッシュ ストレージ アカウントの両方に対するアクセス権を付与する必要があります。

ロールのアクセス許可は、ストレージ アカウントの種類によって異なります。

- Resource Manager ベースのストレージ アカウント (Standard タイプ):
  - [共同作成者](../role-based-access-control/built-in-roles.md#contributor) "_および_"
  - [ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager ベースのストレージ アカウント (Premium タイプ):
  - [共同作成者](../role-based-access-control/built-in-roles.md#contributor) "_および_"
  - [ストレージ BLOB データ所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. レプリケーション用に選択したレプリケーション ストレージ アカウントまたはキャッシュ ストレージ アカウントに移動します。 左ペインで **[アクセス制御 (IAM)]** を選択します。

1. **[ロールの割り当てを追加する]** セクションで、 **[追加]** を選択します。

   ![ロールの割り当てを追加する](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. **[ロールの割り当てを追加]** ページの **[ロール]** フィールドで、前述のアクセス許可リストから適切なロールを選択します。 先ほどメモしたコンテナーの名前を入力し、 **[保存]** を選択します。

    ![ロール ベースのアクセスを提供する](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. これらのアクセス許可に加え、Microsoft の信頼済みサービスへのアクセスも許可する必要があります。 ネットワーク アクセスが特定のネットワークに制限されている場合は、 **[ネットワーク]** タブの **[例外]** セクションで、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を選択します。

![ストレージ アカウントに対して信頼された Microsoft サービスを許可する](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>ストレージ アカウントのプライベート エンドポイントを作成する (省略可)

プライベート ピアリングによる ExpressRoute を使用してレプリケートを行うには、キャッシュ ストレージ アカウントまたはレプリケーション ストレージ アカウントの [プライベート エンドポイントを作成](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint)します (対象サブリソース: **_BLOB_**)。

>[!Note]
>
> - プライベート エンドポイントは、General Purpose v2 (GPv2) のストレージ アカウントでのみ作成できます。 価格情報については、「[Azure ページ BLOB の価格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)」および「[Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)」を参照してください。

ストレージ アカウント用のプライベート エンドポイントは、Azure Migrate プロジェクトのプライベート エンドポイントと同じ仮想ネットワーク内、またはそのネットワークに接続されている別の仮想ネットワーク内に作成する必要があります。

**[はい]** を選択して、プライベート DNS ゾーンと統合します。 プライベート DNS ゾーンは、プライベート リンク上で、仮想ネットワークからストレージ アカウントに接続をルーティングする際に利用されます。 **[はい]** を選択すると、仮想ネットワークに DNS ゾーンが自動的にリンクされ、作成された新しい IP と完全修飾ドメイン名の解決に必要な DNS レコードが追加されます。 詳細については、[プライベート DNS ゾーン](../dns/private-dns-overview.md)をご覧ください。

プライベート エンドポイントを作成しているユーザーがストレージ アカウントの所有者でもある場合、プライベート エンドポイントは自動承認されます。 そうでない場合、ストレージ アカウントの所有者がプライベート エンドポイントの使用を承認する必要があります。 要求されたプライベート エンドポイント接続を承認または拒否するには、ストレージ アカウント ページの **[ネットワーク]** にある **[プライベート エンドポイント接続]** に移動します。

先に進む前に、プライベート エンドポイントの接続状態を確認します。

![プライベート エンドポイントの承認状態](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

プライベート エンドポイントの作成後、 **[レプリケート]**  >  **[ターゲット設定]**  >  **[キャッシュ ストレージ アカウント]** のドロップダウンを使用して、プライベート リンクでレプリケートするためのストレージ アカウントを選択します。  

オンプレミスのレプリケーション アプライアンスのプライベート エンドポイントに、ストレージ アカウントへのネットワーク接続があることを確認します。 [ネットワーク接続の確認方法の詳細を参照してください](#troubleshoot-network-connectivity)。

>[!Note]
>
> - Hyper-V VM を Azure に移行するケースで、レプリケーション ストレージ アカウントの種類が _Premium_ である場合、キャッシュ ストレージ アカウントには、_Standard_ タイプの別のストレージ アカウントを選択する必要があります。 このケースでは、レプリケーション ストレージ アカウントとキャッシュ ストレージ アカウントの両方のプライベート エンドポイントを作成する必要があります。  

その後、[レプリケーションの確認と開始](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)および[移行の実行](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration)に関する手順に従ってください。  

## <a name="troubleshoot-network-connectivity"></a>ネットワーク接続のトラブルシューティング

### <a name="validate-private-endpoints-configuration"></a>プライベート エンドポイントの構成を確認する

プライベート エンドポイントが承認済みの状態であることを確認します。  

1. Azure Migrate: Discovery and Assessment と Azure Migrate: Server Migration のプロパティ ページに移動します。
2. プロパティ ページには、Azure Migrate によって自動的に作成されたプライベート リンク FQDN とプライベート エンドポイントが一覧表示されます。  

3. 診断したいプライベート エンドポイントを選択します。  
    1. 接続状態が [承認済み] になっていることを確認します。
    2. 接続が承認待ち状態である場合、接続を承認する必要があります。
    3. プライベート エンドポイント リソースに移動して、仮想ネットワークが、移行プロジェクトのプライベート エンドポイントの仮想ネットワークと一致しているかどうかを確認することもできます。

    ![プライベート エンドポイント接続を確認する](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)


### <a name="validate-the-data-flow-through-the-private-endpoints"></a>プライベートエンドポイントを介してデータフローを検証する
データ フロー メトリックを確認して、プライベート エンドポイント経由のトラフィック フローを確認します。 [Azure Migrate: サーバー評価とサーバー移行のプロパティ] ページで、プライベート エンドポイントを選択します。 これにより、Azure プライベート リンク センターの 「プライベート エンドポイントの概要」セクションにリダイレクトされます。 左側のメニューで **[メトリック]** を選択して、トラフィック フローを表示するための _データ バイト数_ と _データ バイト アウト_ 情報を表示します。

### <a name="verify-dns-resolution"></a>DNS 解決を確認する

オンプレミス アプライアンス (またはレプリケーション プロバイダー) が Azure Migrate リソースにアクセスする際は、その完全修飾プライベート リンク ドメイン名 (FQDN) が使用されます。 ソース環境からプライベート エンドポイントのプライベート IP アドレスを解決するために、追加の DNS 設定が必要になる場合があります。 ネットワーク接続の問題をトラブルシューティングする際に役立つ可能性のある DNS 構成のシナリオについては、[こちらの記事を参照](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)してください。  

プライベート リンク接続を検証するには、Migrate アプライアンスのホストとなるオンプレミス サーバーから Azure Migrate リソース エンドポイント (プライベート リンク リソースの FQDN) の DNS 解決を実行し、その FQDN がプライベート IP アドレスに解決されることを確認します。
プライベート エンドポイントの詳細とプライベート リンク リソースの FQDN の情報は、Discovery and Assessment と Server Migration のプロパティ ページで入手できます。 **[DNS 設定のダウンロード]** を選択すると、その一覧が表示されます。   

 ![Azure Migrate: Discovery and Assessment のプロパティ](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure Migrate: Server Migration のプロパティ](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

ストレージ アカウントのプライベート リンク FQDN を DNS 解決する具体的な例  

- 「_nslookup<storage-account-name>_ .blob.core.windows.net」と入力します。  <storage-account-name> は、Azure Migrate で使用するストレージ アカウントの名前に置き換えてください。  

    このようなメッセージが返されます。  

   ![DNS 解決の例](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- ストレージ アカウントに対応する 10.1.0.5 というプライベート IP アドレスが返されます。 このアドレスは、プライベート エンドポイントの仮想ネットワーク サブネットに属します。   

他の Azure Migrate アーティファクトについても、同様の方法を使用して DNS 解決を検証できます。   

DNS 解決が正しくない場合は、これらの手順を実行します。  

- カスタム DNS を使用している場合は、カスタム DNS の設定を確認し、DNS 構成が正しいことを確認します。 ガイダンスについては、[プライベート エンドポイントの概要に関する記事の「DNS の構成」](../private-link/private-endpoint-overview.md#dns-configuration)を参照してください。
- Azure に用意されている DNS サーバーを使用する場合は、以降のセクションを参照して、引き続きトラブルシューティングを行います。  

> [!Tip]
> ソース環境の DNS レコードを手動で更新する場合は、プライベート リンク リソースの FQDN とそれに対応するプライベート IP アドレスで、オンプレミス アプライアンス上にある DNS の hosts ファイルを編集してください。 この方法は、テスト目的でのみ推奨されます。 <br/>  


### <a name="validate-the-private-dns-zone"></a>プライベート DNS ゾーンを検証する   
前のセクションで説明したように DNS 解決が機能していない場合は、プライベート DNS ゾーンに問題がある可能性があります。  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>必要なプライベート DNS ゾーン リソースが存在することを確認する  
Azure Migrate では、"privatelink" サブドメインに対応するプライベート DNS ゾーンも、リソースの種類ごとに既定で作成されます。 プライベート DNS ゾーンは、プライベート エンドポイントのリソース グループと同じ Azure リソース グループに作成されます。 Azure リソース グループには、次の形式のプライベート DNS ゾーン リソースが含まれている必要があります。
- privatelink.vaultcore.azure.net (キー コンテナー)
- privatelink.blob.core.windows.net (ストレージ アカウント)
- privatelink.siterecovery.windowsazure.com (Recovery Services コンテナー) (Hyper-V およびエージェントベースのレプリケーションの場合)
- privatelink.prod.migration.windowsazure.com (移行プロジェクト、評価プロジェクト、探索サイト)   

プライベート DNS ゾーンは、Azure Migrate によって自動的に作成されます (ユーザーが選択したキャッシュ ストレージ アカウントとレプリケーション ストレージ アカウントを除く)。 リンクされたプライベート DNS ゾーンは、プライベート エンドポイント ページに移動し、DNS 構成を選択することで特定できます。 プライベート DNS ゾーンは、[プライベート DNS 統合] セクションに表示されます。

![DNS 構成のスクリーンショット](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

DNS ゾーンが存在しない場合は (下図)、[新しいプライベート DNS ゾーン リソースを作成](../dns/private-dns-getstarted-portal.md)してください。  

![プライベート DNS ゾーンを作成する](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png)

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>プライベート DNS ゾーンが仮想ネットワークにリンクされていることを確認する  
リソース エンドポイントのプライベート IP アドレスを DNS クエリで解決するためには、プライベート エンドポイントを含んだ仮想ネットワークにプライベート DNS ゾーンがリンクされている必要があります。 プライベート DNS ゾーンが正しい仮想ネットワークにリンクされていない場合、その仮想ネットワークからの DNS 解決では、プライベート DNS ゾーンが無視されます。   

Azure portal でプライベート DNS ゾーン リソースに移動し、左側のメニューから [仮想ネットワーク リンク] を選択します。 リンクされている仮想ネットワークが表示されます。

![仮想ネットワーク リンクを表示する](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png)

これにより、リンクの一覧が表示されます。どれにも、自分のサブスクリプション内の仮想ネットワークの名前が表示されています。 プライベート エンドポイント リソースが含まれる仮想ネットワークが、この一覧に表示される必要があります。 そのようになっていない場合は、[この記事に従って](../dns/private-dns-getstarted-portal.md#link-the-virtual-network)、プライベート DNS ゾーンを仮想ネットワークにリンクしてください。    

プライベート DNS ゾーンを仮想ネットワークにリンクすると、その仮想ネットワークから発信された DNS 要求で、プライベート DNS ゾーン内の DNS レコードが検索されるようになります。 プライベート エンドポイントが作成された仮想ネットワークへのアドレス解決が正しく実行されるためには、これが必要です。   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>プライベート DNS ゾーンに正しい A レコードが含まれることを確認する

トラブルシューティングの対象となるプライベート DNS ゾーンに移動します。 [概要] ページには、そのプライベート DNS ゾーンのすべての DNS レコードが表示されます。 リソースの DNS A レコードが存在することを確認してください。 A レコードの値 (IP アドレス) は、リソースのプライベート IP アドレスであることが必要です。 A レコードが見つかっても、IP アドレスが正しくない場合は、間違った IP アドレスを削除して新しい IP アドレスを追加する必要があります。 A レコード全体を削除して新しいレコードを追加し、オンプレミスのソース アプライアンスに対して DNS フラッシュを実行することをお勧めします。   

プライベート DNS ゾーンにおけるストレージ アカウントの DNS A レコードの具体的な例を次に示します。

![DNS レコード](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

プライベート DNS ゾーンにおける Recovery Services コンテナーのマイクロサービスの DNS A レコードの具体的な例を次に示します。

![Recovery Services コンテナーの DNS レコード](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> A レコードを削除または変更しても、TTL (Time To Live) の値の有効期限がまだ切れていない可能性があるため、マシンが古い IP アドレスに解決される場合があります。  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>その他、プライベート リンクの接続に影響する可能性のある事柄  

以下、高度なシナリオまたは複雑なシナリオで想定される事柄をいくつか挙げてみます。

- ファイアウォールの設定。仮想ネットワークに接続されている Azure Firewall、またはアプライアンス マシンにデプロイされるカスタム ファイアウォール ソリューションが該当します。  
- ネットワーク ピアリング。使用される DNS サーバーと、トラフィックのルーティング方法に影響を与える可能性があります。  
- カスタム ゲートウェイ (NAT) ソリューション。DNS クエリからのトラフィックなど、トラフィックのルーティング方法に影響を与える可能性があります。

詳細については、[プライベート エンドポイント接続の問題に関するトラブルシューティング ガイド](../private-link/troubleshoot-private-endpoint-connectivity.md)を参照してください。  

## <a name="next-steps"></a>次のステップ
- [移行プロセスを完了](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration)し、[移行後のベスト プラクティス](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices)を確認してください。
