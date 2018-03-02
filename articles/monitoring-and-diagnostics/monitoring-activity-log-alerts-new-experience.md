---
title: "Azure Monitor の新しいアラート (プレビュー) エクスペリエンスを使用したアクティビティ ログ アラートの作成と管理 | Microsoft Docs"
description: "この記事では、Azure Monitor の [アラート (プレビュー)] タブからアクティビティ ログ アラートを作成する方法について説明します。 この記事では、この機能の新しいユーザー エクスペリエンスについて詳しく説明します。"
author: JYOTHIRMAISURI
manager: vvithal
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: 
ms.openlocfilehash: afdd82617c47f0dee22c229feba87bdf79b90a69
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-preview-experience"></a>新しいアラート (プレビュー) のエクスペリエンスを使用してアクティビティ ログ アラートを作成する

アクティビティ ログ アラートは、アラートで指定した条件と一致する新しいアクティビティ ログ イベントが発生したときにアクティブになるアラートです。

これらのアラートは Azure リソースに対応しており、Azure Resource Manager テンプレートを使用して作成できます。 これらは、Azure Portal で作成、更新、削除することもできます。 この記事では、アクティビティ ログ アラートの背後の概念について説明します。 その後、Azure Portal を使用して、[Azure アラート (プレビュー)](monitoring-overview-unified-alerts.md) の新しいエクスペリエンスを使ってアクティビティ ログ イベントにアラートを設定する方法について説明します。

通常は、Azure サブスクリプション内のリソースに特定の変更が発生した場合に通知を受け取るためにアクティビティ ログ アラートを作成します。多くの場合、特定のリソース グループまたはリソースを対象とします。 たとえば、**myProductionResourceGroup** (サンプル リソース グループ) 内の任意の仮想マシンが削除されたときに通知を受け取りたい場合やサブスクリプション内のユーザーに新しいロールが割り当てられた場合に通知を受け取ることができます。

JSON オブジェクトの任意の最上位プロパティに基づいて、アクティビティ ログ イベントのアクティビティ ログ アラートを構成できます。 ただし、ポータルには、以下のセクションで詳しく説明するように、最も一般的なオプションが表示されます。

>[!NOTE]

> カテゴリが [管理] の場合、アラートに上記の条件を 1 つ以上指定する必要があります。 アクティビティ ログ内にイベントが作成されるたびにアクティブ化するアラートを作成することはできません。
>

アクティビティ ログ アラートがアクティブになると、アクションまたは通知の生成にアクション グループが使用されます。 アクション グループは、通知レシーバー (電子メール アドレス、webhook URL、または SMS の電話番号) の再利用可能なセットです。 これらのレシーバーは、複数のアラートから参照でき、通知チャネルの一元管理とグループ化を行うことができます。 アクティビティ ログ アラートを定義するときには 2 つの方法があります。 次のようにすることができます。

* アクティビティ ログ アラートで既存のアクション グループを使用します。
* 新しいアクション グループを作成できます。

アクション グループの詳細については、「[Azure Portal でのアクション グループの作成および管理](monitoring-action-groups.md)」を参照してください。

サービス正常性通知について詳しくは、[サービス正常性通知のアクティビティ ログ アラートの受け取り](monitoring-activity-log-alerts-on-service-notifications.md)に関する記事をご覧ください。


## <a name="whats-new-in-alerts-preview-for-activity-logs"></a>アラート (プレビュー) のアクティビティ ログに関する新機能

[Azure アラート (プレビュー)](monitoring-overview-unified-alerts.md) では、アクティビティ ログ アラートのユーザー エクスペリエンスが強化されました。 [アラートの強化されたユーザー エクスペリエンス](monitoring-overview-unified-alerts.md)により、次のことが可能になりました。

