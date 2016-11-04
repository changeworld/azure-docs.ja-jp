---
title: バックアップ コンテナーで別の DPM サーバーからデータを復元する | Microsoft Docs
description: コンテナーに登録されている DPM サーバーから、Azure Backup コンテナーに保護しているデータを復元します。
services: backup
documentationcenter: ''
author: nkolli1
manager: shreeshd
editor: ''

ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: giridham;jimpark;trinadhk;markgal

---
# バックアップ コンテナーで別の DPM サーバーからデータを復元する
コンテナーに登録されている DPM サーバーから、Azure Backup コンテナーに保護しているデータを復元できるようになりました。この復元プロセスは DPM 管理コンソールに完全統合されており、その他の復元ワークフローと似ています。

バックアップ コンテナーで別の DPM サーバーからデータを復元するには、[System Center Data Protection Manager UR7](https://support.microsoft.com/ja-JP/kb/3065246) と[最新の Azure Backup エージェント](http://aka.ms/azurebackup_agent)が必要です。

## 別の DPM サーバーからデータを復元する
別の DPM サーバーからデータを復元するには:

1. DPM 管理コンソールの **[復元]** タブで、(画面の左上にある) **[外部 DPM の追加]** をクリックします。
   
    ![外部 DPM の追加](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. データを復元する **DPM サーバー**に関連付けられているコンテナーから新しい**コンテナー資格情報**をダウンロードし、バックアップ コンテナーに登録されている DPM サーバーの一覧から DPM サーバーを選択し、データを復元する DPM サーバーに関連付けられている**暗号化パスフレーズ**を指定します。
   
    ![外部 DPM の資格情報](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)
   
   > [!NOTE]
   > 同じ登録コンテナーに関連付けられている DPM サーバーのみ互いのデータを復元できます。
   > 
   > 
   
    外部 DPM サーバーが追加されたら、**[回復]** タブから外部 DPM サーバーとローカル DPM サーバーのデータを閲覧できます。
3. 外部 DPM サーバーで保護されている本稼働サーバーの一覧を閲覧し、該当するデータ ソースを選択します。
   
    ![外部 DPM サーバーの参照](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. **[回復ポイント]** ドロップダウンから **[月/年]** を選択し、回復ポイントが作成された **[回復日]** を選択し、**[回復時刻]** を選択します。
   
    ファイルとフォルダーの一覧が下のページに表示されるので、閲覧し、任意の場所に復元できます。
   
    ![外部 DPM サーバーの回復ポイント](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. 該当する項目を右クリックし、**[回復]** をクリックします。
   
    ![外部 DPM 回復](./media/backup-azure-alternate-dpm-server/recover.png)
6. **[回復の選択]** を確認します。回復するバックアップ コピーの日時とバックアップ コピーの作成元を確認します。選択が間違っている場合、**[キャンセル]** をクリックして [回復] タブに戻り、適切な回復ポイントを選択します。選択が正しければ、**[次へ]** をクリックします。
   
    ![外部 DPM 回復の概要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. **[別の場所に回復する]** を選択します。**[参照]** で適切な回復場所を開きます。
   
    ![外部の DPM 回復の別場所](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. **[コピーの作成]**、**[スキップ]**、**[上書き]** に関連付けられているオプションを選択します。
   
   * **[コピーの作成]** は、名前の競合がある場合、ファイルのコピーを作成します。
   * **[スキップ]** は、名前の競合がある場合、ファイルの復元を省略します。
   * **[上書き]** は、名前の競合がある場合、指定された場所に既存のコピーを上書きします。
     
     **[セキュリティの復元]** のオプションを選択します。データを復元する復元先コンピューターのセキュリティ設定または回復ポイントが作成された時点で生成物に適用されたセキュリティを適用できます。
     
     回復が完了したら、**通知**を送信するかどうかを確認します。
     
     ![外部の DPM 回復の通知](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. **概要**画面には、これまでに選択したオプションが一覧表示します。**[回復]** をクリックすると、該当するオンプレミスの場所にデータが復元されます。
   
    ![外部 DPM 回復のオプション概要](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)
   
   > [!NOTE]
   > DPM サーバーの **[監視]** タブで回復ジョブを監視できます。
   > 
   > 
   
    ![回復の監視](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. DPM サーバーの **[回復]** タブの **[外部 DPM の消去]** をクリックし、外部 DPM サーバーのビューを削除できます。
    
    ![外部 DPM の消去](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## エラー メッセージのトラブルシューティング
| いいえ。 | エラー メッセージ | トラブルシューティングの手順 |
|:---:|:--- |:--- |
| 1\. |このサーバーは資格情報コンテナーが指定するコンテナーに登録されていません。 |**原因:** 選択した資格情報コンテナー ファイルが回復対象の DPM サーバーに関連付けられているバックアップ コンテナーに属さないとき、このエラーが表示されます。<br> **解決策:** DPM サーバーが登録されているバックアップ コンテナーから資格情報コンテナー ファイルをダウンロードします。 |
| 2\. |回復可能なデータがないか、選択したサーバーが DPM サーバーではありません。 |**原因:** DPM 2012 R2 UR7 の DPM サーバーが他にバックアップ コンテナーに登録されていないか、DPM 2012 R2 UR7 の DPM サーバーでメタデータがまだアップロードされていないか、選択したサーバーが DPM サーバー (別名、Windows Server または Windows Client)ではありません。<br> **解決策:** 他の DPM サーバーがバックアップ コンテナーに登録されている場合、SCDPM 2012 R2 UR7 と最新の Azure Backup エージェントがインストールされていることを確認します。<br>他の DPM サーバーが DPM 2012 R2 UR7 のバックアップ コンテナーに登録されている場合、UR7 のインストール後、1 日待ってから回復プロセスを開始します。以前にクラウドに保護されたすべてのバックアップのメタデータを夜間ジョブがアップロードします。このデータを回復に利用できます。 |
| 3\. |このコンテナーには他の DPM サーバーが登録されていません。 |**原因:** DPM 2012 R2 UR7 以降がインストールされた他の DPM サーバーが回復元のコンテナーに登録されていません。<br>**解決策:** 他の DPM サーバーがバックアップ コンテナーに登録されている場合、SCDPM 2012 R2 UR7 と最新の Azure Backup エージェントがインストールされていることを確認します。<br>DPM 2012 R2 UR7 がインストールされているバックアップ コンテナーに他の DPM サーバーが登録されている場合、UR7 のインストール後、1 日待ってから回復プロセスを開始します。以前にクラウドに保護されたすべてのバックアップのメタデータを夜間ジョブがアップロードします。このデータを回復に利用できます。 |
| 4\. |指定した暗号化パスフレーズが次のサーバーに関連付けられているパスフレーズと一致しません。**<サーバー名>** |**原因:** 回復対象の DPM サーバーのデータからデータを暗号化する過程で使用された暗号化パスフレーズが指定した暗号化パスフレーズに一致しません。エージェントはデータを復号できません。そのため、回復に失敗します。<br>**解決策:** データを回復する DPM サーバーに関連付けられている暗号化パスフレーズとまったく同じものを指定してください。 |

## よく寄せられる質問:
1. **UR7 と最新の Azure Backup エージェントをインストールしましたが、別の DPM サーバーから外部 DPM サーバーを追加できません。なぜですか。**
   
    A) (更新プログラムのロールアップ 7 以前の更新プログラムのロールアップを利用し) データ ソースがクラウドに保護されている既存の DPM サーバーの場合、UR7 と最新の Azure Backup エージェントをインストールした後、1 日待ってから*外部 DPM サーバーの追加*を開始する必要があります。これは DPM 保護グループのメタデータを Azure にアップロードするために必要です。最初にこれは夜間ジョブで行われます。
2. **Azure Backup エージェントのバージョンの最小要件は何ですか。**
   
    A) この機能を有効にするには 2.0.8719.0 以降の Azure Backup エージェントが必要です。[コントロール パネル] **>** [すべてのコントロール パネル項目] **>** [プログラムと機能] **>** [Microsoft Azure Recovery Services Agent] の順に進むと、Azure Backup エージェントのバージョンを確認できます。バージョンが 2.0.8719.0 以前の場合、[最新の Azure Backup エージェント](https://go.microsoft.com/fwLink/?LinkID=288905)をダウンロードし、インストールしてください。
   
    ![外部 DPM の消去](./media/backup-azure-alternate-dpm-server/external-dpm-azurebackupagentversion.png)

## 次のステップ:
• [Azure Backup FAQ](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_0810_2016-->
