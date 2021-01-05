---
title: Log Analytics の IT Service Management Connector
description: この記事では、IT Service Management Connector (ITSMC) の概要を紹介し、これを使用して ITSM の作業項目を Log Analytics で監視および管理し、問題を迅速に解決することについて説明します。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 3d4e5ad0b24b7163072d7e3110a523dad9608923
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507373"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>IT Service Management Connector を使用して Azure を ITSM ツールに接続する

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT Service Management Connector (ITSMC) を使用すると、Azure を、サポートされている IT Service Management (ITSM) 製品またはサービスに接続できます。

Azure Log Analytics や Azure Monitor などの Azure サービスには、Azure および Azure 以外のリソースでの問題の検出、分析、トラブルシューティングを行うツールが用意されています。 しかし、問題に関連する作業項目は、一般に、ITSM の製品またはサービス内に存在します。 ITSMC では、より迅速な問題解決に役立つように、Azure と ITSM ツールの間に双方向接続が提供されます。

ITSMC は、次の ITSM ツールとの接続をサポートしています。

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Cherwell や Provance のお客様には、統合の別の方法として、[Webhook アクション](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook)を使用して Cherwell と Provance のエンドポイントに接続することを提案しています。

ITSMC を使用すると、次のことができます。

-  お使いの Azure アラート (メトリック アラート、アクティビティ ログ アラート、Log Analytics アラート) に基づいて、ITSM ツールで作業項目を作成する。
-  オプションで、ITSM ツールのインシデントと変更要求データを Azure Log Analytics ワークスペースに同期する。

法律条項とプライバシー ポリシーについては、「[Microsoft プライバシー ステートメント](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)」を参照してください。

次の手順を完了すれば、ITSMC の使用を開始できます。

