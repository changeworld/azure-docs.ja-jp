---
title: "Azure Migrate で Azure に移行するためにオンプレミスの VMware VM を検出して評価する | Microsoft Docs"
description: "Azure Migrate サービスを使って Azure に移行するためにオンプレミスの VMware VM を検出して評価する方法について説明します。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bbcfe95f5427681f8d55d647b102d35fc37f15ee
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="discover-and-assess-on-premises-vmware-vms-for-migration-to-azure"></a>Azure に移行するためにオンプレミスの VMware VM を検出して評価する

[Azure Migrate](migrate-overview.md) サービスは、Azure への移行についてオンプレミスのワークロードを評価します。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Migrate プロジェクトを作成します。
> * 評価のためにオンプレミスの VMware VM を検出するように、オンプレミスのコレクター仮想マシン (VM) をセットアップします。
> * VM をグループ化して、評価を作成します。


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件

- **VMware**: 移行する予定の VM は、バージョン 5.5、6.0、または 6.5 を実行している vCenter Server で管理する必要があります。 また、コレクター VM を展開するには、バージョン 5.0 以降を実行している 1 つの ESXi ホストが必要です。 
 
> [!NOTE]
> Hyper-V のサポートもロードマップにあり、近日中の実現を目指しています。 

- **vCenter Server アカウント**: vCenter Server にアクセスするには、読み取り専用アカウントが必要です。 Azure Migrate はこのアカウントを使ってオンプレミスの VM を検出します。
- **アクセス許可**: vCenter Server 上で、.OVA 形式でファイルをインポートして VM を作成するためのアクセス許可が必要です。 
- **統計情報の設定**: デプロイを始める前に、vCenter Server の統計設定をレベル 3 に設定する必要があります。 レベル 3 より低い場合は、評価は機能しますが、ストレージとネットワークのパフォーマンス データが収集されません。 この場合のサイズは、CPU とメモリのパフォーマンス データと、ディスクおよびネットワーク アダプターの構成データに基づいて勧められます。 

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする
[Azure Portal](https://portal.azure.com) にログインします。

## <a name="create-a-project"></a>プロジェクトの作成

1. Azure Portal で、**[リソースの作成]** をクリックします。
2. 「**Azure Migrate**」を検索し、検索結果でサービス **Azure Migrate (プレビュー)** を選択します。 **[Create]**をクリックします。
3. プロジェクト名およびプロジェクトの Azure サブスクリプションを指定します。
4. 新しいリソース グループを作成します。
5. プロジェクトを作成する場所を指定して、**[作成]** をクリックします。 このプレビューで Azure Migrate プロジェクトを作成できるのは、米国中西部リージョンだけです。 ただし、対象となる任意の Azure の場所について移行を計画することもできます。 プロジェクト用に指定された場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用します。 

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>コレクター アプライアンスをダウンロードする

Azure Migrate は、コレクター アプライアンスと呼ばれるオンプレミスの VM を作成します。 この VM はオンプレミスの VMware VM を検出し、それについてのメタデータを Azure Migrate サービスに送信します。 コレクター アプライアンスをセットアップするには、.OVA ファイルをダウンロードし、それをオンプレミスの vCenter サーバーにインポートして、VM を作成します。

1. Azure Migrate プロジェクトで、**[作業の開始]** > **[検出と評価]** > **[マシンの検出]** の順にクリックします。
2. **[マシンの検出]** で **[ダウンロード]** をクリックし、.OVA ファイルをダウンロードします。
3. **[プロジェクトの資格情報をコピーします]** で、プロジェクトの ID とキーをコピーします。 これらの情報はコレクターを構成するときに必要になります。

    ![.ova ファイルをダウンロードする](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>コレクター アプライアンスを確認する

.OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、OVA のハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成されたハッシュは、次の設定と一致する必要があります。
    
    OVA バージョン 1.0.8.49 の場合
    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | cefd96394198b92870d650c975dbf3b8 
    SHA1 | 4367a1801cf79104b8cd801e4d17b70596481d6f
    SHA256 | fda59f076f1d7bd3ebf53c53d1691cc140c7ed54261d0dc4ed0b14d7efef0ed9

    OVA バージョン 1.0.8.40 の場合:

    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>コレクター VM を作成する

ダウンロードしたファイルを vCenter Server にインポートします。

1. vSphere Client コンソールで、**[File]\(ファイル\)** > **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。

    ![OVF をデプロイする](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、.OVA ファイルの場所を指定します。
3. **[Name]\(名前\)** と **[Location]\(場所\)** で、コレクター VM のフレンドリ名と、VM がホストされるインベントリ オブジェクトを指定します。
5. **[Host/Cluster]\(ホスト/クラスター\)** で、コレクター VM が実行するホストまたはクラスターを指定します。
7. ストレージで、コレクター VM の保存先を指定します。
8. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
9. **[Network Mapping]\(ネットワーク マッピング\)** で、コレクター VM の接続先となるネットワークを指定します。 このネットワークには、Azure にメタデータを送信するためのインターネット接続が必要です。 
10. 設定を再確認したら、**[Finish]\(完了\)** をクリックします。

## <a name="run-the-collector-to-discover-vms"></a>コレクターを実行して VM を検出する

1. vSphere Client コンソールで、VM を右クリックして **[Open Console]\(コンソールを開く\)** を選びます。
2. アプライアンスの設定で、言語、タイム ゾーン、パスワードを指定します。
3. デスクトップにある **[Run collector]\(コレクターの実行\)** ショートカットをクリックします。
4. Azure Migrate Collector で、**[Set Up Prerequisites]\(前提条件の設定\)** を開きます。
    - ライセンス条項に同意し、サード パーティの情報を確認します。
    - VM がインターネットにアクセスできることをコレクターがチェックします。
    - VM がプロキシ経由でインターネットにアクセスしている場合は、**[Proxy settings]\(プロキシの設定\)** をクリックし、プロキシ アドレスとリスニング ポートを指定します。 プロキシで認証が必要な場合は、資格情報を指定します。

    > [!NOTE]
    > プロキシのアドレスを、http://ProxyIPAddress または http://ProxyFQDN の形式で入力する必要があります。 サポートされるのは HTTP プロキシのみです。

    - コレクター サービスが実行されていることをコレクターがチェックします。 このサービスは、既定でコレクター VM にインストールされています。
    - VMware PowerCLI をダウンロードしてインストールします。

5. **[Specify vCenter Server details]\(vCenter Server 詳細の指定\)** で、次の操作を行います。
    - vCenter サーバーの名前 (FQDN) または IP アドレスを指定します。
    - **[ユーザー名]** と **[パスワード]** で、コレクターが vCenter サーバーの VM を検出するために使用する読み取り専用の資格情報を指定します。
    - **[コレクション スコープ]** で、VM 検出のスコープを選択します。 コレクターは、指定されたスコープ内の VM のみを検出できます。 スコープは、指定のフォルダー、データセンター、またはクラスターに設定できます。 VM の数は 1000 台を超えないようにします。 

6. **[Specify migration project]\(移行プロジェクトの指定\)** で、ポータルからコピーした Azure Migrate プロジェクトの ID とキーを指定します。 ID とキーをコピーしなかった場合は、コレクター VM から Azure Portal を開きます。 プロジェクトの **[概要]** ページで、**[マシンの検出]** をクリックして値をコピーします。  
7. **[View collection progress]\(収集の進行状況の表示\)** で検出を監視し、VM から収集されたメタデータがスコープ内にあることを確認します。 コレクターがおおよその検出時間を表示します。

> [!NOTE]
> コレクターは、オペレーティング システムの言語およびコレクター インターフェイスの言語として、"英語 (米国)" のみをサポートしています。 他の言語は将来サポートされるようになります。


### <a name="verify-vms-in-the-portal"></a>ポータル内の VM を確認する

検出時間は検出している VM の数によって異なります。 通常、VM が 100 台の場合、コレクターが実行を終了した後、検出が完了するまで約 1 時間かかります。 

1. Azure Migrate プロジェクト内で、**[管理]** > **[マシン]** の順にクリックします。
2. 検出対象の VM がポータルに表示されていることを確認します。


## <a name="create-and-view-an-assessment"></a>評価を作成して表示する

VM が検出された後、それらをグループ化して、評価を作成します。 

1. プロジェクトの **[概要]** ページで、**[+ 評価の作成]** をクリックします。
2. **[すべて表示]** をクリックして、評価のプロパティを確認します。
3. グループを作成し、グループ名を指定します。
4. グループに追加するマシンを選びます。
5. **[評価の作成]** をクリックして、グループと評価を作成します。
6. 評価が作成された後、**[概要]** > **[ダッシュボード]** でそれを表示します。
7. **[評価のエクスポート]** をクリックし、Excel ファイルとしてダウンロードします。

### <a name="sample-assessment"></a>評価のサンプル

評価レポートの例を次に示します。 VM が Azure と互換性があるかどうか、および毎月の推定コストに関する情報が含まれます。 

![評価レポート](./media/tutorial-assessment-vmware/assessment-report.png)

#### <a name="azure-readiness"></a>Azure の準備状況

このビューには、各マシンの準備状態が表示されます。

- 準備ができている VM の場合、Azure Migrate は Azure での VM のサイズを推奨します。
- 準備ができていない VM の場合、Azure Migrate は理由を説明し、修復の手順を示します。
- Azure Migrate は、移行に使うことができるツールを提案します。 マシンがリフト アンド シフト方式の移行に適している場合は、Azure Site Recovery サービスが推奨されます。 データベース マシンのある場合は、Azure Database Migration Service が推奨されます。

  ![評価の準備状態](./media/tutorial-assessment-vmware/assessment-suitability.png)  

#### <a name="monthly-cost-estimate"></a>月額料金の見積もり

このビューには、Azure で VM を実行する合計計算コストとストレージ コストと、各マシンの詳細が表示されます。 コストの見積もりは、マシンのパフォーマンスに基づく推奨サイズとそのディスク数、および評価のプロパティを使って計算されます。 

> [!NOTE]
> Azure Migrate に提供されているコスト見積もりは、サービスとしての Azure インフラストラクチャ (IaaS) VM としてオンプレミスの VM を実行するために用意されています。 Azure Migrate では、サービスとしてのプラットホーム (PaaS) のコスト、またはサービスとしてのソフトウェア (SaaS) のコストは考慮されません。 

コンピューティングとストレージの見積もり月額コストが、グループ内の全 VM について集計されます。 

![評価の VM コスト](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

ドリルダウンして、特定のマシンの詳細を見ることができます。

![評価の VM コスト](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>次の手順

- 大規模な VMware 環境の検出と評価の方法を[確認](how-to-scale-assessment.md)します。
- [マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を使って、信頼度の高い評価グループを作成する方法を確認します。
- 評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
