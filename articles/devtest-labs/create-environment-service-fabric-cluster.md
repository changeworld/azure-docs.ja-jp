---
title: Service Fabric クラスター環境を作成する
description: 自己完結型 Service Fabric クラスターを使用して環境を作成する方法について説明します。 スケジュールを使用してクラスターを開始および停止する方法を確認します。
author: EMaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: c9c7eec0d5c909285e4f7eb8768dbb300c86822e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325279"
---
# <a name="create-an-environment-with-self-contained-service-fabric-cluster-in-azure-devtest-labs"></a>Azure DevTest Labs で Service Fabric クラスターがある環境を作成する
この記事では、Azure DevTest Labs で自己完結型 Service Fabric クラスターがある環境を作成する方法について説明します。 

## <a name="overview"></a>概要
DevTest Labs では、Azure Resource Manager テンプレートに定義されているように自己完結型テスト環境を作成できます。 これらの環境には、IaaS リソース (仮想マシンなど) と PaaS リソース (Service Fabric など) の両方が含まれます。 DevTest Labs では、仮想マシンを制御するためのコマンドを提供することで、環境内の仮想マシンを管理できます。 これらのコマンドによって、スケジュールに従って、仮想マシンを開始または停止できます。 同様に、DevTest Labs は、環境内の Service Fabric クラスターを管理するために役立てることもできます。 環境内の Service Fabric クラスターは、手動で、またはスケジュールを使用して、開始または停止できます。

## <a name="create-a-service-fabric-cluster"></a>Service Fabric クラスターの作成
Service Fabric クラスターは、DevTest Labs の環境を使用して作成されます。 各環境は、Git リポジトリの Azure Resource Manager テンプレートによって定義されます。 DevTest Labs 用の[パブリック Git リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments/)では、[ServiceFabric-Cluster](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster) フォルダー内に Service Fabric クラスターを作成するための Resource Manager テンプレートが含まれています。 

1. 最初に、[ラボの作成](devtest-lab-create-lab.md)に関する記事の手順に従って、Azure DevTest Labs でラボを作成します。 **[パブリック環境]** オプションは既定で **[オン]** になっていることに注意してください。 
2. 次の手順に従って、自分のサブスクリプションに Service Fabric プロバイダーが登録されていることを確認します。
    1. 左側のナビゲーション メニューで **[サブスクリプション]** を選択し、自分の **サブスクリプション** を選択します。
    2. **[サブスクリプション]** ページで、左側のメニューの **[設定]** セクションから **[リソース プロバイダー]** を選択します。 
    3. **Microsoft.ServiecFabric** が登録されていない場合は、**[登録]** を選択します。 
3. ラボの **DevTest Lab** ページで、ツール バーの **+ 追加** を選びます。 
    
    ![ツールバーの [追加] ボタンを示すスクリーンショット。](./media/create-environment-service-fabric-cluster/add-button.png)
3. **[ベースの選択]** ページで、一覧から **[Service Fabric Lab クラスター]** を選択します。 

    ![一覧からの Service Fabric Lab クラスターの選択を示すスクリーンショット。](./media/create-environment-service-fabric-cluster/select-service-fabric-cluster.png)
4. **[設定の構成]** ページで、次の手順に従います。 
    1. クラスター **環境** の **名前** を指定します。これは、Service Fabric クラスターの Azure リソース グループです。 
    2. クラスターの仮想マシンの **オペレーティング システム (OS)** を選択します。 既定値は **Windows** です。
    3. クラスターの **管理者** の名前を指定します。 
    4. 管理者の **パスワード** を指定します。 
    5. **[証明書]** には、証明書の情報を Base64 でエンコードされた文字列として入力します。 証明書ファイルを作成するには、次の手順を実行します。
        1. [Git リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments/ServiceFabric-LabCluster)から **Create-ClusterCertificate.ps1** ファイルをダウンロードします。 または、マシン上にリポジトリをクローンします。 
        2. **PowerShell** を起動します。 
        3. `.\Create-ClusterCertificate.ps1` コマンドを使用して **ps1** ファイルを実行します。 メモ帳にテキスト ファイルが開き、このページの証明書関連フィールドに入力するために必要な情報が表示されます
    6. **証明書のパスワード** を入力します。
    7. 証明書の **サムプリント** を指定します。
    8. **[設定の構成]** ページで **[追加]** を選択します。 

        ![クラスター設定の構成を示すスクリーンショット。](./media/create-environment-service-fabric-cluster/configure-settings.png)
