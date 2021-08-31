---
title: Azure Security Center を Azure VMware Solution と統合する
description: Azure Security Center ダッシュボードから Azure のネイティブ セキュリティ ツールを使用して、Azure VMware Solution VM を保護する方法について説明します。
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: 9c7326fca3aeebf277b5f54a65729e2594933984
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228666"
---
# <a name="integrate-azure-security-center-with-azure-vmware-solution"></a>Azure Security Center を Azure VMware Solution と統合する 

Azure Security Center は、Azure VMware Solution とオンプレミス仮想マシン (VM) 全体で高度な脅威防止を実現します。 Azure VMware Solution VM の脆弱性を評価し、必要に応じてアラートを生成します。 このセキュリティ アラートは、解決のために Azure Monitor に転送できます。 Azure Security Center では、セキュリティ ポリシーを定義できます。 詳細については、[セキュリティ ポリシーの処理](../security-center/tutorial-security-policy.md)に関する記事を参照してください。 

Azure Security Center には、次のような多くの機能が用意されています。
- ファイルの整合性の監視
- ファイルレス攻撃の検出
- オペレーティング システムのパッチ評価 
- セキュリティの誤った構成の評価
- エンドポイント保護の評価

この図は、Azure VMware Solution VM の統合セキュリティの統合監視アーキテクチャを示しています。
 
:::image type="content" source="media/azure-security-integration/azure-integrated-security-architecture.png" alt-text="Azure 統合セキュリティのアーキテクチャを示す図。" border="false":::

**Log Analytics エージェント** により、Azure、Azure VMware Solution、オンプレミスの VM からログ データが収集されます。 ログ データは Azure Monitor ログに送信され、**Log Analytics ワークスペース** に格納されます。 各ワークスペースには、データを格納するための独自のデータ リポジトリと構成があります。  ログが収集されると、**Azure Security Center** によって、Azure VMware Solution VM の脆弱性の状態が評価され、重大な脆弱性についてアラートが生成されます。 評価が完了すると、インシデントを作成して他の脅威とマップするために、Azure Security Center から Azure Sentinel に環境の脆弱性が転送されます。  Azure Security Center は、Azure Security Center コネクタを使用して Azure Sentinel に接続されます。 

## <a name="prerequisites"></a>前提条件

- [Security Center の使用の最適化を計画します](../security-center/security-center-planning-and-operations-guide.md)。

- [Security Center でサポートされているプラットフォームを確認します](../security-center/security-center-os-coverage.md)。

- さまざまなソースからデータを収集するために、[Log Analytics ワークスペースを作成します](../azure-monitor/logs/quick-create-workspace.md)。

- [サブスクリプションで Azure Security Center を有効にします](../security-center/security-center-get-started.md)。 

   >[!NOTE]
   >Azure Security Center はデプロイを必要としない事前構成済みのツールですが、有効にする必要があります。

- [Azure Defender を有効にします](../security-center/enable-azure-defender.md)。 


## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Security Center に Azure VMware Solution VM を追加する

1. Azure portal で、**Azure Arc** を検索し、それを選択します。

2. [リソース] の下で **[サーバー]** を選択してから、 **[+追加]** を選択します。

   :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Azure に Azure VMware Solution VM を追加するための Azure Arc サーバーのページを示すスクリーンショット。":::

3. **[スクリプトの生成]** を選択します。
 
   :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="対話型スクリプトを使用してサーバーを追加するためのオプションを示す Azure Arc ページのスクリーンショット。"::: 
 
4. **[前提条件]** タブで、 **[次へ]** を選択します。

5. **[リソースの詳細]** タブで次の詳細を入力し、 **[次へ: タグ]** を選択します。 

    - サブスクリプション

    - Resource group

    - リージョン 

    - オペレーティング システム

    - プロキシ サーバーの詳細
    
6. **[タグ]** タブで、 **[次へ]** を選択します。

7. **[スクリプトのダウンロードと実行]** タブで、 **[ダウンロード]** を選択します。

8. オペレーティング システムを指定し、Azure VMware Solution VM でスクリプトを実行します。

## <a name="view-recommendations-and-passed-assessments"></a>推奨事項と合格した評価を表示する

推奨事項と評価では、リソースのセキュリティ正常性の詳細が提供されます。 

1. Azure Security Center で、左ペインから **[インベントリ]** を選択します。

2. [リソースの種類] で、 **[サーバー - Azure Arc]** を選択します。
 
   :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="[リソースの種類] で [サーバー - Azure Arc] が選択されていることを示す、Azure Security Center の [インベントリ] ページのスクリーンショット。":::

3. リソースの名前を選択します。 リソースのセキュリティ正常性の詳細を示すページが表示されます。

4. **[推奨事項の一覧]** で、 **[推奨事項]** タブ、 **[合格した評価]** タブ、 **[利用できない評価]** タブを選択して、以下の詳細を表示します。

   :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Azure Security Center のセキュリティの推奨事項と評価を示すスクリーンショット。":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Azure Sentinel ワークスペースをデプロイする

Azure Sentinel では、環境全体にわたるセキュリティ分析、アラート検出、自動化された脅威対応が提供されます。 これは、Log Analytics ワークスペースの上に構築されたクラウドネイティブのセキュリティ情報イベント管理 (SIEM) ソリューションです。

Azure Sentinel は Log Analytics ワークスペースの上に構築されているため、必要なのは使用するワークスペースを選択するだけです。