- **[モニター]** > **[アラート (プレビュー)]** ブレードから、アクティビティ ログ アラート ルールを[作成](#create-an-alert-rule-for-an-activity-log)および[管理](#view-and-manage-activity-log-alert-rules)します。 [アクティビティ ログ](monitoring-overview-activity-logs.md)の詳細を確認してください。

- **アラートの対象に関する新しいオプション**: 新しいアクティビティ ログ アラート ルールを作成する際に、ターゲット リソース、リソース グループ、またはサブスクリプションを選択できるようになりました。


## <a name="create-an-alert-rule-for-an-activity-log"></a>アクティビティ ログのアラート ルールの作成

> [!NOTE]

>  アラート ルールを作成する際は、次のことを確認してください。

> - スコープ内のサブスクリプションが、アラートが作成されているサブスクリプションと同じである。
- 条件が、アラートが構成されているレベル/状態/呼び出し元/リソース グループ/リソース ID/リソースの種類/イベント カテゴリである必要があります。
- アラートの構成 JSON に "anyOf" 条件または入れ子になった条件がない (基本的に許可されている allOf は 1 つだけで、それ以上 allOf/anyOf は含まれません)。


次の手順に従います。

1. Azure Portal で、**[モニター]** > **[アラート (プレビュー)]** の順に選択します。
2. **[アラート (プレビュー)]** ウィンドウの上部にある **[新しいアラート ルール]** をクリックします。

     ![新しいアラート ルール](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     **[ルールの作成]** ウィンドウが表示されます。

      ![新しいアラート ルールのオプション](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **[アラートの条件を定義します]** で、次の情報を入力し、**[完了]** をクリックします。

    - **[アラートの対象]**: 新しいアラートの対象を表示および選択するには、**[サブスクリプション別でフィルター]** / **[リソースの種類でフィルター]** を使用し、表示された一覧からリソースまたはリソース グループを選択します。

    > [!NOTE]

    > リソース、リソース グループ、またはサブスクリプション全体を選択できます。

    **[アラートの対象] のサンプル ビュー**

     ![ターゲットを選択する](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - **[対象の条件]** で、**[条件の追加]** をクリックし、**アクティビティ ログ**としてシグナルの種類を選択します。

    - 表示された一覧からシグナルを選択します。

    ログ履歴のタイムラインと、この対象シグナルの対応するアラート ロジックを選択できます。

    **[条件の追加] 画面**

    ![条件の追加](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **[履歴の表示]**: [過去 6 時間]、[過去 12 時間]、[過去 24 時間]、[先週]。

    **[アラート ロジック]**:

        - **Event Level**- The severity level of the event.**Verbose,Informational, Warning, Error**, or **Critical**.
        - **Status**: The status of the event.**Started, Failed**, or **Succeeded**.
        - **Event initiated by**: Also known as the caller; The email address or Azure Active Directory identifier of the user who performed the operation.

        **Sample signal graph with alert logic applied** :

        ![ criteria selected](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. **[アラートの詳細を定義します]** で、以下の詳細を入力します。

    - **[アラート ルール名]** - 新しいアラート ルールの名前
    - **[説明]** - 新しいアラート ルールの説明
    - **[リソース グループにアラートを保存します]** - この新しいルールを保存するリソース グループを選択します。

5. **[アクション グループ]** のドロップダウン メニューから、この新しいアラート ルールに割り当てるアクション グループを指定します。 または、[新しいアクション グループを作成](monitoring-action-groups.md)し、新しいルールに割り当てます。 新しいグループを作成するには、**[+ 新しいグループ]** をクリックします。

6. ルールを作成した後に有効にするには、**[ルールの作成時に有効にする]** オプションの **[はい]** をクリックします。
7. **[アラート ルールの作成]** をクリックします。

    アクティビティ ログの新しいアラート ルールが作成され、ウィンドウの右上に確認メッセージが表示されます。

    ![ アラート ルールが作成されました](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    ルールは、有効化、無効化、編集、または削除することができます。 アクティビティ ログ ルールの管理の[詳細を確認](#view-and-manage-activity-log-alert-rules)してください。

## <a name="view-and-manage-activity-log-alert-rules"></a>アクティビティ ログ アラート ルールの表示と管理

1. Azure Portal で、**[モニター]** > **[アラート (プレビュー)]** の順にクリックし、ウィンドウの左上にある **[ルールの管理]** をクリックします。

    ![ manage alert rules](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    使用可能なルールの一覧が表示されます。

2. 変更するアクティビティ ログ ルールを検索します。

    ![ アクティビティ ログ アラート ルールの検索](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    編集するアクティビティのルールを検索するには、用意されているフィルターとして **[サブスクリプション]**、**[リソース グループ]**、**[リソース]** を使用できます。

    > [!NOTE]

    > 編集できるのは、**[説明]**、**[対象の条件]**、**[アクション グループ]** のみです。

3.  ルールを選択し、ダブルクリックしてルールのオプションを編集します。 必要な変更を行い、**[保存]** をクリックします。

    ![ manage alert rules](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  ルールは、無効化、有効化、または削除することができます。 手順 2 で説明したようにルールを選択した後、ウィンドウの上部にある適切なオプションを選択します。


## <a name="next-steps"></a>次の手順

- [アクティビティ ログ アラートをアーカイブする](monitoring-archive-activity-log.md)
- [アクティビティ ログをイベント ハブにストリーミングする](monitoring-stream-activity-logs-event-hubs.md)
- [アクティビティ ログに移行する](monitoring-migrate-management-alerts.md)
