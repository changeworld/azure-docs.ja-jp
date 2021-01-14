---
title: Log Analytics の IT Service Management Connector
description: この記事では、IT Service Management Connector (ITSMC) の概要を紹介し、これを使用して ITSM の作業項目を Log Analytics で監視および管理し、問題を迅速に解決することについて説明します。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: b26643daede9e26f2bf1807ae99a6ced5d1cb08c
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901574"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>IT Service Management Connector を使用して Azure を ITSM ツールに接続する

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

この記事では、Log Analytics の IT Service Management Connector (ITSMC) を構成して、作業項目を一元的に管理する方法に関する情報を提供します。

## <a name="add-it-service-management-connector"></a>IT Service Management Connector を追加する

接続を作成する前に、ITSMC を追加する必要があります。

1. Azure portal で **[リソースの作成]** を選択します。

   ![[リソースの作成] メニュー項目を示すスクリーンショット。](media/itsmc-overview/azure-add-new-resource.png)

2. Azure Marketplace で **IT Service Management Connector** を検索します。 **[作成]** を選択します。

   ![Azure Marketplace の [作成] ボタンを示すスクリーンショット。](media/itsmc-overview/add-itsmc-solution.png)

3. **[LA Workspace]\(LA ワークスペース\)** セクションで、ITSMC をインストールする Azure Log Analytics ワークスペースを選択します。
   >[!NOTE]
   >
   > * ITSMC は、以下のリージョンの Log Analytics ワークスペースにのみインストールできます:米国東部、米国西部 2、米国中南部、US Gov アリゾナ、US Gov バージニア、カナダ中部、西ヨーロッパ、英国南部、東南アジア、東日本、インド中部、オーストラリア南東部。