1. Azure portal で、**Azure Sentinel** を検索し、それを選択します。

2. Azure Sentinel ワークスペースのページで、 **[+追加]** を選択します。

3. Log Analytics ワークスペースを選択し、 **[追加]** を選択します。

## <a name="enable-data-collector-for-security-events"></a>セキュリティ イベントのデータ コレクターを有効にする

1. Azure Sentinel ワークスペースのページで、構成したワークスペースを選択します。

2. [構成] の下の **[データ コネクタ]** を選択します。

3. [コネクタ名] 列の下の一覧から **[セキュリティ イベント]** を選択し、 **[コネクタ ページを開く]** を選択します。

4. コネクタ ページで、ストリームするイベントを選択し、 **[変更の適用]** を選択します。

   :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Azure Sentinel の [セキュリティ イベント] ページのスクリーンショット。ここで、ストリームするイベントを選択できます。":::




## <a name="connect-azure-sentinel-with-azure-security-center"></a>Azure Sentinel を Azure Security Center と接続する  

1. Azure Sentinel ワークスペースのページで、構成したワークスペースを選択します。

2. [構成] の下の **[データ コネクタ]** を選択します。

3. 一覧から **[Azure Security Center]** を選択してから、 **[コネクタ ページを開く]** を選択します。

   :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Azure Security Center を Azure Sentinel と接続するための選択を示す、Azure Sentinel の [データ コネクタ] ページのスクリーンショット。":::

4. **[接続]** を選択して、Azure Security Center を Azure Sentinel と接続します。

5. **[インシデントの作成]** を有効にして、Azure Security Center のためのインシデントを生成します。

## <a name="create-rules-to-identify-security-threats"></a>セキュリティの脅威を特定するためのルールを作成する

データ ソースを Azure Sentinel に接続したら、検出された脅威に対するアラートを生成するルールを作成できます。 次の例では、Windows サーバーへのサインイン試行で正しくないパスワードが使用された場合のルールを作成します。

1. Azure Sentinel の概要ページで、[構成] の下の **[Analytics]** を選択します。

2. [構成] の下の **[Analytics]** を選択します。

3. **[+ 作成]** を選択し、ドロップダウンで **[スケジュール済みクエリ ルール]** を選択します。

4. **[全般]** タブで必要な情報を入力し、 **[次へ: ルールのロジックを設定]** を選択します。

    - Name

    - Description

    - 方針

    - 重大度

    - Status

5. **[ルールのロジックを設定]** タブで必要な情報を入力し、 **[次へ]** を選択します。

    - ルールクエリ (次にクエリの例を示します)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - エンティティのマップ

    - クエリのスケジューリング

    - アラートのしきい値

    - イベントのグループ化

    - 抑制


6. **[インシデントの設定]** タブで、 **[この分析ルールによってトリガーされるアラートからインシデントを作成する]** を有効にし、 **[次へ: 自動応答]** を選択します。
 
    :::image type="content" source="../sentinel/media/tutorial-detect-threats-custom/general-tab.png" alt-text="Azure Sentinel で新しいルールを作成するための分析ルール ウィザードを示すスクリーンショット。":::

7. **確認\)** をクリックします。

8. **[確認と作成]** タブで情報を確認し、 **[作成]** を選択します。

>[!TIP]
>Windows サーバーへのサインインの試みが 3 回失敗すると、作成されたルールにより、失敗した試みすべてに対してインシデントがトリガーされます。

## <a name="view-alerts"></a>アラートを表示する

生成されたインシデントは、Azure Sentinel で表示できます。 インシデントを割り当てて、解決されたら閉じることもできます。これらはすべて Azure Sentinel 内から行えます。

1. Azure Sentinel の概要ページに移動します。

2. [脅威の管理] の下で、 **[インシデント]** を選択します。

3. インシデントを選択し、解決のためにチームに割り当てます。

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="インシデントが選択され、解決のためにインシデントを割り当てるオプションが表示されている、Azure Sentinel の [インシデント] ページのスクリーンショット。":::

>[!TIP]
>問題解決後は閉じることができます。

## <a name="hunt-security-threats-with-queries"></a>クエリを使用してセキュリティの脅威をハンティングする

クエリを作成するか、Azure Sentinel で使用可能な定義済みのクエリを使用して、環境内の脅威を特定できます。 次の手順では、定義済みのクエリを実行します。

1. Azure Sentinel の概要ページで、[脅威の管理] の **[ハンティング]** を選択します。 定義済みのクエリの一覧が表示されます。

   >[!TIP]
   >**[+ 新しいクエリ]** を選択して、新しいクエリを作成することもできます。 
   >
   >:::image type="content" source="../sentinel/media/hunting/save-query.png" alt-text="[+ 新しいクエリ] が強調表示されている、Azure Sentinel の [ハンティング] ページのスクリーンショット。":::

3. クエリを選択してから、 **[クエリの実行]** を選択します。

4. **[結果の表示]** を選択して結果を確認します。



## <a name="next-steps"></a>次のステップ

Azure VMware Solution VM を保護する方法について説明したので、次のことについて学習します。

- [Azure Defender ダッシュボードの使用](../security-center/azure-defender-dashboard.md)
- [Azure Sentinel の高度なマルチステージ攻撃の検出](../azure-monitor/logs/quick-create-workspace.md)
- [Azure VMware Solution での Azure ネイティブ サービスの統合](integrate-azure-native-services.md)
