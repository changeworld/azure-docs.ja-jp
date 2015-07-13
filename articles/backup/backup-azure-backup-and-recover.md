<properties
   pageTitle="Azure Backup - Windows サーバーまたは Windows クライアントからのバックアップと復元"
   description="Windows サーバーまたは Windows クライアントからバックアップと復元を行う方法について説明します。この記事では、代替サーバーの復旧についても説明します。"
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="04/02/2015"
	 ms.author="prvijay"/>

# Windows サーバーまたは Windows クライアント コンピューターからのバックアップと復元
この記事では、Windows サーバーまたは Windows クライアント コンピューターからのバックアップに必要な手順について説明します。また、同じコンピューターにバックアップ ファイルを復元するために必要な手順と、他のコンピューターにバックアップ ファイルを復元するために必要な手順についても説明します。

## ファイルのバックアップ
1. コンピューターが登録されたら、Microsoft Azure Backup mmc スナップインを開きます。<br/> ![Search result][1]

2. **[バックアップのスケジュール]** をクリックします。<br/> ![Schedule Backup][2]

3. バックアップする項目を選択します。Windows サーバーまたは Windows クライアントで Azure Buckup を使用して (つまり System Center Data Protection Manager を使用しない)、ファイルとフォルダーを保護することができます。<br/> ![Items to Backup][3]

4. バックアップ スケジュールと保持ポリシーを指定します。保持ポリシーについては以降の[記事](backup-azure-backup-cloud-as-tape.md)で詳しく説明します。

