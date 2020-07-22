---
title: Azure Automation での Azure VM の更新プログラムとパッチの管理
description: この記事では、Update Management を使用して Azure VM の更新プログラムとパッチを管理する方法を説明します。
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 04/06/2020
ms.custom: mvc
ms.openlocfilehash: a701a5a9fd77bd801bb535fe1f26bfa17c97757b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185791"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Azure VM の更新プログラムとパッチの管理

この記事では、Azure Automation の [Update Management](automation-update-management.md) 機能を使用して、Azure VM の更新プログラムとパッチを管理する方法について説明します。 価格情報については、[Update Management の Automation の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。

> [!NOTE]
> Update Management では、ファーストパーティの更新プログラムの展開とパッチの事前ダウンロードがサポートされています。 このサポートには、パッチ適用対象のシステムに対する変更が必要になります。 お使いのシステムでこれらの設定を構成する方法については、「[Azure Automation Update Management 用に Windows Update の設定を構成する](automation-configure-windows-update.md)」を参照してください。

この記事の手順を使用する前に、次のいずれかの方法を使用して、VM で Update Management を確実に有効にしてください。

* [Automation アカウントから Update Management を有効にする](automation-onboard-solutions-from-automation-account.md)
* [Azure portal を参照して Update Management を有効にする](automation-onboard-solutions-from-browse.md)
* [Runbook から Update Management を有効にする](automation-onboard-solutions.md)
* [Azure VM から Update Management を有効にする](automation-onboard-solutions-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>展開のスコープを制限する

Update Management では、ワークスペース内のスコープ構成を使用して、更新プログラムを受信するコンピューターを対象として指定します。 詳細については、[Update Management の展開スコープの制限](automation-scope-configurations-update-management.md)に関するページを参照してください。

## <a name="view-update-assessment"></a>更新の評価を確認する

更新の評価を表示するには:

1. Automation アカウントの **[更新の管理]** で **[更新の管理]** を選択します。 

2. ご使用の環境の更新プログラムが、[更新の管理] ページに一覧表示されます。 更新プログラムが不足していると識別された場合は、 **[不足している更新プログラム]** タブに、足りない更新プログラムの一覧が表示されます。

3. **[情報リンク]** で、更新プログラムのリンクを選択して、更新プログラムに関する重要な情報を提供するサポート記事を開きます。

    ![更新ステータスを確認する](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

4. 更新プログラムのほかの場所をクリックすると、[ログ検索] ペインが開きます。 その更新プログラムについて、ログ検索のクエリが事前に定義されています。 詳細情報を表示するには、このクエリを変更するか、独自のクエリを作成します。

    ![更新ステータスを確認する](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>アラートを構成する

更新プログラムの展開の状態を通知するアラートを設定するには、次の手順に従います。

1. お使いの Automation アカウントで、 **[監視]** の **[アラート]** に移動し、 **[新しいアラート ルール]** をクリックします。

2. [アラート ルールの作成] ページでは、お使いの Automation アカウントがリソースとして既に選択されています。 これを変更する場合は、 **[リソースの編集]** をクリックします。 

3. [リソースを選択してください] ページで、 **[リソースの種類でフィルター]** ドロップダウン メニューから **[Automation アカウント]** を選択します。 

4. 使用する Automation アカウントを選択し、 **[完了]** をクリックします。

5. **[条件の追加]** をクリックして、ご自分の更新プログラムの展開に適したシグナルを選択します。 次の表は、使用できる 2 つのシグナルの詳細を示しています。

    |シグナル名|Dimensions|説明
    |---|---|---|
    |`Total Update Deployment Runs`|- Update Deployment Name (更新プログラムの展開の名前)<br>- Status (状態)    |更新プログラムの展開の全体的な状態に関するアラート。|
    |`Total Update Deployment Machine Runs`|- Update Deployment Name (更新プログラムの展開の名前)</br>- Status (状態)</br>- Target Computer (ターゲット コンピューター)</br>- Update Deployment Run ID (更新プログラムの展開の実行 ID)    |特定のマシンを対象とした更新プログラムの展開の状態に関するアラート。|

6. ディメンションには、リストから有効な値を選択します。 必要な値がリストにない場合は、ディメンションの横にある **\+** をクリックしてカスタム名を入力します。 その後、検索対象の値を選択します。 ディメンションにすべての値を選択したい場合は、 **[選択]\*** ボタンをクリックします。 ディメンションに値を選択しないと、Update Management ではそのディメンションが無視されます。

    ![シグナル ロジックの構成](./media/automation-tutorial-update-management/signal-logic.png)

7. **[アラート ロジック]** の下の、 **[時間の集計]** と **[しきい値]** のフィールドに値を入力し、 **[完了]** をクリックします。

8. 次のペインで、アラートの名前と説明を入力します。

9. **[重大度]** フィールドを、成功した実行には **[Informational(Sev 2)]\(情報 (重大度 2)\)** に設定し、失敗した実行には **[Informational(Sev 1)]\(情報 (重大度 1)\)** に設定します。

    ![シグナル ロジックの構成](./media/automation-tutorial-update-management/define-alert-details.png)

10. アラート ルールを有効にする方法に応じて、 **[はい]** または **[いいえ]** をクリックします。

11. このルールにアラートを使用しない場合は、 **[アラートを表示しない]** を選択します。

## <a name="configure-action-groups-for-your-alerts"></a>アラートにアクション グループを構成する

アラートを構成したら、アクション グループを設定できます。これは複数のアラートに対して使用するアクションのグループです。 このアクションには、メール通知、Runbook、Webhook などを含めることができます。 アクション グループの詳細については、[アクション グループの作成および管理](../azure-monitor/platform/action-groups.md)に関するページを参照してください。

1. アラートを選択し、 **[アクション グループ]** で **[新規作成]** を選択します。 

2. アクション グループの完全な名前と短い名前を入力します。 Update Management では、指定のグループを使用して通知を送信する場合に短縮名を使用します。

3. **[アクション]** で、アクションを指定する名前 (**電子メール通知**など) を入力します。 

4. **[アクションの種類]** には、適切な種類 (**メール、SMS、プッシュ、音声**など) を選択します。 

5. **[詳細の編集]** をクリックします。

6. アクションの種類のペインに入力します。 たとえば、**メール、SMS、プッシュ、音声**を使用している場合は、アクション名を入力し、 **[電子メール]** チェックボックスをオンにして、有効なメール アドレスを入力し、 **[OK]** をクリックします。

    ![電子メール アクション グループの構成](./media/automation-tutorial-update-management/configure-email-action-group.png)

7. [アクション グループの追加] ペインで、 **[OK]** をクリックします。

8. アラート メールでは、メールの件名をカスタマイズできます。 **[ルールの作成]** で **[アクションをカスタマイズする]** を選択し、 **[メールの件名]** を選択します。 

9. 完了したら、 **[アラート ルールの作成]** をクリックします。 

## <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムの展開をスケジュールすると、対象マシンへの更新プログラムの展開を処理する **Patch-MicrosoftOMSComputers**Runbook にリンクされた[スケジュール](shared-resources/schedules.md) リソースが作成されます。 リリース スケジュールとサービス期間に従って展開をスケジュールし、更新プログラムをインストールする必要があります。 展開に含める更新プログラムの種類を選択できます。 たとえば、緊急更新プログラムやセキュリティ更新プログラムを追加し、更新プログラムのロールアップを除外できます。

>[!NOTE]
>展開の作成後に Azure portal または PowerShell を使用してこのスケジュール リソースを削除した場合、スケジュールされた更新プログラムの展開が破棄され、ポータルからスケジュール リソースを再構成しようとするとエラーが表示されます。 スケジュール リソースは、対応するデプロイ スケジュールを削除することによってのみ削除することができます。  

新しい更新プログラムの展開をスケジュールするには:

1. ご自分の Automation アカウントで、 **[更新の管理]** の **[更新の管理]** に移動し、 **[更新プログラムの展開のスケジュール]** を選択します。

2. **[新しい更新プログラムの展開]** の下の **[名前]** フィールドを使用して、展開に一意の名前を入力します。

3. 更新プログラムの展開の対象となるオペレーティング システムを選択します。

4. **[更新するグループ (プレビュー)]** 領域で、サブスクリプション、リソース グループ、場所、タグを組み合わせたクエリを定義し、展開に含める Azure VM の動的グループを構築します。 詳細については、「[Update Management を利用して動的グループを使用する](automation-update-management-groups.md)」を参照してください。

5. **[更新するマシン]** 領域で、保存した検索またはインポートしたグループを選択するか、ドロップダウン メニューから **[マシン]** を選択し、個々のマシンを選択します。 このオプションを使用すると、各マシンの Log Analytics エージェントの準備状況を確認できます。 Azure Monitor ログでコンピューター グループを作成するさまざまな方法については、[Azure Monitor ログのコンピューター グループ](../azure-monitor/platform/computer-groups.md)に関するページを参照してください

6. **[更新プログラムの分類]** 領域を使用して、製品の[更新プログラムの分類](automation-view-update-assessments.md#work-with-update-classifications)を指定します。 製品ごとに、更新プログラムの展開に含めるものを除き、サポート対象の更新プログラムの分類すべての選択を解除します。

7. **[更新プログラムの包含/除外]** 領域を使用して、展開の特定の更新プログラムを選択します。 [包含/除外] ページには、包含または除外するサポート技術情報の記事の ID 番号別に更新プログラムが表示されます。 
    
   > [!IMPORTANT]
   > 包含より除外が優先されることを覚えておいてください。 たとえば、`*` の除外ルールを定義すると、Update Management ではすべてのパッチまたはパッケージがインストールから除外されます。 除外されたパッチは、マシンに不足しているものとして表示されます。 Linux マシンでは、除外された依存パッケージを含むパッケージを含めた場合、メイン パッケージはインストールされません。

   > [!NOTE]
   > 置き換え済みの更新プログラムを更新プログラムの展開に含めるように指定することはできません。

8. **[スケジュール設定]** を選択します。 既定の開始時刻は、現在の時刻の 30 分後です。 開始時刻は、10 分後以降の将来の任意の時点に設定できます。

9. **[繰り返し]** フィールドを使用して、展開を 1 回だけ実行するか、定期的なスケジュールを使用するかどうかを指定し、 **[OK]** をクリックします。

10. **[事前スクリプトと事後スクリプト (プレビュー)]** 領域で、展開の前後に実行するスクリプトを選択します。 詳細については、「[事前スクリプトと事後スクリプトを管理する](pre-post-scripts.md)」を参照してください。
    
11. **[メンテナンス期間 (分)]** フィールドを使用して、更新プログラムをインストールするために許容される時間を指定します。 メンテナンス期間を指定するときは、次の詳細を考慮してください。

    * メンテナンス期間によって、インストールされる更新プログラムの数が制御されます。
    * メンテナンス期間の終了が近づいている場合でも、Update Management では、新しい更新プログラムのインストールは停止されません。
    * メンテナンス期間を超過した場合でも、進行中の更新は終了されません。
    * Windows でメンテナンス期間が超過した場合、Service Pack の更新プログラムのインストールに時間がかかることが原因であることがよくあります。

    > [!NOTE]
    > Ubuntu でメンテナンス期間外に更新プログラムが適用されないようにするには、`Unattended-Upgrade` パッケージを再構成して自動更新を無効にします。 パッケージの構成方法については、[Ubuntu サーバー ガイドの自動更新に関するトピック](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)をご覧ください。

12. **[再起動のオプション]** フィールドを使用して、展開中の再起動の処理方法を指定します。 次のオプションを使用できます。 
    * 必要に応じて再起動 (既定値)
    * 常に再起動
    * 再起動しない
    * 再起動のみ (このオプションでは更新プログラムはインストールされません)

    > [!NOTE]
    > **[再起動のオプション]** が **[再起動しない]** に設定されている場合、「[再起動の管理に使われるレジストリ キー](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)」に記載されているレジストリ キーにより、再起動イベントが発生する可能性があります。

13. 展開スケジュールの構成が完了したら、 **[作成]** をクリックします。

    ![更新プログラムのスケジュール設定ウィンドウ](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

14. 状態ダッシュボードに戻ります。 **[スケジュールされた更新プログラムの展開]** を選択して、作成した展開スケジュールを表示します。

## <a name="schedule-an-update-deployment-programmatically"></a>プログラムで更新プログラムの展開をスケジュールする

REST API を使用して更新プログラムの展開を作成する方法については、「[ソフトウェア更新プログラムの構成 - 作成](/rest/api/automation/softwareupdateconfigurations/create)」を参照してください。 

サンプル Runbook を使用して、週単位の更新プログラムの展開を作成できます。 この Runbook について詳しくは、「[Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)」(リソース グループ内の VM に対して週単位の更新プログラムのデプロイを作成する) をご覧ください。

## <a name="check-deployment-status"></a>展開の状態を確認する

スケジュールされた展開の開始後、 **[更新の管理]** の **[更新プログラムの展開]** タブに、その状態が表示されます。 展開が現在実行中の場合、状態は **[処理中]** と表示されます。 展開が正常に終了すると、状態が **[成功]** に変わります。 展開に含まれる 1 つ以上の更新プログラムでエラーが発生した場合、状態は **[Partially failed]\(部分的に失敗\)** になります。

## <a name="view-results-of-a-completed-update-deployment"></a>完了した更新プログラムの展開の結果を表示する

展開が完了したら、それを選択してそのダッシュボードを表示することができます。

![特定の展開に関する更新プログラムの展開ステータスのダッシュボード](./media/automation-tutorial-update-management/manageupdates-view-results.png)

**[更新プログラムを実行した結果]** には、ターゲット VM 上の更新プログラムの合計数と展開結果が表示されます。 右側の表には、更新プログラムの詳細な内訳とそれぞれのインストール結果が示されます。

使用できる値は次のとおりです。

* **試行されていません** - 定義されたメンテナンス期間に基づく利用可能な時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* **選択されていません** - 更新プログラムが展開用に選択されていませんでした。
* **成功** - 更新できました。
* **失敗** - 更新できませんでした。

展開によって作成されたログ エントリをすべて表示するには、 **[すべてのログ]** を選択します。

ターゲット VM での更新プログラムの展開を管理する Runbook のジョブ ストリームを確認するには、 **[出力]** を選択します。

展開で発生したエラーの詳細情報を確認するには、 **[エラー]** を選択します。

## <a name="view-the-deployment-alert"></a>展開アラートを表示する

更新プログラムの展開が完了すると、展開のセットアップ時に指定したアラートが表示されます。 たとえば、パッチを確認する電子メールを次に示します。

![電子メール アクション グループの構成](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>次のステップ

* スコープ構成の詳細については、「[Update Management のスコープ構成を操作する](automation-scope-configurations-update-management.md)」を参照してください。
* Log Analytics ワークスペースに格納されているログを検索する必要がある場合は、[Azure Monitor ログでのログ検索](../azure-monitor/log-query/log-query-overview.md)に関する記事を参照してください。
* 展開が終了した場合は、「[Update Management の Automation アカウントからワークスペースのリンクを解除する](automation-unlink-workspace-update-management.md)」を参照してください。
* Update Management から VM を削除するには、「[Update Management から VM を削除する](automation-remove-vms-from-update-management.md)」を参照してください。
* Update Management の一般的なエラーのトラブルシューティングについては、「[Update Management ソリューションに関する問題のトラブルシューティング](troubleshoot/update-management.md)」を参照してください。
* Windows Update エージェントに関する問題のトラブルシューティングについては、「[Windows Update エージェントの問題をトラブルシューティングする](troubleshoot/update-agent-issues.md)」を参照してください。
* Linux Update エージェントに関する問題のトラブルシューティングについては、「[Linux Update エージェントに関する問題のトラブルシューティング](troubleshoot/update-agent-issues-linux.md)」を参照してください。
