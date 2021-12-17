---
title: Azure Automation の Start/Stop VMs during off-hours を有効にする
description: この記事では、Azure VM の Start/Stop VMs during off-hours 機能を有効にする方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 05/18/2021
ms.topic: conceptual
ms.openlocfilehash: 70b58b689798b6651a88674668497a27a2780429
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450934"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Start/Stop VMs during off-hours を有効にする

このトピックの手順を順に行って、新規または既存の Automation アカウントおよびリンクされた Log Analytics ワークスペースを使用して、VM の Start/Stop VMs during off-hours 機能を有効にします。 セットアップ プロセスが完了したら、機能をカスタマイズする変数を構成します。

## <a name="enable-and-configure"></a>有効化と構成 

1. Azure [Portal](https://portal.azure.com) にサインインします。
2. **Automation アカウント** を検索して選択します。
3. **[Automation アカウント]** ページで、一覧からお使いの Automation アカウントを選択します。
4. Automation アカウントから、 **[関連リソース]** の下にある **[VM の開始/停止]** を選択します。 この画面では、 **[Learn more about and enable the solution]\(ソリューションの詳細と有効化\)** をクリックして確認できます。 この機能が既にデプロイされている場合は、 **[ソリューションの管理]** をクリックし、それを一覧から見つけることができます。

   ![Automation アカウントから有効にする](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Azure portal のどこからでも、 **[リソースの作成]** をクリックしてリソースを作成できます。 [Marketplace] ページで、「**Start**」、「**Start/Stop**」などのキーワードを入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 または、機能のフルネームから 1 つ以上のキーワードを入力し、**Enter** キーを押すこともできます。 検索結果から **Start/Stop VMs during off-hours** を選択します。

5. 選択したデプロイの [Start/Stop VMs during off-hours] ページで概要を確認し、 **[作成]** をクリックします。

   ![Azure portal](media/automation-solution-vm-management/azure-portal-01.png)

   リソースが作成されると、[ソリューションの追加] ページが表示されます。 Automation アカウントにインポートする前に、機能を構成するように求められます。

   ![VM 管理の [ソリューションの追加] ページ](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

6. **[ソリューションの追加]** ページで、 **[ワークスペース]** を選択します。 既存の Log Analytics ワークスペースを一覧から選択します。 ワークスペースと同じサポート対象リージョンに Automation アカウントが存在しない場合は、次の手順で新しい Automation アカウントを作成できます。 

   > [!NOTE]
   > 機能を有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](how-to/region-mappings.md)に関する記事をご覧ください。

7. ワークスペースとして利用できる Automation アカウントがサポート対象リージョンに存在しない場合は、 **[ソリューションの追加]** ページで **[Automation アカウント]** を選択します。 新しい Automation アカウントを作成してそこに関連付けることができます。 **[Automation アカウントの作成]** を選択し、 **[Automation アカウントの追加]** ページの **[名前]** フィールドに Automation アカウントの名前を入力してください。

    それ以外のオプションはすべて、選択した Log Analytics ワークスペースに基づいて自動的に入力されます。 これらのオプションは変更できません。 この機能に含まれている Runbook は、Azure 実行アカウントが既定の認証方法になります。 
    
    **[OK]** をクリックすると、構成オプションが検証され、Automation アカウントが作成されます。 進行状況は、メニューの **[通知]** で追跡できます。

8. [ソリューションの追加] ページで、 **[パラメーターの構成]** を選択します。 **[パラメーター]** ページが表示されます。

    ![ソリューションの [パラメーター] ページ](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

9. **[Target ResourceGroup Names]\(ターゲット リソース グループ名\)** フィールドの値を指定します。 このフィールドは、管理する機能の VM を含むグループ名を定義します。 複数の名前を入力する場合は、それぞれをコンマで区切ってください (値の大文字と小文字は区別されません)。 ワイルドカードを使用して、サブスクリプション内の全リソース グループの VM を対象にすることもできます。 値は `External_Start_ResourceGroupNames` および `External_Stop_ResourceGroupNames` 変数に格納されます。

    > [!IMPORTANT]
    > **[Target ResourceGroup Names]\(ターゲット リソース グループ名\)** の既定値は **&ast;** です。 この設定は、サブスクリプション内のすべての VM が対象です。 サブスクリプション内のすべての VM を対象にする機能を使用しない場合は、スケジュールを選択する前に、リソース グループ名の一覧を指定する必要があります。
  
10. **[VM 除外リスト (文字列)]** フィールドの値を指定します。 この値は、ターゲット リソース グループの 1 つ以上の仮想マシンの名前です。 複数の名前を入力する場合は、それぞれをコンマで区切ってください (値の大文字と小文字は区別されません)。 ワイルドカードの使用がサポートされています。 この値は、`External_ExcludeVMNames` 変数に格納されます。
  
11. **[スケジュール]** フィールドを使用して、機能による VM 管理のスケジュールを選択します。 スケジュールの開始日時を選択して、選択した時刻に開始する定期的な日単位のスケジュールを作成します。 別のリージョンを選択することはできません。 機能の構成後、スケジュールを特定のタイム ゾーンに構成するには、「[起動および停止スケジュールの変更](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)」を参照してください。

12. [アクション グループ](../azure-monitor/alerts/action-groups.md)からメール通知を受信するには、 **[メール通知]** フィールドで既定値の **[はい]** を使用し、有効なメール アドレスを指定します。 **[いいえ]** を選択したものの、後日、電子メール通知を受信することにした場合は、コンマで区切られた有効なメール アドレスで作成されたアクション グループを更新することができます。 次のアラート ルールがサブスクリプションに作成されます。

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

13. 機能に必要な初期設定を構成した後、 **[OK]** をクリックして **[パラメーター]** ページを閉じます。

14. **Create** をクリックしてください。 すべての設定が検証された後、機能はご利用のサブスクリプションにデプロイされます。 このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。

    > [!NOTE]
    > Azure クラウド ソリューション プロバイダー (Azure CSP) サブスクリプションがある場合は、デプロイ完了後に、Automation アカウントで、 **[共有リソース]** の **[変数]** に移動し、[External_EnableClassicVMs](automation-solution-vm-management.md#variables) 変数を **False** に設定します。 これにより、ソリューションによるクラシック VM リソースの検索が停止します。

## <a name="create-alerts"></a>アラートを作成する

Start/Stop VMs during off-hours には、事前に定義された一連の Automation ジョブ アラートは含まれていません。 Runbook ジョブの結果に関連して Automation アカウントから転送されるログ データについて、また DevOps や運用プロセス、運用作業を支援するジョブ失敗アラートの作成方法については、「[Azure Monitor ログにジョブ データを転送する](automation-manage-send-joblogs-log-analytics.md#azure-monitor-log-records)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 機能を設定するには、「[Start/Stop VMs during off-hours を構成する](automation-solution-vm-management-config.md)」を参照してください。
* 機能のエラーを解決するには、「[Start/Stop VMs during off-hours の問題のトラブルシューティング](troubleshoot/start-stop-vm.md)」を参照してください。