5. 最初のバックアップを送信する方法を選択します。最初のシード処理を実施する方法は、バックアップするデータの量と、インターネットのアップロード リンク速度によって決まります。GB または TB 単位のデータを大きな遅延が発生する低帯域幅接続でバックアップする場合は、最寄りの Azure データ センターにディスクを送付して、最初のバックアップを完了することをお勧めします。これは "オフライン バックアップ" と呼ばれ、この[記事](https://msdn.microsoft.com/library/azure/dn894419.aspx)で詳しく説明されています。十分な帯域幅接続が確保できる場合は、ネットワーク経由で最初のバックアップを完了することをお勧めします。<br/> ![Initial Backup][4]

6. ワークフローが完了したら、mmc スナップインに戻り、**[今すぐバックアップ]** をクリックして、ネットワーク経由での最初のシード処理を完了します。<br/> ![Backup now][5]

7. 最初のシード処理が完了すると、Azure Backup コンソールの **[ジョブ]** ビューに状態が表示されます。<br/> ![IR complete][6]

## 同じコンピューターへのデータの回復
ファイルを誤って削除してしまい、バックアップ元と同じコンピューターにファイルまたはボリュームを復元する場合には、次の手順に従ってデータを回復することができます。

1. **Microsoft Azure Backup** スナップインを開きます。

2. **[データの回復]** をクリックして、ワークフローを開始します。<br/> ![Recover Data][7]

3. バックアップ ファイルを同じコンピューターに復元する場合は、**[このサーバー (*コンピューター名*)]** オプションを選択します。<br/> ![Same machine][8]

4. **[ファイルの参照]** または **[ファイルの検索]** を選択することもできます。パスがわかっている 1 つまたは複数のファイルを復元する場合は、既定のオプションをそのまま使用します。フォルダー構造がわからず、ファイルを検索する場合は、**[ファイルの検索]** オプションを選択します。ここでは、既定のオプションを使用します。<br/> ![Browse files][9]

5. 次の画面で、ファイルの復元元となるボリュームを選択します。この画面では、任意の時点から復元できます。カレンダー コントロールに**太字**で表示される日付は、復元ポイントが存在していることを示しています。日付を選択すると、バックアップ スケジュールに基づいて (バックアップ操作が成功していれば)、**[時間]** ドロップダウンから特定の時点を選択できます。<br/> ![Volume and Date][10]

6. 次の画面で、回復する項目を選択します。復元するフォルダーまたはファイルを複数選択することができます。<br/> ![Select files][11]

7. 次の画面で、回復パラメーターを指定します。<br/> ![Recovery options][12]
  + 元の場所に復元することも (ファイルまたはフォルダーが上書きされます)、同じコンピューターの別の場所に復元することもできます。

  + 復元するファイルまたはフォルダーが復元先の場所に存在する場合、コピーを作成するか (同じファイルの 2 つのバージョンが作成されます)、復元先の場所にあるファイルを上書きするか、復元先に存在するファイルの回復をスキップすることができます。

  + 復元されるファイルの ACL を復元するという既定のオプションは、そのままにしておくことを強くお勧めします。

8. これらの入力を指定すると、このコンピューターにファイルを復元する回復ワークフローが開始されます。

## 別のコンピューターへの回復
サーバー全体が失われた場合でも、別のコンピューターにファイルまたはボリュームを回復することは可能です。次の手順はそのワークフローを示しています。

この手順で使用される用語は次のとおりです。+ *ソース コンピューター* – バックアップが実行され、現在は使用できなくなっている元のコンピューター。

  + *ターゲット コンピューター* – データが取得されるコンピューター。

  + *サンプルの資格情報コンテナー* – *ソース コンピューター*と*ターゲット コンピューター*が登録されているバックアップ資格情報コンテナー。<br/>

> [AZURE.NOTE]コンピューターのバックアップは、以前のバージョンのオペレーティング システムが実行されているコンピューターには復元できません。たとえば、バックアップが Windows 7 コンピューターで行われた場合、Windows 8 以上のコンピューターにそのバックアップを復元できます。ただし、その逆は当てはまりません。

1. *ターゲット コンピューター*の **Microsoft Azure Backup** スナップインを開きます。

2. *ターゲット コンピューター*および*ソース コンピューター*が同じバックアップ資格情報コンテナー (この記事では*サンプルの資格情報コンテナー*) に復元されていることを確認します。

3. **[データの回復]** をクリックして、ワークフローを開始します。<br/> ![Recover Data][7]

4. **[別のサーバー]** を選択します。<br/> ![Another Server][13]

5. *サンプルの資格情報コンテナー*に対応する資格情報コンテナーの資格情報ファイルを指定します。資格情報コンテナーの資格情報ファイルが無効である (または有効期限が切れている) 場合、Azure ポータルの*サンプルの資格情報コンテナー*から、新しい資格情報コンテナーの資格情報ファイルをダウンロードします。資格情報コンテナーの資格情報ファイルを指定すると、その資格情報コンテナーの資格情報ファイルに対するバックアップ資格情報コンテナーが表示されます。

6. 表示されているコンピューターの一覧から*ソース コンピューター*を選択します。<br/> ![List of machines][14]

7. 前回と同様に、**[ファイルの検索]** または **[ファイルの参照]** オプションを選択します。ここでは、**[ファイルを検索]** オプションを使用します。<br/> ![Search][15]

8. 次の画面で、ボリュームと日付を選択します。復元するフォルダー名とファイル名を検索します。<br/> ![Search items][16]

9. ファイルの復元先にする必要がある場所を選択します。<br/> ![Restore location][17]

10. *ソース コンピューター*を*サンプルの資格情報コンテナー*に登録する際に指定した暗号化パスフレーズを入力します。<br/> ![Encryption][18]

入力して **[回復]** ボタンをクリックすると、指定した復元先でバックアップ ファイルの復元が開始されます。

<!--Image references-->
[1]: ./media/backup-azure-backup-and-recover/result.png
[2]: ./media/backup-azure-backup-and-recover/schedulebackup.png
[3]: ./media/backup-azure-backup-and-recover/items.png
[4]: ./media/backup-azure-backup-and-recover/initialbackup.png
[5]: ./media/backup-azure-backup-and-recover/backupnow.png
[6]: ./media/backup-azure-backup-and-recover/ircomplete.png

[7]: ./media/backup-azure-backup-and-recover/recover.png
[8]: ./media/backup-azure-backup-and-recover/samemachine.png
[9]: ./media/backup-azure-backup-and-recover/browseandsearch.png
[10]: ./media/backup-azure-backup-and-recover/volanddate.png
[11]: ./media/backup-azure-backup-and-recover/selectfiles.png
[12]: ./media/backup-azure-backup-and-recover/recoveroptions.png

[13]: ./media/backup-azure-backup-and-recover/anotherserver.png
[14]: ./media/backup-azure-backup-and-recover/machinelist.png
[15]: ./media/backup-azure-backup-and-recover/search.png
[16]: ./media/backup-azure-backup-and-recover/searchitems.png
[17]: ./media/backup-azure-backup-and-recover/restorelocation.png
[18]: ./media/backup-azure-backup-and-recover/encryption.png
 

<!---HONumber=62-->