1.  [ITSMC を追加します。](#add-it-service-management-connector)
2. [ITSM 製品/サービスを IT Service Management Connector に追加する](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
3.  [ITSM 接続を作成します。](#create-an-itsm-connection)
4.  [接続を使用します。](#use-itsmc)
   
   >[!NOTE]
> この順序で手順に従う必要があります。そうしないと、エラーが発生します。

##  <a name="add-it-service-management-connector"></a>IT Service Management Connector を追加する

接続を作成する前に、ITSMC を追加する必要があります。

1. Azure portal で **[リソースの作成]** を選択します。

   ![[リソースの作成] メニュー項目を示すスクリーンショット。](media/itsmc-overview/azure-add-new-resource.png)

2. Azure Marketplace で **IT Service Management Connector** を検索します。 **[作成]** を選択します。

   ![Azure Marketplace の [作成] ボタンを示すスクリーンショット。](media/itsmc-overview/add-itsmc-solution.png)

3. **[OMS ワークスペース]** セクションで、ITSMC をインストールする Azure Log Analytics ワークスペースを選択します。
   >[!NOTE]
   > * 現在進行中の、Microsoft Operations Management Suite (OMS) から Azure Monitor への移行の一環として、OMS ワークスペースは、"*Log Analytics ワークスペース*" と呼ばれるようになっています。
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

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

お使いの ITSM ツールを準備したら、以下の手順を完了して接続を作成します。

1. **[すべてのリソース]** で、**ServiceDesk( *<実際のワークスペース名>* )** を探します。

   ![Azure portal の最新のリソースを示すスクリーンショット。](media/itsmc-overview/itsm-connections.png)

1. 左側のウィンドウの **[ワークスペースのデータ ソース]** で、 **[ITSM 接続]** を選択します。

   ![[ITSM 接続] メニュー項目を示すスクリーンショット。](media/itsmc-overview/add-new-itsm-connection.png)
   このページには接続の一覧を表示されています。
1. **[接続の追加]** を選択します。

4. [ITSM 製品/サービスとの ITSMC 接続の構成](./itsmc-connections.md)に関するページの説明に従って、接続の設定を指定します。

   > [!NOTE]
   >
   > 既定では、ITSMC の接続の構成データは、24 時間に 1 回更新されます。 接続のデータをすぐに最新の情報に更新し、編集内容やテンプレートの更新を反映させるには、接続のブレードにある **[同期]** ボタンを選択します。
   >
   > ![[接続] ブレードの [同期] ボタンを示すスクリーンショット。](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>ITSMC を使用する
   ITSMC を使用して、Azure アラート、Log Analytics アラート、Log Analytics ログ レコードから作業項目を作成できます。

## <a name="template-definitions"></a>テンプレート定義
   作業項目の種類として、ITSM ツールによって定義されているテンプレートを使用できるものがあります。
テンプレートを使用すると、ユーザーはアクション グループの一部として定義されている固定値に従って自動的に入力されるフィールドを定義できます。 テンプレートは ITSM ツールで定義します。 アクション グループの定義で使用するテンプレートを定義できます。
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Azure アラートから ITSM 作業項目を作成する

ITSM 接続を作成したら、ITSM ツールで、Azure アラートに基づいて作業項目を作成できます。 作業項目を作成するには、アクション グループで ITSM アクションを使用します。

アクション グループでは、Azure アラートのアクションをトリガーするために、モジュール化された、再利用できる方法が提供されます。 アクション グループは、Azure portal で、メトリック アラート、アクティビティ ログ アラート、Azure Log Analytics アラートと共に使用できます。

> [!NOTE]
> ITSM 接続を作成したら、同期プロセスが終了するまで 30 分待機する必要があります。
> 

以下の手順に従って作業項目を作成します。

1. Azure portal で、 **[アラート]** を選択します。
2. 画面上部のメニューで、 **[アクションの管理]** を選択します。

    ![[アクションの管理] メニュー項目を示すスクリーンショット。](media/itsmc-overview/action-groups-selection-big.png)

   **[アクション グループの作成]** ウィンドウが表示されます。

3. アクション グループを作成する **サブスクリプション** と **リソース グループ** を選択します。 アクション グループの **アクション グループ名** と **表示名** を指定します。 **Next:通知** を選択します。

    ![[アクション グループの作成] ウィンドウを示すスクリーンショット。](media/itsmc-overview/action-groups-details.png)

4. 通知の一覧で、 **[次へ: アクション]** を選択します。
5. アクションの一覧で、 **[アクションの種類]** 一覧の **[ITSM]** を選択します。 アクションの **名前** を指定します。 **[詳細の編集]** を表すペンのボタンを選択します。
6. **[サブスクリプション]** の一覧で、Log Analytics ワークスペースを配置するサブスクリプションを選択します。 **[接続]** の一覧で、ITSM コネクタ名を選択します。 その後には実際のワークスペース名が続きます。 たとえば、MyITSMMConnector(MyWorkspace) となります。

7. **作業項目** の種類を選択します。

8. 標準状態のフィールドに固定値を入力する場合は、 **[カスタム テンプレートを使用する]** を選択します。 それ以外の場合は、 **[テンプレート]** の一覧で既存の [テンプレート](#template-definitions)を選択し、テンプレート フィールドに固定値を入力します。

9. **[各構成項目に個々の作業項目を作成する]** を選択すると、すべての構成項目に独自の作業項目が含められます。 構成項目ごとに 1 つの作業項目が存在することになります。 これは、作成されるアラートに従って更新されます。

    * 作業項目ドロップダウン "インシデント" または "アラート" で選択した場合: **[各構成項目に個々の作業項目を作成する]** チェック ボックスをオフにすると、すべてのアラートに新しい作業項目が作成されます。 構成項目ごとに、複数のアラートが存在する場合があります。

       ![[ITSM インシデント] ウィンドウを示すスクリーンショット。](media/itsmc-overview/itsm-action-configuration.png)

    * 作業項目ドロップダウン "イベント" で選択した場合:ラジオ ボタン選択で **[各ログ エントリに対して、個々の作業項目を作成します]** を選択した場合、アラートのたびに新しい作業項目が作成されます。 ラジオ ボタン選択で **[各構成項目に個々の作業項目を作成する]** を選択すると、すべての構成項目に独自の作業項目が含められます。
   ![[ITSM イベント] ウィンドウを示すスクリーンショット。](media/itsmc-overview/itsm-action-configuration-event.png)

10. **[OK]** を選択します。

Azure アラートのルールを作成または編集するときには、ITSM アクションを含むアクション グループを使用します。 アラートがトリガーされると、ITSM ツールで、作業項目が作成または更新されます。

> [!NOTE]
>
>- ITSM アクションの価格については、アクション グループの[価格に関するページ](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。
>
>
>- アラート ルール定義の短い説明のフィールドは、ITSM アクションを使用して送信する場合は 40 文字に制限されます。

## <a name="additional-information"></a>関連情報

### <a name="data-synced-from-your-itsm-product"></a>お使いの ITSM 製品から同期されるデータ
インシデントと変更要求は、接続の構成に基づいて、お使いの ITSM 製品から Log Analytics ワークスペースに同期されます。

このセクションでは、ITSMC によって収集されるデータの例の一部を示します。

**ServiceDesk_CL** 内のフィールドは、Log Analytics にインポートする作業項目の種類によって異なります。 以下に、2 つの作業項目の種類について、フィールドの一覧を示します。

**作業項目:** **インシデント**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- サービス デスク接続名
- サービス デスク ID
- State
- 緊急度
- 影響
- Priority
- エスカレーション
- 作成者
- 解決者
- 終了者
- source
- 割当先
- カテゴリ
- タイトル
- 説明
- 作成日
- 終了日
- 解決日
- 最終更新日
- Computer


**作業項目:** **変更要求**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- サービス デスク接続名
- サービス デスク ID
- 作成者
- 終了者
- source
- 割当先
- タイトル
- Type
- カテゴリ
- State
- エスカレーション
- 競合状態
- 緊急度
- Priority
- リスク
- 影響
- 割当先
- 作成日
- 終了日
- 最終更新日
- 要求日
- 開始予定日
- 終了予定日
- 作業開始日
- 作業終了日
- 説明
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow インシデントの出力データ

| Log Analytics のフィールド | ServiceNow のフィールド |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |緊急度 |
| Impact_s |影響|
| Priority_s | Priority |
| CreatedBy_s | 開始者 |
| ResolvedBy_s | 解決者|
| ClosedBy_s  | 終了者 |
| Source_s| 連絡先の種類 |
| AssignedTo_s | 割り当て先  |
| Category_s | カテゴリ |
| Title_s|  簡単な説明 |
| Description_s|  Notes |
| CreatedDate_t|  開始済み |
| ClosedDate_t| closed|
| ResolvedDate_t|解決済み|
| Computer  | 構成項目 |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow 変更要求の出力データ

| Log Analytics | ServiceNow のフィールド |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | 要求者 |
| ClosedBy_s | 終了者 |
| AssignedTo_s | 割り当て先  |
| Title_s|  簡単な説明 |
| Type_s|  Type |
| Category_s|  カテゴリ |
| CRState_s|  State|
| Urgency_s|  緊急度 |
| Priority_s| Priority|
| Risk_s| リスク|
| Impact_s| 影響|
| RequestedDate_t  | Requested by date\(要求日\) |
| ClosedDate_t | 終了日 |
| PlannedStartDate_t  | 開始予定日 |
| PlannedEndDate_t  | 終了予定日 |
| WorkStartDate_t  | 実際の開始日 |
| WorkEndDate_t | 実際の終了日|
| Description_s | 説明 |
| Computer  | 構成項目 |

## <a name="contact-us"></a>お問い合わせ

IT Service Management Connector に関する質問やフィードバックがある場合は、[omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) までご連絡ください。

## <a name="next-steps"></a>次のステップ
[ITSM 製品/サービスを IT Service Management Connector に追加する](./itsmc-connections.md)