5. クラスターが作成されると、前の手順で指定した環境名を持つリソース グループが表示されます。 展開すると、その中の Service Fabric クラスターが表示されます。 リソース グループの状態が **[作成中]** のままで先に進まない場合は、ツールバーの **[更新]** を選択します。 **Service Fabric クラスター** 環境では、Linux または Windows のいずれかで、5 つのノードと 1 つのノードの種類を持つ 1 つのクラスターが作成されます。

    次の例で、**mysfabricclusterrg** は、Service Fabric クラスター用に作成したリソース グループの名前です。 ラボ環境は、それらを作成したリソース グループ内で自己完結していることを覚えておいてください。 この制限は、環境を定義するテンプレートが、新しく作成されたリソース グループ、または[ラボで使用されている仮想ネットワーク](devtest-lab-configure-vnet.md)内のみのリソースにアクセスできることを意味します。 このサンプルでは、必要なすべてのリソースは同じリソース グループ内に作成されます。

    ![作成されたクラスターを示すスクリーンショット。](./media/create-environment-service-fabric-cluster/cluster-created.png)

## <a name="start-or-stop-the-cluster"></a>クラスターを開始または停止する
クラスターの開始または停止は、DevTest Labs ページ自体から、または DevTest Labs によって提供される Service Fabric クラスター・ページから実行できます。 

### <a name="from-the-devtest-labs-page"></a>DevTest Labs ページから
ラボの DevTest Labs ページで、クラスターを停止または開始できます。 

1. 次の図に示されている Service Fabric クラスターの **3 つの点 ([...])** を選択します。 

    ![クラスターの開始と停止コマンドを示すスクリーンショット。](./media/create-environment-service-fabric-cluster/start-stop-on-devtest-lab-page.png)

2. コンテキスト メニューには、2 つのコマンド (クラスターを **開始する** ためのコマンドと **停止する** ためのコマンド) が表示されます。 [開始] コマンドは、クラスター内のすべてのノードを開始します。 [停止] コマンドは、クラスター内のすべてのノードを停止します。 クラスターを停止すると、Service Fabric クラスター自体は準備完了状態のままになります。 ただし、ラボのクラスターで開始コマンドを再発行するまでノードは使用できません。

    テスト環境で Service Fabric クラスターを使用する際に、いくつかの考慮事項があります。 ノードの再開始後に Service Fabric クラスターが完全にリハイドレートするには、多少時間がかかる場合があります。 シャットダウンから起動までのデータを保持するには、仮想マシンに接続されたマネージド ディスクにデータを保存する必要があります。 接続されたマネージド ディスクを使用しているときはパフォーマンスへの影響があるため、テスト環境にのみお勧めします。 データ ストレージに使用されているディスクがバックアップされていない場合は、クラスターで停止コマンドを発行するとデータは失われます。

### <a name="from-the-service-fabric-cluster-page"></a>Service Fabric クラスター ページから 
クラスターを開始または停止する別の方法があります。 

1. DevTest Labs ページで、ツリー ビューから Service Fabric クラスターを選択します。 

    ![クラスターの選択を示すスクリーンショット。](./media/create-environment-service-fabric-cluster/select-cluster.png)

2. クラスターの **Service Fabric クラスター** ページでは、ツールバーにクラスターを開始または停止するコマンドが表示されます。 

    ![Service Fabric クラスター ページの開始と停止コマンドを示すスクリーンショット。](./media/create-environment-service-fabric-cluster/start-stop-on-cluster-page.png)

