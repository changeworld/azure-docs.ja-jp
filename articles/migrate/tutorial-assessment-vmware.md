---
title: "Azure Migrate で Azure に移行するためにオンプレミスの VMware VM を検出して評価する | Microsoft Docs"
description: "Azure Migrate サービスを使って Azure に移行するためにオンプレミスの VMware VM を検出して評価する方法について説明します。"
services: migration-planner
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a2521630-730f-4d8b-b298-e459abdced46
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: 1c21364c3ff5cfb61866c912a699b722f2668607
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
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

- **VMware**: 少なくとも 1 つの VMware VM が、ESXi ホスト上、またはバージョン 5.0 以降を実行しているクラスター上に配置されている必要があります。 ホストまたはクラスターが、バージョン 5.5、6.0、または 6.5 を実行する vCenter サーバーによって管理されている必要があります。
- **vCenter アカウント**: vCenter サーバーの管理者の資格情報と、読み取り専用のアカウントが必要です。 Azure Migrate はこのアカウントを使って VM を検出します。
- **アクセス許可**: vCenter サーバー上で、.OVA 形式でファイルをインポートして VM を作成するためのアクセス許可が必要です。 
- **統計情報の設定**: デプロイを始める前に、vCenter サーバーの統計設定をレベル 3 に設定する必要があります。 レベル 3 より低い場合は、評価は機能しますが、ストレージとネットワークのパフォーマンス データが収集されません。

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする
[Azure Portal](https://portal.azure.com) にログインします。

## <a name="create-a-project"></a>プロジェクトの作成

1. Azure Portal で、**[リソースの作成]** をクリックします。
2. 「**Azure Migrate**」を検索し、検索結果でサービス (**Azure Migrate (プレビュー)**) を選びます。 **[Create]**をクリックします。
3. プロジェクト名およびプロジェクトの Azure サブスクリプションを指定します。
4. 新しいリソース グループを作成します。
5. プロジェクトを作成するリージョンを指定して、**[作成]** をクリックします。 オンプレミスの VM から収集されたメタデータが、このリージョンに保存されます。 このプレビューで Azure Migrate プロジェクトを作成できるのは、米国中西部リージョンだけです。 ただし、別の場所の VM を評価することはできます。

    ![Azure Migrate](./media/tutorial-assessment-vmware/project-1.png)
    


## <a name="download-the-collector-appliance"></a>コレクター アプライアンスをダウンロードする

Azure Migrate は、コレクター アプライアンスと呼ばれるオンプレミスの VM を作成します。 この VM はオンプレミスの VMware VM を検出し、それについてのメタデータを Azure Migrate サービスに送信します。 コレクター アプライアンスをセットアップするには、.OVA ファイルをダウンロードし、それをオンプレミスの vCenter サーバーにインポートして、VM を作成します。

1. Azure Migrate プロジェクトで、**[作業の開始]** > **[検出と評価]** > **[マシンの検出]** の順にクリックします。
2. **[マシンの検出]** で **[ダウンロード]** をクリックし、.OVA ファイルをダウンロードします。
3. **[プロジェクトの資格情報をコピーします]** で、プロジェクトの ID とキーをコピーします。 これらの情報はコレクターを構成するときに必要になります。

    ![.ova ファイルをダウンロードする](./media/tutorial-assessment-vmware/download-ova.png)

### <a name="verify-the-collector-appliance"></a>コレクター アプライアンスを確認する

.OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、OVA のハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. 生成されたハッシュは、次の設定と一致する必要があります。
    
    OVA バージョン 1.0.8.38 の場合
    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | dd27dd6ace28f9195a2b5d52a4003067 
    SHA1 | d2349e06a5d4693fc2a1c0619591b9e45c36d695
    SHA256 | 1492a0c6d6ef76e79269d5cd6f6a22f336341e1accbc9e3dfa5dad3049be6798

    OVA バージョン 1.0.8.40 の場合
    **アルゴリズム** | **ハッシュ値**
    --- | ---
    MD5 | afbae5a2e7142829659c21fd8a9def3f
    SHA1 | 1751849c1d709cdaef0b02a7350834a754b0e71d
    SHA256 | d093a940aebf6afdc6f616626049e97b1f9f70742a094511277c5f59eacc41ad

## <a name="create-the-collector-vm"></a>コレクター VM を作成する

ダウンロードしたファイルを vCenter サーバーにインポートします。

1. vSphere Client コンソールで、**[File]\(ファイル\)** > **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。

    ![OVF をデプロイする](./media/tutorial-assessment-vmware/vcenter-wizard.png)

2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、.OVA ファイルの場所を指定します。
3. **[Name]\(名前\)** と **[Location]\(場所\)** で、コレクター VM のフレンドリ名と、VM がホストされるインベントリ オブジェクトを指定します。
5. **[Host/Cluster]\(ホスト/クラスター\)** で、コレクター VM が実行するホストまたはクラスターを指定します。
7. ストレージで、コレクター VM の保存先を指定します。
8. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
9. **[Network Mapping]\(ネットワーク マッピング\)** で、コレクター VM の接続先のネットワークを指定します。 このネットワークには、Azure にメタデータを送信するためのインターネット接続が必要です。 
10. 設定を再確認したら、**[Finish]\(完了\)** をクリックします。

## <a name="run-the-collector-to-discover-vms"></a>コレクターを実行して VM を検出する

1. vSphere Client コンソールで、VM を右クリックして **[Open Console]\(コンソールを開く\)** を選びます。
2. アプライアンスの設定で、言語、タイム ゾーン、パスワードを指定します。
3. デスクトップにある **[コレクターの実行]** ショートカットをクリックします。
4. Azure Migrate Collector で、**[Set Up Prerequisites]\(前提条件の設定\)** を開きます。
    - ライセンス条項に同意し、サード パーティーの情報を確認します。
    - VM がインターネットにアクセスできることをコレクターがチェックします。
    - VM がプロキシ経由でインターネットにアクセスしている場合は、**[Proxy settings]\(プロキシの設定\)** をクリックし、プロキシ アドレスとリスニング ポートを指定します。 プロキシで認証が必要な場合は、資格情報を指定します。
    - Windows プロファイラー サービスが実行されていることをコレクターがチェックします。 このサービスは、既定でコレクター VM にインストールされています。
    - VMware PowerCLI をダウンロードしてインストールします。
が必要です。 **[マシンの検出]** で、次の操作を行います。
    - vCenter サーバーの名前 (FQDN) または IP アドレスを指定します。
    - **[ユーザー名]** と **[パスワード]** で、コレクターが vCenter サーバーの VM を検出するために使用する読み取り専用の資格情報を指定します。
    - **[コレクション スコープ]** で、VM 検出のスコープを選択します。 コレクターは、指定されたスコープ内の VM のみを検出できます。 スコープは、特定のフォルダー、データセンター、またはクラスターに設定できます。 VM の数は 1000 個を超えないようにします。 
    - **[Tag category for grouping]\(グループ化のためのタグ カテゴリ\)** で、**[None]\(なし\)** を選択します。
1. **[Select Project]\(プロジェクトの選択\)** で、ポータルからコピーした Azure Migrate プロジェクトの ID とキーを指定します。 ID とキーをコピーしなかった場合は、コレクター VM から Azure Portal を開きます。 プロジェクトの **[概要]** ページで、**[マシンの検出]** をクリックして値をコピーします。  
2. **[Complete Discovery]\(検出の完了\)** で検出を監視し、VM から収集されたメタデータがスコープ内にあることを確認します。 コレクターがおおよその検出時間を表示します。

> [!NOTE]
> コレクターは、オペレーティング システムの言語およびコレクター インターフェイスの言語として、"英語 (米国)" のみをサポートしています。 他の言語は将来サポートされるようになります。


### <a name="verify-vms-in-the-portal"></a>ポータル内の VM を確認する

検出時間は検出している VM の数によって異なります。 通常、VM が 100 個の場合、コレクターが実行を終了したあと検出が完了するまで約 1 時間かかります。 

1. Migration Planner プロジェクト内で、**[管理]** > **[マシン]** の順にクリックします。
2. 検出対象の VM がポータルに表示されていることを確認します。


## <a name="create-and-view-an-assessment"></a>評価を作成して表示する

VM が検出された後、それらをグループ化して、評価を作成します。 

1. プロジェクトの **[概要]** ページで、**[+ 評価の作成]** をクリックします。
2. **[すべて表示]** をクリックして、評価の設定を確認します。
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

このビューには、各マシンのコンピューティングとストレージのコストが表示されます。 コストの見積もりは、マシンのパフォーマンスに基づく推奨サイズとそのディスク数、および評価のプロパティを使って計算されます。

コンピューティングとストレージの見積もり月額コストが、グループ内の全 VM について集計されます。 各マシンをクリックして、詳細を見ることができます。 

![評価の VM コスト](./media/tutorial-assessment-vmware/assessment-vm-cost.png) 

ドリルダウンして、特定のマシンのコストを見ることができます。

![評価の VM コスト](./media/tutorial-assessment-vmware/assessment-vm-drill.png) 

## <a name="next-steps"></a>次のステップ

- 多数のオンプレミスのマシンの評価をセットアップする方法を[学習](how-to-scale-assessment.md)します。
- [マシンの依存関係マッピング](how-to-create-group-machine-dependencies.md)を使って、より詳細な評価グループを作成する方法を学習します。
- 評価の計算方法の[詳細を学習](concepts-assessment-calculation.md)します。