4. **[Log Analytics ワークスペース]** セクションで、ITSMC リソースを作成するリソース グループを選択します。

   ![[Log Analytics ワークスペース] セクションを示すスクリーンショット。](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >現在進行中の、Microsoft Operations Management Suite (OMS) から Azure Monitor への移行の一環として、OMS ワークスペースは、"*Log Analytics ワークスペース*" と呼ばれるようになっています。

5. **[OK]** を選択します。

ITSMC リソースがデプロイされると、ウィンドウの右上隅に通知が表示されます。

## <a name="create-an-itsm-connection"></a>ITSM 接続を作成する

ITSMC をインストールしたら、接続を作成できます。

接続を作成するには、ITSMC からの接続を許可するように ITSM ツールを準備する必要があります。  

手順については、接続先になる ITSM 製品に基づいて、次のいずれかのリンクを選択します。

- [ServiceNow](./itsmc-connections-servicenow.md)
- [System Center Service Manager](./itsmc-connections-scsm.md)
- [Cherwell](./itsmc-connections-cherwell.md)
- [Provance](./itsmc-connections-provance.md)

お使いの ITSM ツールを準備したら、以下の手順を完了して接続を作成します。

1. **[すべてのリソース]** で、**ServiceDesk( *<実際のワークスペース名>* )** を探します。

   ![Azure portal の最新のリソースを示すスクリーンショット。](media/itsmc-overview/itsm-connections.png)

1. 左側のウィンドウの **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** を選択します。

   ![[ITSM 接続] メニュー項目を示すスクリーンショット。](media/itsmc-overview/add-new-itsm-connection.png)
1. **[接続の追加]** を選択します。

1. ITSM 製品またはサービスに応じた説明に従って、接続設定を指定します。

    - [ServiceNow](./itsmc-connections-servicenow.md)
    - [System Center Service Manager](./itsmc-connections-scsm.md)
    - [Cherwell](./itsmc-connections-cherwell.md)
    - [Provance](./itsmc-connections-provance.md)

   > [!NOTE]
   >
   > 既定では、ITSMC の接続の構成データは、24 時間に 1 回更新されます。 接続のデータをすぐに最新の情報に更新し、編集内容やテンプレートの更新を反映させるには、接続のブレードにある **[同期]** ボタンを選択します。
   >
   > ![[接続] ブレードの [同期] ボタンを示すスクリーンショット。](media/itsmc-overview/itsmc-connections-refresh.png)

## <a name="use-itsmc"></a>ITSMC を使用する

   ITSMC を使用して、Azure Monitor アラートから ITSM ツールにアラートを作成できます。

## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure アラートから ITSM 作業項目を作成する

ITSM 接続を作成したら、ITSM ツールで、Azure アラートに基づいて作業項目を作成できます。 作業項目を作成するには、アクション グループで ITSM アクションを使用します。

アクション グループでは、Azure アラートのアクションをトリガーするために、モジュール化された、再利用できる方法が提供されます。 アクション グループは、Azure portal で、メトリック アラート、アクティビティ ログ アラート、Azure Log Analytics アラートと共に使用できます。

> [!NOTE]
> ITSM 接続を作成したら、同期プロセスが終了するまで 30 分待機する必要があります。

### <a name="template-definitions"></a>テンプレート定義

   作業項目の種類として、ITSM ツールによって定義されているテンプレートを使用できるものがあります。
テンプレートを使用すると、ユーザーはアクション グループの一部として定義されている固定値に従って自動的に入力されるフィールドを定義できます。 テンプレートは ITSM ツールで定義します。
アクション グループの定義の一部として使用するテンプレートを定義できます。

以下の手順を使用して、アクション グループを作成します。

1. Azure portal で、 **[アラート]** を選択します。
2. 画面上部のメニューで、 **[アクションの管理]** を選択します。

    ![[アクションの管理] メニュー項目を示すスクリーンショット。](media/itsmc-overview/action-groups-selection-big.png)

   **[アクション グループの作成]** ウィンドウが表示されます。

3. アクション グループを作成する **サブスクリプション** と **リソース グループ** を選択します。 アクション グループの **アクション グループ名** と **表示名** を指定します。 **Next:通知** を選択します。

    ![[アクション グループの作成] ウィンドウを示すスクリーンショット。](media/itsmc-overview/action-groups-details.png)

4. 通知の一覧で、 **[次へ: アクション]** を選択します。
5. アクションの一覧で、 **[アクションの種類]** 一覧の **[ITSM]** を選択します。 アクションの **名前** を指定します。 **[詳細の編集]** を表すペンのボタンを選択します。

    ![アクション グループの定義を示すスクリーンショット。](media/itsmc-definition/action-group-pen.png)

6. **[サブスクリプション]** の一覧で、Log Analytics ワークスペースを配置するサブスクリプションを選択します。 **[接続]** の一覧で、ITSM コネクタ名を選択します。 その後には実際のワークスペース名が続きます。 たとえば、MyITSMMConnector(MyWorkspace) となります。

7. **作業項目** の種類を選択します。

8. 標準状態のフィールドに固定値を入力する場合は、 **[カスタム テンプレートを使用する]** を選択します。 それ以外の場合は、 **[テンプレート]** の一覧で既存の [テンプレート](#template-definitions)を選択し、テンプレート フィールドに固定値を入力します。

9. アクション ITSM グループ定義の最後のセクションでは、各アラートから作成されるアラートの数を定義できます。 このセクションはログ検索アラートのみに関連しています。

    * 作業項目ドロップダウン "インシデント" または "アラート" で選択した場合:
        * **[各構成項目に個々の作業項目を作成する]** チェック ボックスをオンにすると、すべてのアラートのすべての構成項目に新しい作業項目が作成されます。 ITSM システムの構成項目ごとに複数の作業項目が存在する場合があります。

            次に例を示します。
            1) 次の 3 つの構成項目を含むアラート 1: A、B、C - 3 つの作業項目を作成します。
            2) 次の 1 つの構成項目を含むアラート 2: D - 1 つの作業項目を作成します。

                **このフローが終了するまでに、アラートは 4 つになります**
        * **[各構成項目に個々の作業項目を作成する]** チェック ボックスをオフにすると、新しい作業項目が作成されないアラートになります。 作業項目はアラート ルールに従ってマージされます。

            次に例を示します。
            1) 次の 3 つの構成項目を含むアラート 1: A、B、C - 1 つの作業項目を作成します。
            2) 次の 1 つの構成項目を含むフェーズ 1 と同じアラート ルールのアラート 2: D - フェーズ 1 の作業項目にマージされます。
            3) 次の 1 つの構成項目を含む別のアラート ルールのアラート 3: E - 1 つの作業項目を作成します。

                **このフローが終了するまでに、アラートは 2 つになります**

       ![[ITSM インシデント] ウィンドウを示すスクリーンショット。](media/itsmc-overview/itsm-action-configuration.png)

    * 作業項目ドロップダウン "イベント" で選択した場合:
        * ラジオ ボタン選択で **[各ログ エントリに対して、個々の作業項目を作成します]** を選んだ場合は、ログ検索アラート クエリの検索結果の各行に対してアラートが作成されます。 アラートのペイロードでは、description プロパティに検索結果の行が含まれるようになります。
        * ラジオ ボタン選択で **[各構成項目に個々の作業項目を作成する]** を選んだ場合は、すべてのアラートのすべての構成項目に新しい作業項目が作成されます。 ITSM システムの構成項目ごとに複数の作業項目が存在する場合があります。 これは、[インシデント/アラート] セクションのチェック ボックスをオンにした場合と同じになります。
    ![[ITSM イベント] ウィンドウを示すスクリーンショット。](media/itsmc-overview/itsm-action-configuration-event.png)

10. **[OK]** を選択します。

Azure アラートのルールを作成または編集するときには、ITSM アクションを含むアクション グループを使用します。 アラートがトリガーされると、ITSM ツールで、作業項目が作成または更新されます。

> [!NOTE]
>
>- ITSM アクションの価格については、アクション グループの[価格に関するページ](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。
>
>
>- アラート ルール定義の短い説明のフィールドは、ITSM アクションを使用して送信する場合は 40 文字に制限されます。

## <a name="next-steps"></a>次のステップ

* [ITSM Connector での問題のトラブルシューティング](./itsmc-resync-servicenow.md)