## <a name="configure-auto-startup-and-auto-shutdown-schedule"></a>自動開始と自動シャットダウンのスケジュールを構成する
Service Fabric クラスターは、スケジュールに従って開始または停止することもできます。 このエクスペリエンスは、ラボの仮想マシンに対するエクスペリエンスに似ています。 コストを削減するために、既定では、ラボで作成されるすべてのクラスターは、ラボの[シャットダウン ポリシー](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)に定義された時刻に自動的にシャットダウンされます。 クラスターをシャットダウンするかどうかを指定する、またはクラスターのシャットダウン時刻を指定することで上書きできます。 

![自動開始と自動シャットダウンの既存のスケジュールを示すスクリーンショット。](./media/create-environment-service-fabric-cluster/existing-schedules.png)

### <a name="opt-in-to-the-auto-start-schedule"></a>自動開始スケジュールをオプトインする
開始スケジュールをオプトインするには、次の手順を実行します。

1. 左側のメニューの **[自動開始]** を選択します。
2. **[この Service Fabric クラスターの自動開始をスケジュールできるようにする]** で **[オン]** を選択します。 このページは、ラボの所有者が、その仮想マシンまたは Service Fabric クラスターの自動開始をユーザーに許可している場合にのみ有効です。
3. ツールバーの **[保存]** を選択します。 

    ![[自動開始] ページを示すスクリーンショット。](./media/create-environment-service-fabric-cluster/set-auto-start-settings.png)

### <a name="configure-auto-shutdown-settings"></a>自動シャットダウンの設定を構成する 
シャットダウンの設定を変更するには、次の手順を実行します。

1. 左側のメニューの **[自動シャットダウン]** を選択します。 
2. このページで、**[有効化]** で **[オフ]** を選択することで、自動シャットダウンをオプトアウトできます。 
3. **[有効化]** で **[オン]** を選択した場合は、次の手順に従ってください。
    1. シャットダウンする **時刻** を指定します。
    2. 時刻の **タイムゾーン** を指定します。 
    3. 自動シャットダウンする前に DevTest Labs に **通知** を送信させるかどうかを指定します。 
    4. 通知オプションで **[はい]** を選択した場合は、通知を送信する **[Webhook URL]** または **[電子メール アドレス]** あるいはその両方を指定します。 
    5. ツールバーの **[保存]** を選択します。

        ![[自動シャットダウン] ページを示すスクリーンショット。](./media/create-environment-service-fabric-cluster/auto-shutdown-settings.png)

## <a name="to-view-nodes-in-the-service-fabric-cluster"></a>Service Fabric クラスター内のノードを表示する
前の手順の Service Fabric クラスター ページは、DevTest Labs ページです。 このページには、クラスター内のノードは表示されません。 クラスターの詳細を表示するには、次の手順を実行します。

1. ラボの **DevTest ラボ** ページで、**[マイ仮想マシン]** セクションのツリー ビューから **リソース グループ** を選択します。

    ![リソース グループの選択を示すスクリーンショット。](./media/create-environment-service-fabric-cluster/select-resource-group.png)
2. **リソース グループ** ページで、一覧のリソース グループ内のすべてのリソースを表示します。 一覧から自分の **Service Fabric クラスター** を選択します。 

    ![一覧からのクラスターの選択を示すスクリーンショット。](./media/create-environment-service-fabric-cluster/select-cluster-resource-group-page.png)
3. Service Fabric によってクラスター用に提供されている **Service Fabric クラスター** ページが表示されます。 このページには、ノードやノードの種類など、クラスターに関するすべての情報が表示されます。

    ![Service Fabric クラスターのホーム ページを示すスクリーンショット。](./media/create-environment-service-fabric-cluster/service-fabric-cluster-page.png)

## <a name="next-steps"></a>次のステップ
環境の詳細については、次の記事を参照してください。 

- [Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs でパブリック環境を構成して使用する](devtest-lab-configure-use-public-environments.md)
