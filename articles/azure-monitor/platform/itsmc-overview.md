---
title: Log Analytics の IT Service Management Connector
description: この記事では、IT Service Management Connector (ITSMC) の概要を紹介し、これを使用して ITSM の作業項目を Log Analytics で監視および管理し、問題を迅速に解決することについて説明します。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: 90a5f0f84c72895a8450a42260b07f6dbea15e37
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428029"
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
> 2020 年 10 月 1 日以降、新規のお客様が Cherwell と Provance ITSM を Azure Alert と統合することはできなくなります。 新しい ITSM 接続はサポートされません。 既存の ITSM 接続はサポートされます。

ITSMC を使用すると、次のことができます。

-  お使いの Azure アラート (メトリック アラート、アクティビティ ログ アラート、Log Analytics アラート) に基づいて、ITSM ツールで作業項目を作成する。
-  オプションで、ITSM ツールのインシデントと変更要求データを Azure Log Analytics ワークスペースに同期する。

法律条項とプライバシー ポリシーについては、「[Microsoft プライバシー ステートメント](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)」を参照してください。

次の手順を完了すれば、ITSMC の使用を開始できます。

1.  [ITSMC を追加します。](#add-it-service-management-connector)
2.  [ITSM 接続を作成します。](#create-an-itsm-connection)
3.  [接続を使用します。](#use-itsmc)


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

8. 標準状態のフィールドに固定値を入力する場合は、 **[カスタム テンプレートを使用する]** を選択します。 それ以外の場合は、 **[テンプレート]** の一覧で既存の [テンプレート](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#template-definitions)を選択し、テンプレート フィールドに固定値を入力します。

9. **[各構成項目に個々の作業項目を作成する]** を選択すると、すべての構成項目に独自の作業項目が含められます。 構成項目ごとに 1 つの作業項目が存在することになります。 これは、作成されるアラートに従って更新されます。

   * 作業項目ドロップダウン "インシデント" または "アラート" で選択した場合: **[各構成項目に個々の作業項目を作成する]** チェック ボックスをオフにすると、すべてのアラートに新しい作業項目が作成されます。 構成項目ごとに、複数のアラートが存在する場合があります。

   ![[ITSM チケット] ウィンドウを示すスクリーンショット。](media/itsmc-overview/itsm-action-configuration.png)
   
   * 作業項目ドロップダウン "イベント" で選択した場合:ラジオ ボタン選択で **[各ログ エントリに対して、個々の作業項目を作成します]** を選択した場合、アラートのたびに新しい作業項目が作成されます。 ラジオ ボタン選択で **[各構成項目に個々の作業項目を作成する]** を選択すると、すべての構成項目に独自の作業項目が含められます。
   ![[ITSM チケット] ウィンドウを示すスクリーンショット。](media/itsmc-overview/itsm-action-configuration-event.png)

10. **[OK]** を選択します。

Azure アラートのルールを作成または編集するときには、ITSM アクションを含むアクション グループを使用します。 アラートがトリガーされると、ITSM ツールで、作業項目が作成または更新されます。

> [!NOTE]
>
>- ITSM アクションの価格については、アクション グループの[価格に関するページ](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。
>
>
>- アラート ルール定義の短い説明のフィールドは、ITSM アクションを使用して送信する場合は 40 文字に制限されます。


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>インシデントと変更要求データを視覚化および分析する

ITSMC では、接続を設定したときの構成に応じて、最大 120 日分のインシデントと変更要求データを同期できます。 このデータのログ レコードのスキーマについては、この記事の[次のセクション](#additional-information)を参照してください。

インシデントと変更要求データは、ITSMC ダッシュボードを使用して視覚化できます。

![ITSMC ダッシュボードを示すスクリーンショット。](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

ダッシュボードでは、コネクタの状態に関する情報も提供されます。これは、接続の問題を分析する際の開始点として利用できます。

Service Map で、影響を受けたコンピューターに対して同期されたインシデントを視覚化することもできます。

サービス マップは、Windows と Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、重要なサービスを提供する相互接続されたシステムとしてサーバーを表示できます。 Service Map には、TCP 接続の任意のアーキテクチャ全体にわたるサーバー、プロセス、ポートの間の接続が表示されます。 エージェントのインストール以外に構成は必要ありません。 詳細については、[Service Map の使用](../insights/service-map.md)に関するページを参照してください。

Service Map を使用している場合は、次に示すように、ITSM ソリューションで作成されたサービス デスク項目を表示できます。

![Log Analytics の画面を示すスクリーンショット。](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


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
| PlannedStartDate_t  |     開始予定日 |
| PlannedEndDate_t  |   終了予定日 |
| WorkStartDate_t  | 実際の開始日 |
| WorkEndDate_t | 実際の終了日|
| Description_s | 説明 |
| Computer  | 構成項目 |


## <a name="troubleshoot-itsm-connections"></a>ITSM 接続のトラブルシューティング
- 接続対象ソースの UI からの接続が失敗し、"**接続の保存中にエラーが発生しました**" というメッセージが表示される場合は、次の手順を実行してください。
   - ServiceNow、Cherwell、Provance の接続の場合:  
     - 各接続のユーザー名、パスワード、クライアント ID、クライアント シークレットが正しく入力されていることを確認します。  
     - 対応する ITSM 製品で、接続を作成するための十分な特権を持っていることを確認します。  
   - Service Manager 接続の場合:  
     - Web アプリが正常にデプロイされていること、およびハイブリッド接続が作成されていることを確認します。 オンプレミスの Service Manager コンピューターとの接続が正常に確立されていることを確認するには、[ハイブリッド接続](./itsmc-connections.md#configure-the-hybrid-connection)の作成に関するドキュメントで説明しているように Web アプリの URL に移動します。  

- ServiceNow のデータが Log Analytics に同期されていない場合は、ServiceNow インスタンスがスリープ状態になっていことを確認します。 ServiceNow dev インスタンスは、長時間アイドル状態になっているとスリープ状態に移行することがあります。 それが起きているのでなければ、問題を報告してください。
- Log Analytics アラートが発生しても ITSM 製品内に作業項目が作成されない場合、構成項目が作成されなかったり作業項目にリンクされなかったりする場合、その他の情報が必要な場合は、以下のリソースを参照してください。
   -  ITSMC: このソリューションには、接続、作業項目、コンピューターなどの概要が表示されます。 **[コネクタの状態]** というラベルのタイルを選択します。 そうすると、適切なクエリを使用して **ログ検索** が行われます。 詳細については、`LogType_S` が `ERROR` になっているログ レコードを参照してください。
   - **[ログ検索]** ページ:クエリ `*ServiceDeskLog_CL*` を使用して、エラーと関連情報を直接表示します。

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager Web アプリのデプロイのトラブルシューティング
-   Web アプリのデプロイに関する問題がある場合は、サブスクリプションでリソースを作成/デプロイするためのアクセス許可があることを確認します。
-   [スクリプト](itsmc-service-manager-script.md)の実行時に "**オブジェクト参照がオブジェクトのインスタンスに設定されていません**" というエラーが発生する場合は、 **[ユーザー構成]** セクションに有効な値を入力したことを確認します。
-   Service Bus Relay 名前空間を作成できない場合は、必要なリソース プロバイダーがサブスクリプションに登録されていることを確認します。 登録されていない場合は、Azure portal で、Service Bus Relay 名前空間を手動で作成します。 これは、Azure portal で[ハイブリッド接続を作成する](./itsmc-connections.md#configure-the-hybrid-connection)ときに作成することもできます。


## <a name="contact-us"></a>お問い合わせ

IT Service Management Connector に関する質問やフィードバックがある場合は、[omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) までご連絡ください。

## <a name="next-steps"></a>次のステップ
[ITSM 製品/サービスを IT Service Management Connector に追加する](./itsmc-connections.md)

