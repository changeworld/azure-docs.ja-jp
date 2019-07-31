---
title: Azure テーブル ストレージ | Azure Marketplace
description: Azure テーブル ストレージで潜在顧客管理を構成します。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: a53ed93813215655c4a165faa0bce36d9249e8e6
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227899"
---
# <a name="lead-management-instructions-for-table-storage"></a>テーブル ストレージでの潜在顧客管理の手順

この記事では、潜在顧客を管理するために Azure テーブル ストレージを構成する方法について説明します。 テーブル ストレージは、顧客情報を格納および変更するのに役立ちます。

## <a name="configure-table-storage"></a>テーブル ストレージの構成

1. Azure アカウントを持っていない場合は、[無料試用版アカウントを作成](https://azure.microsoft.com/pricing/free-trial/)してください。
1. アカウントがアクティブになった後、[Azure portal](https://portal.azure.com) にサインインします。
1. Azure portal で次の手順を実行します。  
    1. 左側のウィンドウにある **[+ リソースの作成]** を選択します。 **[新規]** ウィンドウが開きます。
    1. **[新規]** ウィンドウで **[ストレージ]** を選択します。 右側に **[おすすめ]** のリストが表示されます。
    1. **ストレージ アカウント**を選択します。 その後、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)」の手順に従います。

    ![Azure のストレージ アカウントの作成](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    ストレージ アカウントについて詳しくは、[クイックスタートのチュートリアル](https://docs.microsoft.com/azure/storage/)をご覧ください。 価格の情報については、「[Azure Storage の価格の概要](https://azure.microsoft.com/pricing/details/storage/)」をご覧ください。

1. ストレージ アカウントがプロビジョニングされるまで待ちます。通常は数分かかります。 次に、Azure portal のホーム ページからアカウントにアクセスします。ナビゲーション ウィンドウで、 **[リソースをすべて表示する]** または **[すべてのリソース]** を選択します。

    ![Azure ストレージ アカウントにアクセスする](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

1. ストレージ アカウントのウィンドウから、キーに対するストレージ アカウントの接続文字列をコピーします。 それを、Cloud パートナー ポータルでストレージ アカウントの **[接続文字列]** フィールドに貼り付けます。

    接続文字列の例:

    ```sql
    DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
    ```

      ![Azure ストレージ キー](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

[Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) または同様のツールを使って、テーブル ストレージ内のデータを表示できます。 それからデータをエクスポートすることもできます。

## <a name="use-microsoft-flow-with-table-storage-optional"></a>テーブル ストレージで Microsoft Flow を使用する ("*省略可能*")

[Microsoft Flow](https://docs.microsoft.com/flow/) を使用すると、テーブル ストレージに潜在顧客が追加されたときに自動的に通知を送信できます。 Microsoft Flow アカウントを持っていない場合は、[無料アカウントにサインアップ](https://flow.microsoft.com/)してください。

### <a name="lead-notification-example"></a>潜在顧客の通知の例

この例では、基本的なフローを作成する方法を示します。 そのフローでは、新しい潜在顧客がテーブル ストレージに追加されると、1 時間ごとにメール通知が自動的に送信されます。

1. Microsoft Flow アカウントにサインインします。
1. 左側のナビゲーション ウィンドウで、 **[マイ フロー]** を選択します。
1. 上部のナビゲーション バーで、 **[+ 新規]** を選択します。  
1. ドロップダウン リストから、 **[+ 一から作成]** を選択します。
1. **[フローを一から作成する]** で、 **[一から作成]** を選択します。

   ![新しいフローを一から作成する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

1. コネクタとトリガーの検索ページで、 **[トリガー]** を選択します。
1. **[トリガー]** で、 **[繰り返し]** を選択します。
1. **[繰り返し]** ウィンドウで、 **[間隔]** の設定を既定の **1** のままにします。 **[頻度]** ドロップダウン リストから、 **[時間]** を選択します。

   >[!NOTE] 
   >この例では、1 時間の間隔を使います。 ただし、ビジネス ニーズに最も適した間隔と頻度を選択できます。

   ![繰り返しとして 1 時間の頻度を設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

1. **[+ 新しいステップ]** を選択します。
1. **[過去の時間を取得]** を探し、 **[アクションを選択してください]** の **[過去の時間を取得]** を選択します。

    ![[過去の時間を取得] アクションを探して選択する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

1. **[過去の時間を取得]** ウィンドウで、 **[間隔]** を **1** に設定します。  **[時間単位]** ドロップダウン リストから、 **[時間]** を選択します。
    >[!IMPORTANT] 
    >**[間隔]** と **[時間単位]** が、繰り返しとして構成した間隔および頻度 (ステップ 8) と一致することを確認します。

    ![過去の時間間隔の取得を設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >いつでもフローをチェックして、各ステップが正しく構成されていることを確認できます。[フロー] メニュー バーから **[フロー チェッカー]** を選択します。

次の手順では、ストレージ テーブルに接続し、新しい潜在顧客を処理するための処理ロジックを設定します。

1. **[過去の時間を取得]** ステップの後で、 **[+ 新しいステップ]** を選択し、 **[エンティティの取得]** を探します。
1. **[アクション]** で、 **[Get entities] (エンティティの取得)** 、 **[詳細オプションを表示する]** の順に選択します。
1. **[エンティティの取得]** ウィンドウで、次のフィールドを指定します。

   - **[テーブル]** : 実際のテーブル ストレージの名前。 次の図では、"MarketPlaceLeads" と入力されています。

     ![Azure テーブル名のカスタム値を選択する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **[フィルター クエリ]** : このフィールドを選択すると、ポップアップ ウィンドウに **[過去の時間を取得]** アイコンが表示されます。 **[過去の時間]** を選択し、この値をクエリのフィルター処理のタイムスタンプとして使います。 または、フィールドに次の関数を貼り付けてもかまいません。
   
      `CreatedTime Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![フィルター クエリ関数を設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

1. **[新しいステップ]** を選択し、テーブル ストレージで新しい潜在顧客をスキャンする条件を追加します。

   ![[新しいステップ] を使ってテーブル ストレージをスキャンする条件を追加する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

1. **[アクションの選択]** ウィンドウで、 **[アクション]** を選択してから、 **[条件コントロール]** を選択します。

     ![条件コントロールを追加する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

1. **[条件]** ウィンドウで、 **[値の選択]** を選択してから、ポップアップ ウィンドウで **[式]** を選択します。
1. ***[fx]*** フィールドに `length(body('Get_entities')?['value'])` を貼り付けます。 **[OK]** を選択して、この関数を追加します。 



     ![条件に関数を追加する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

1. 条件の結果に基づいて実行するアクションを設定します。

    1. ドロップダウン リストから **[が次の値より大きい]** を選択します。
   1. 値として「**0**」を入力します。

     ![条件の結果に基づくアクションを設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

1. 条件を解決すると "いいえの場合" は、何もしません。

    条件を解決すると "はいの場合" は、メールを送信するために Office 365 アカウントに接続するアクションをトリガーします。
   1. **[アクションの追加]** を選択します。
   1. **[電子メールを送信する]** を選択します。
   1. **[電子メールを送信する]** ウィンドウで、次のフィールドに情報を入力します。

      - **[宛先]** : 通知を受け取るすべてのユーザーのメール アドレス。
      - **[件名]** : メールの件名。 例: "*新しい潜在顧客!* "
      - **[本文]** : 各メールに含めるテキスト (省略可能)。 また、潜在顧客の情報を挿入する関数として `body('Get_entities')?['value']` を貼り付けます。

        >[!NOTE] 
        >メールの本文に静的または動的なデータ ポイントを追加挿入できます。

      ![潜在顧客の通知の電子メールを設定する](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

1. **[保存]** を選択してフローを保存します。 Microsoft Flow により、エラーが自動的にテストされます。 エラーがない場合は、保存された後、フローが実行を開始します。

    次の図では、最終的なフローの例が示されています。

    [![最終的なフローのシーケンス](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

    ("*図を選択すると拡大されます。* ")

### <a name="manage-your-flow"></a>フローを管理する

実行後のフローを簡単に管理できます。 そのフローを完全に制御できます。 たとえば、停止、編集、実行履歴の表示、分析の取得などが可能です。 次の図では、フロー管理のオプションが示されています。

 ![フロー管理のオプション](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

**[フローを無効にする]** を選択するまで、フローは実行され続けます。

潜在顧客のメール通知を何も受け取らない場合は、テーブル ストレージに新しい潜在顧客が追加されていません。
フローで障害が発生した場合は、次の例のようなメールが届きます。

 ![フローのエラーの電子メール通知](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)

## <a name="next-steps"></a>次の手順

[潜在顧客の構成](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
