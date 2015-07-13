<properties
   pageTitle="Azure Backup エージェントのダウンロード、インストール、および登録"
   description="Azure Backup エージェントをダウンロードする方法と場所、インストール手順、および資格情報コンテナーの資格情報を使用して Azure Backup エージェントを登録する方法について説明します。"
   services="backup"
   documentationCenter=""
   authors="prvijay"
   manager="shreeshd"
   editor=""/>
<tags
   ms.service="backup"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="storage-backup-recovery"
   ms.date="04/08/2015"
   ms.author="prvijay"/>

# Azure Backup エージェントのダウンロード、インストール、および登録

Azure Backup 資格情報コンテナーを作成したら、データやアプリケーションを Azure にバックアップするための各オンプレミスのサーバー (Windows Server、Windows クライアントまたは System Center Data Protection Manager サーバー) にエージェントをインストールする必要があります。この記事では、Windows Server または Windows クライアント コンピューターに Azure Backup をセットアップするのに必要な手順について説明します。

1. [管理ポータル](https://manage.windowsazure.com/)にサインインします。

2. **[復旧サービス]** をクリックし、サーバーに登録するバックアップ資格情報コンテナーを選択します。バックアップ資格情報コンテナーの [クイック スタート] ページが表示されます。<br/> ![クイック スタート][1]

3. [クイック スタート] ページで、**[エージェントのダウンロード]** オプションの **[Windows Server または System Center Data Protection Manager または Windows クライアント用]** をクリックします。[保存] をクリックして、ローカル コンピューターにコピーします。<br/> ![Save agent][2]

4. エージェントがインストールされたら、MARSAgentInstaller.exe をダブルクリックして Azure Backup エージェントのインストールを起動します。エージェントに必要なインストール フォルダーとスクラッチ フォルダーを選択します。指定されたキャッシュの場所には、バックアップ データの 5% 以上の空き領域が必要です。

5.	プロキシ サーバーを使用してインターネットに接続する場合、**[プロキシ構成]** 画面で、プロキシ サーバーの詳細を入力します。認証済みのプロキシを使用する場合は、この画面でユーザー名とパスワードの詳細を入力します。

6.	Azure Backup エージェントは、.NET Framework 4.5 と Windows PowerShell を (まだ使用可能でない場合は) インストールして、インストールを完了します。

7.	エージェントがインストールされたら、**[登録に進む]** ボタンをクリックして、ワークフローを続行します。<br/> ![Register][3]

8. [資格情報コンテナーの資格情報] 画面で、以前にダウンロードされた資格情報コンテナーの資格情報ファイルを参照して選択します。<br/> ![Vault credentials][4] <br/> <br/>
> [AZURE.NOTE]資格情報コンテナーの資格情報ファイルは (ポータルからダウンロード後) 48 時間のみ有効です。この画面で何らかのエラー (例: "指定されたコンテナーの資格情報ファイルは期限切れです。") が発生した場合は、Azure ポータルにログインし、資格情報コンテナーの資格情報ファイルを再度ダウンロードします。
>
> セットアップ アプリケーションがアクセスできる場所に、資格情報コンテナーの資格情報ファイルがあることを確認します。アクセス関連のエラーが発生した場合は、資格情報コンテナーの資格情報ファイルをこのコンピューターの一時的な場所にコピーし、操作をやり直してください。
>
> 資格情報コンテナーの資格情報が無効であるというエラー (例: "無効なコンテナーの資格情報が指定されました。ファイルが破損しているか、最新の資格情報が回復サービスと関連付けられていません。") が発生した場合、ポータルから新しい資格情報コンテナーの資格情報ファイルをダウンロードしてから操作をやり直してください。このエラーは通常、ユーザーが Azure ポータルで [資格情報コンテナー資格情報のダウンロード] オプションを連続でクリックした場合に発生します。この場合、2 番目の資格情報コンテナーの資格情報ファイルだけが有効です。

9. **[暗号化の設定]** 画面で、パスフレーズを生成するか、パスフレーズ (最小 16 文字) を指定し、必ず安全な場所にパスフレーズを保存してください。<br/> ![暗号化][5] <br/> <br/>
> [AZURE.WARNING]パスフレーズを紛失または忘れてしまった場合、Microsoft はバックアップ データの回復を支援することはできません。エンド ユーザーは暗号化パスフレーズを所有していますが、Microsoft はエンド ユーザーが使用するパスフレーズを確認できません。回復操作中に必要になる場合もあるため、ファイルは安全な場所に保存してください。

10. **[完了]** ボタンをクリックすると、コンピューターは資格情報コンテナーに正常に登録され、Microsoft Azure へのバックアップを開始できるようになります。Azure Backup mmc スナップインの **[プロパティの変更]** オプションをクリックすることによって、登録ワークフロー時に指定した設定を変更できます。<br/> ![Change Properties][6]

## 次のステップ
+ ワークフローに関するご質問は、Azure Backup のインストールと構成に関する [FAQ](backup-azure-backup-faq.md) をご覧ください。


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png
 

<!---HONumber=62-->