<properties
   pageTitle="Azure Backup エージェントのダウンロード、インストール、および登録 | Microsoft Azure"
   description="Azure Backup エージェントをダウンロードする方法と場所、インストール手順、および資格情報コンテナーの資格情報を使用して Azure Backup エージェントを登録する方法について説明します。"
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""/> <tags ms.service="backup" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="storage-backup-recovery" ms.date="07/01/2015" ms.author="jimpark"; "aashishr"/>

# Azure Backup エージェントのダウンロード、インストール、および登録
Azure Backup 資格情報コンテナーを作成したら、データやアプリケーションを Azure にバックアップするための各オンプレミスのサーバー (Windows Server、Windows クライアントまたは System Center Data Protection Manager サーバー) にエージェントをインストールする必要があります。この記事では、Windows Server または Windows クライアント コンピューターに Azure Backup をセットアップするのに必要な手順について説明します。

1. [管理ポータル](https://manage.windowsazure.com/)にサインインします。

2. **[復旧サービス]** をクリックし、サーバーに登録するバックアップ資格情報コンテナーを選択します。バックアップ資格情報コンテナーの [クイック スタート] ページが表示されます。

    ![クイック スタート](./media/backup-azure-backup-download-register/quickstart.png)

3. [クイック スタート] ページで、**[エージェントのダウンロード]** オプションの **[Windows Server または System Center Data Protection Manager または Windows クライアント用]** をクリックします。**[保存]** をクリックして、ローカル コンピューターにコピーします。

    ![Save agent](./media/backup-azure-backup-download-register/agent.png)

4. エージェントがインストールされたら、MARSAgentInstaller.exe をダブルクリックして Azure Backup エージェントのインストールを起動します。

5. エージェントに必要なインストール フォルダーとスクラッチ フォルダーを選択します。指定されたキャッシュの場所には、バックアップ データの 5% 以上の空き領域が必要です。

6.	プロキシ サーバーを使用してインターネットに接続する場合、**[プロキシ構成]** 画面で、プロキシ サーバーの詳細を入力します。認証済みのプロキシを使用する場合は、この画面でユーザー名とパスワードの詳細を入力します。

7. Azure Backup エージェントは、.NET Framework 4.5 と Windows PowerShell を (まだ使用可能でない場合は) インストールして、インストールを完了します。

8.	エージェントがインストールされたら、**[登録に進む]** ボタンをクリックして、ワークフローを続行します。

    ![Register](./media/backup-azure-backup-download-register/register.png)

9. [資格情報コンテナーの資格情報] 画面で、以前にダウンロードされた資格情報コンテナーの資格情報ファイルを参照して選択します。

    ![Vault credentials](./media/backup-azure-backup-download-register/vc.png)

    > [AZURE.NOTE]資格情報コンテナーの資格情報ファイルは (ポータルからダウンロード後) 48 時間のみ有効です。この画面で何らかのエラー (例: "指定されたコンテナーの資格情報ファイルは期限切れです。") が発生した場合は、Azure ポータルにログインし、資格情報コンテナーの資格情報ファイルを再度ダウンロードします。

    セットアップ アプリケーションがアクセスできる場所に、資格情報コンテナーの資格情報ファイルがあることを確認します。アクセス関連のエラーが発生した場合は、資格情報コンテナーの資格情報ファイルをこのコンピューターの一時的な場所にコピーし、操作をやり直してください。

    資格情報コンテナーの資格情報が無効であるというエラー (例: "無効なコンテナーの資格情報が指定されました。ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。") が発生した場合、ポータルから新しい資格情報コンテナーの資格情報ファイルをダウンロードしてから操作をやり直してください。このエラーは通常、ユーザーが Azure ポータルで [資格情報コンテナー資格情報のダウンロード] オプションを連続でクリックした場合に発生します。この場合、2 番目の資格情報コンテナーの資格情報ファイルだけが有効です。

10. **[暗号化の設定]** 画面で、パスフレーズを生成するか、パスフレーズ (最小 16 文字) を指定し、必ず安全な場所にパスフレーズを保存してください。

    ![暗号化](./media/backup-azure-backup-download-register/encryption.png)

    > [AZURE.WARNING]パスフレーズを紛失または忘れてしまった場合、Microsoft はバックアップ データの回復を支援することはできません。エンド ユーザーは暗号化パスフレーズを所有していますが、Microsoft はエンド ユーザーが使用するパスフレーズを確認できません。回復操作中に必要になる場合もあるため、ファイルは安全な場所に保存してください。

11. **[完了]** ボタンをクリックすると、コンピューターは資格情報コンテナーに正常に登録され、Microsoft Azure へのバックアップを開始できるようになります。Azure Backup mmc スナップインの **[プロパティの変更]** オプションをクリックすることによって、登録ワークフロー時に指定した設定を変更できます。

    ![Change Properties](./media/backup-azure-backup-download-register/change.png)

## 次のステップ
- Azure へのデータとアプリケーションのバックアップについて詳しくは、「[スケジュールされたバックアップ](backup-azure-backup-and-recover.md)」をご覧ください。

<!---HONumber=July15_HO4-->