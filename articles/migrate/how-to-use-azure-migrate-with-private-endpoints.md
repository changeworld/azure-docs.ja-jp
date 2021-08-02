---
title: プライベート エンドポイントで Azure Migrate を使用する
description: Azure Migrate のプライベート リンクのサポートを利用して、プライベート リンクを使った検出、評価、移行を行います。
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/10/2020
ms.openlocfilehash: 85202474e8fd3c4340357b99ae91ee7d25d79ebf
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111747187"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>プライベート エンドポイントで Azure Migrate を使用する  

この記事では、[Azure Private Link](../private-link/private-endpoint-overview.md) を使用したプライベート ネットワーク上で、Azure Migrate を使用してサーバーの検出、評価、移行を行う方法について説明します。

Azure Private Link を使用すると、ExpressRoute のプライベート ピアリングまたはサイト間 VPN 接続で [Azure Migrate: Discovery and Assessment](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) および [Azure Migrate: Server Migration](./migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用して、Azure Migrate サービスにプライベートかつ安全に接続することができます。

プライベート エンドポイントの接続方法は、公衆ネットワークを経由することなく Azure Migrate サービスやその他の Azure リソースにアクセスするという組織的な要件がある場合に推奨されます。 プライベート リンクを使用し、既にある ExpressRoute プライベート ピアリング回線を使用すると、帯域幅や待機時間の要件を満たしやすくなります。

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


Azure Migrate プロジェクトの公衆ネットワーク アクセスを有効にするには、Azure portal にサインインし、Azure portal で **Azure Migrate のプロパティ** ページに移動し、 **[いいえ]**  >  **[保存]** を選択します。

![ネットワーク アクセス モードの変更方法を示す図](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>その他の考慮事項   

**考慮事項** | **詳細**
--- | ---
**料金** | 価格情報については、[Azure BLOB の価格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)に関するページと「[Azure Private Link の価格](https://azure.microsoft.com/pricing/details/private-link/)」を参照してください。  
**仮想ネットワークの要件** | 選択した仮想ネットワーク内またはそれに接続された仮想ネットワーク内に、ExpressRoute または VPN Gateway のエンドポイントが存在する必要があります。 仮想ネットワークには約 15 個の IP アドレスが必要となる場合があります。  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>プライベート エンドポイント接続を使用してプロジェクトを作成する

こちらの[記事](./create-manage-projects.md#create-a-project-for-the-first-time)を参照して、新しい Azure Migrate プロジェクトを設定します。

> [!Note]
> 既存の Azure Migrate プロジェクトの接続方法をプライベート エンドポイント接続に変更することはできません。

Azure Migrate プロジェクトのプライベート エンドポイントを作成するには、 **[詳細]** 構成セクションに以下の情報を入力します。
1. **[接続方法]** で **[プライベート エンドポイント]** を選択します。
2. **[Disable public endpoint access]\(パブリック エンドポイント アクセスを無効にする\)** では、既定の設定である **[いいえ]** のままにします。 移行ツールによっては、公衆ネットワーク アクセスが無効になっていると、利用状況データを Azure Migrate プロジェクトにアップロードできない場合があります。 [詳細情報。](#other-integrated-tools)
3. **[仮想ネットワークのサブスクリプション]** で、プライベート エンドポイントの仮想ネットワークのサブスクリプションを選択します。
4. **[仮想ネットワーク]** で、プライベート エンドポイントの仮想ネットワークを選択します。 Azure Migrate プロジェクトに接続する必要のある Azure Migrate アプライアンスおよびその他のソフトウェア コンポーネントは、このネットワークまたは接続された仮想ネットワーク上に存在する必要があります。
5. **[サブネット]** で、プライベート エンドポイントのサブネットを選択します。

   ![Create project](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

6. **[作成]** を選択します。 移行プロジェクトが作成され、それにプライベート エンドポイントがアタッチされます。 Azure Migrate プロジェクトがデプロイされるまで数分待ちます。 プロジェクトの作成中にこのページを閉じないでください。

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Azure Private Link を使用して移行するサーバーを検出して評価する

### <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスを設定する

1. **[マシンの検出]**  >  **[お使いのマシンは仮想化されていますか?]** で、サーバーの種類を選択します。
2. **[Azure Migrate プロジェクト キーを生成します]** で、Azure Migrate アプライアンスの名前を入力します。
3. **[キーの生成]** を選択して、必要な Azure リソースを作成します。

    > [!Important]
    > リソースの作成中に [マシンの検出] ページを閉じないでください。  
    - この手順では、キー コンテナー、ストレージ アカウント、Recovery Services コンテナー (エージェントレス VMware 移行の場合のみ) のほか、いくつかの内部リソースが Azure Migrate によって作成され、各リソースにプライベート エンドポイントがアタッチされます。 プライベート エンドポイントは、プロジェクトの作成時に選択した仮想ネットワーク内に作成されます。  
    - プライベート エンドポイントが作成されると、Azure Migrate リソース用の DNS CNAME リソース レコードが、*privatelink* というプレフィックスが付いたサブドメイン内のエイリアスに更新されます。 また、既定では、Azure Migrate によって *privatelink* サブドメインに対応するプライベート DNS ゾーンがリソースの種類ごとに作成され、関連付けられたプライベート エンドポイントの DNS A レコードが挿入されます。 これにより、ソース ネットワークに存在する Azure Migrate アプライアンスおよびその他のソフトウェア コンポーネントは、プライベート IP アドレス上にある Azure Migrate リソースのエンドポイントに到達できるようになります。  
    - また、Azure Migrate は、移行プロジェクトの[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を有効にし、ストレージ アカウントに安全にアクセスするためのアクセス許可をそのマネージド ID に付与します。  

4. キーが正常に生成されたら、そのキーの詳細をコピーして、アプライアンスの構成と登録を行います。   

#### <a name="download-the-appliance-installer-file"></a>アプライアンスのインストーラー ファイルをダウンロードする  

Azure Migrate の検出および評価では、軽量の Azure Migrate アプライアンスを使用します。 このアプライアンスによって、サーバー検出が実行され、サーバーの構成とパフォーマンス メタデータが Azure Migrate に送信されます。  

> [!Note]
> テンプレートを使用してアプライアンスを展開するオプション (VMware 環境のサーバーと VHD の Hyper-V 環境の OVA) は、プライベート エンドポイント接続を使用した Azure Migrate プロジェクトではサポートされていません。

アプライアンスを設定するには:
  1. インストーラー スクリプトを含んだ ZIP ファイルをポータルからダウンロードします。
  2. アプライアンスのホストとなるサーバー上に ZIP ファイルをコピーします。   
  3. zip ファイルをダウンロードした後、ファイルのセキュリティを確認します。
  4. インストーラー スクリプトを実行して、アプライアンスを展開します。

各シナリオのダウンロード リンクは次のとおりです。

シナリオ | ダウンロード リンク | ハッシュ値
--- | --- | ---
Hyper-V | [AzureMigrateInstaller-HyperV-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160557) | CBF8927AF137A106E2A34AC4F77CFFCB1CD96873C592E1DF37BC5606254989EC
物理 | [AzureMigrateInstaller-Physical-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160558) | 1CB967D92096EB48E4C3C809097F52C8341FC7CA7607CF840C529E7A21B1A21D
VMware | [AzureMigrateInstaller-VMware-public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160648) | 0A4FCC4D1500442C5EB35E4095EF781CB17E8ECFE8E4F8C859E65231E00BB154
VMware スケールアウト | [AzureMigrateInstaller-VMware-Public-Scaleout-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160811) | 2F035D34E982EE507EAEC59148FDA8327A45D2A845B4A95475EC6D2469D72D28

#### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します

    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例 (パブリック クラウドの場合): ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink.zip SHA256 ```

3.  前出の表のハッシュ値を比較して、アプライアンスの最新バージョンを検証します。

選択したシナリオ (VMware、Hyper-V、物理など) の[ハードウェア要件](./migrate-appliance.md)をサーバーが満たしていること、また、[必要な URL](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) に接続できることを確認します。


#### <a name="run-the-script"></a>スクリプトを実行する

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。
2. （昇格した）管理者権限を使用し、マシンで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルから抽出したコンテンツが格納されたフォルダーに変更します。
4. 次のように、スクリプト **AzureMigrateInstaller.ps1** を実行します。

    ```
    PS C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink> .\AzureMigrateInstaller.ps1
    ```

5. スクリプトが正常に実行されると、アプライアンスの構成マネージャーが起動し、アプライアンスを構成できるようになります。 問題が発生した場合は、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log で、スクリプトログを確認してください。

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>アプライアンスを構成して継続的な検出を開始する

アプライアンス サーバーに接続できる任意のマシン上でブラウザーを開き、アプライアンス構成マネージャーの URL (`https://appliance name or IP address: 44368`) を開きます。 または、構成マネージャーのショートカットを選択し、アプライアンス サーバーのデスクトップから構成マネージャーを開くこともできます。

#### <a name="set-up-prerequisites"></a>前提条件の設定

1. サードパーティの情報を確認し、**ライセンス条項** に同意します。    

2. 構成マネージャー > **[前提条件のセットアップ]** で、以下を実行します。
   - **接続**: 必要な URL へのアクセスがアプライアンスによってチェックされます。 サーバーでプロキシを使用する場合は、次の操作を行います。
     - **[プロキシの設定]** を選択し、プロキシ アドレス (`http://ProxyIPAddress` または `http://ProxyFQDN`) とリッスン ポートを指定します。
     - プロキシで認証が必要な場合は、資格情報を指定します。 サポートされるのは HTTP プロキシのみです。
     - プロキシ サーバーをバイパスする URL または IP アドレスのリストを追加できます。
     - プロキシ サーバーの詳細を更新した場合やプロキシをバイパスするために URL または IP アドレスを追加した場合は、 **[保存]** を選択して構成を登録します。

        > [!Note]
        > 接続チェック中に aka.ms/* リンクでエラーが発生し、アプライアンスからインターネット経由でこの URL にアクセスできないようにしたい場合は、[**こちら**](./migrate-appliance.md#turn-off-auto-update)の手順に従って、アプライアンスの自動更新サービスを無効にする必要があります。 自動更新を無効にすると、aka.ms/* URL の接続チェックがスキップされます。

   - **時刻同期**:検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
   - **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** を選択して、アプライアンス サーバーで実行されているサービスの状態とバージョンを確認します。
        > [!Note]
        > アプライアンスの自動更新サービスを無効にした場合は、[**こちら**](./migrate-appliance.md#manually-update-an-older-version)の手順に従って、アプライアンスのサービスを手動で最新版に更新することができます。
   - **VDDK のインストール**: ("_VMware アプライアンスでのみ必要_") VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが、アプライアンスによってチェックされます。 インストールされていない場合は、**インストール手順** に記載されているとおりに、VMware から VDDK 6.7 をダウンロードし、ダウンロードした zip コンテンツをアプライアンス上の指定された場所に抽出します。

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>アプライアンスを登録して継続的な検出を開始する

前提条件の確認が完了したら、手順に従ってアプライアンスを登録し、各シナリオの継続的な検出を開始します。
- [VMware VM](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate)
- [Hyper-V VM](./tutorial-discover-hyper-v.md#register-the-appliance-with-azure-migrate)
- [物理サーバー](./tutorial-discover-physical.md#register-the-appliance-with-azure-migrate)
- [AWS VM](./tutorial-discover-aws.md#register-the-appliance-with-azure-migrate)
- [GCP VM](./tutorial-discover-gcp.md#register-the-appliance-with-azure-migrate)


>[!Note]
> アプライアンスの登録中や検出の開始時に DNS 解決の問題が発生した場合は、Azure Migrate アプライアンスをホストするオンプレミス サーバーから、ポータルの **[キーの生成]** ステップで作成した Azure Migrate リソースに到達できることを確認してください。 [ネットワーク接続の確認方法の詳細を参照してください](./troubleshoot-network-connectivity.md)。

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
    - 既定では、Azure Migrate によって自動的にプライベート DNS ゾーンが作成され、Recovery Services コンテナーのマイクロサービス用に DNS A レコードが追加されます。 プライベート DNS ゾーンは、プライベート エンドポイント仮想ネットワークにリンクされ、これを利用してオンプレミスのレプリケーション アプライアンスが完全修飾ドメイン名をプライベート IP アドレスに解決することができます。

4. レプリケーション アプライアンスを登録する前に、レプリケーション アプライアンスをホストするマシンからコンテナーのプライベート リンク FQDN に到達できることを確認します。 [ネットワーク接続の確認方法の詳細を参照してください](./troubleshoot-network-connectivity.md)。

5. 接続を確認したら、アプライアンスのセットアップおよびキー ファイルをダウンロードし、インストール プロセスを実行して、Azure Migrate にそのアプライアンスを登録します。 [こちらの詳細な手順](./tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)を確認してください。 レプリケーション アプライアンスの設定後、これらの手順に従って、移行対象のマシンに[モビリティ サービスをインストール](./tutorial-migrate-physical-virtual-machines.md#install-the-mobility-service)します。

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Azure Private Link を使用して Azure にサーバーをレプリケートする

[これらの手順](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)に従って、レプリケーションの対象となるサーバーを選択します。  

**[レプリケート]**  >  **[ターゲット設定]**  >  **[キャッシュ/レプリケーション ストレージ アカウント]** のドロップダウンを使用して、プライベート リンク経由でレプリケートするためのストレージ アカウントを選択します。  

Azure Migrate プロジェクトにプライベート エンドポイント接続がある場合は、Azure Migrate に必要なストレージ アカウントにアクセスするための[アクセス許可を Recovery Services コンテナーのマネージド ID に付与](#grant-access-permissions-to-the-recovery-services-vault)する必要があります。   

加えて、プライベート リンクを介したレプリケーションを有効にするために、[ストレージ アカウント用のプライベート エンドポイントを作成](#create-a-private-endpoint-for-the-storage-account-optional)します。

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Recovery Services コンテナーにアクセス許可を付与する

キャッシュ レプリケーション ストレージ アカウントまたはレプリケーション ストレージ アカウントに対する認証済みアクセスのために、Recovery Services コンテナーにアクセス許可を付与する必要があります。

Azure Migrate によって作成された Recovery Services コンテナーを特定し、必要なアクセス許可を付与するには、次の手順を実行します。

"**_Recovery Services コンテナーとマネージド ID のオブジェクト ID を特定する_**"

Recovery Services コンテナーの詳細は、Azure Migrate: Server Migration の **[プロパティ]** ページで確認できます。

1. **Azure Migrate ハブ** に移動し、Azure Migrate: Server Migration タイルの **[概要]** を選択します。

    ![Azure Migrate ハブの [概要] ページ](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. 左側のペインで、 **[プロパティ]** を選択します。 Recovery Services コンテナーの名前とマネージド ID をメモします。 コンテナーの **[接続の種類]** は "_プライベート エンドポイント_" に、 **[レプリケーションの種類]** は "_その他_" になります。 コンテナーにアクセス権を提供する際に、この情報が必要になります。

    ![Azure Migrate: Server Migration の [プロパティ] ページ](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_ストレージ アカウントにアクセスするためのアクセス許可_**

 コンテナーのマネージド ID には、レプリケーションに必要なストレージ アカウントに対する以下のロールのアクセス許可を付与する必要があります。  この場合は、ストレージ アカウントを事前に作成しておく必要があります。

>[!Note]
> プライベート リンクを使用して Hyper-V VM を Azure に移行する場合は、レプリケーション ストレージ アカウントとキャッシュ ストレージ アカウントの両方に対するアクセス権を付与する必要があります。

Resource Manager のロールのアクセス許可は、ストレージ アカウントの種類によって異なります。

|**ストレージ アカウントの種類** | **ロールのアクセス許可**|
|--- | ---|
|Standard の種類 | [Contributor](../role-based-access-control/built-in-roles.md#contributor)<br>[ストレージ BLOB データ共同作成者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|
|Premium の種類 | [Contributor](../role-based-access-control/built-in-roles.md#contributor)<br>[ストレージ BLOB データ所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

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

ストレージ アカウント用のプライベート エンドポイントは、Azure Migrate プロジェクトのプライベート エンドポイントと同じ仮想ネットワーク内、またはそのネットワークに接続されている別の仮想ネットワーク内に作成します。

**[はい]** を選択して、プライベート DNS ゾーンと統合します。 プライベート DNS ゾーンは、プライベート リンク上で、仮想ネットワークからストレージ アカウントに接続をルーティングする際に利用されます。 **[はい]** を選択すると、仮想ネットワークに DNS ゾーンが自動的にリンクされ、作成された新しい IP と完全修飾ドメイン名の解決に必要な DNS レコードが追加されます。 詳細については、[プライベート DNS ゾーン](../dns/private-dns-overview.md)をご覧ください。

プライベート エンドポイントを作成しているユーザーがストレージ アカウント所有者でもある場合、プライベート エンドポイントの作成は自動承認されます。 そうでない場合、ストレージ アカウントの所有者がプライベート エンドポイントの使用を承認する必要があります。 要求されたプライベート エンドポイント接続を承認または拒否するには、ストレージ アカウント ページの **[ネットワーク]** にある **[プライベート エンドポイント接続]** に移動します。

先に進む前に、プライベート エンドポイントの接続状態を確認します。

![プライベート エンドポイントの承認状態](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

プライベート エンドポイントの作成後、 **[レプリケート]**  >  **[ターゲット設定]**  >  **[キャッシュ ストレージ アカウント]** のドロップダウンを使用して、プライベート リンクでレプリケートするためのストレージ アカウントを選択します。  

オンプレミスのレプリケーション アプライアンスのプライベート エンドポイントに、ストレージ アカウントへのネットワーク接続があることを確認します。 [ネットワーク接続の確認方法の詳細を参照してください](./troubleshoot-network-connectivity.md)。

>[!Note]
>
> - Hyper-V VM を Azure に移行するケースで、レプリケーション ストレージ アカウントの種類が _Premium_ である場合、キャッシュ ストレージ アカウントには、_Standard_ タイプの別のストレージ アカウントを選択する必要があります。 このケースでは、レプリケーション ストレージ アカウントとキャッシュ ストレージ アカウントの両方のプライベート エンドポイントを作成する必要があります。  

その後、[レプリケーションの確認と開始](./tutorial-migrate-physical-virtual-machines.md#replicate-machines)および[移行の実行](./tutorial-migrate-physical-virtual-machines.md#run-a-test-migration)に関する手順に従ってください。


## <a name="next-steps"></a>次のステップ
- [移行プロセスを完了](./tutorial-migrate-physical-virtual-machines.md#complete-the-migration)し、[移行後のベスト プラクティス](./tutorial-migrate-physical-virtual-machines.md#post-migration-best-practices)を確認してください。
