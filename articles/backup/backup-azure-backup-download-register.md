<properties
	pageTitle="エージェントのダウンロード、インストール、および Azure Backup の登録"
	description="学習方法と場所、Azure Backup エージェントをダウンロードするインストール手順と、コンテナー資格情報を使用して、Azure Backup エージェントを登録する方法"
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

# ダウンロード、インストール、および Azure Backup エージェントを登録します。

Azure バックアップ コンテナーを作成した後に各バックアップ データを Azure にアプリケーションを使用すると、内部設置型サーバー (Windows Server、Windows のクライアントまたはサーバーの System Center Data Protection Manager) で、エージェントをインストールする必要があります。この記事では、Windows Server または Windows クライアント コンピューター上の Azure Backup エージェントをセットアップするのに必要な手順について説明します。

1. サインイン、 [管理ポータル](https://manage.windowsazure.com/)

2. **[復旧サービス]** をクリックし、サーバーに登録するバックアップ資格情報コンテナーを選択します。そのバックアップ コンテナーの [クイック スタート] ページが表示されます。 <br/> ![クイック スタート][1]

3. [クイック スタート] ページで、クリックして **For Windows Server または System Center Data Protection Manager または Windows クライアント** [ **エージェントのダウンロード** オプション。クリックして保存すると、ローカル コンピューターにコピーします。 <br/> ![エージェントを保存します。][2]

4. MARSAgentInstaller.exe を Azure Backup エージェントのインストールを起動する、エージェントがインストールされたら、ダブルクリックしてください。インストール フォルダーと、エージェントに必要なスクラッチ フォルダーを選択します。指定されたキャッシュの場所は、これは、バックアップ データの 5% 以上に空き領域が必要です。

5.	インターネットに接続するプロキシ サーバーを使用するかどうか、 **プロキシ構成** 画面で、プロキシ サーバーの詳細を入力します。認証済みのプロキシを使用する場合は、この画面で、ユーザー名とパスワードの詳細を入力します。

6.	Azure Backup エージェントをインストール .NET Framework 4.5 と Windows PowerShell (既に使用可能ではありません) の場合、インストールを完了します。

7.	エージェントをインストールしたら、 **登録に進みます** 、ワークフローを続行するボタンをクリックします <br/> 。![Register][3]

8. [資格情報コンテナーの資格情報] 画面でを見つけて、以前にダウンロードされた資格情報コンテナー資格情報ファイルを選択します。 <br/> ![コンテナー資格情報][4] <br/> <br/>
> [AZURE.NOTE]コンテナーの資格情報ファイルのサイズは 48 に対してのみ有効時間 (後、ポータルからダウンロードされます)。この画面 (つまり"コンテナー資格情報ファイル provided 有効期限が切れた")、ログインに、Azure ポータルでのすべてのエラーが発生した場合は、資格情報コンテナー資格情報ファイルを再度ダウンロードします。
>
> セットアップ アプリケーションにアクセスできる場所に、コンテナーの資格情報ファイルが使用可能なことを確認します。関連するエラーへのアクセスが発生した場合、このコンピューターに一時的な場所に、コンテナー資格情報ファイルをコピーおよび操作をやり直してください。
>
> 無効な資格情報コンテナー資格情報 (例:「指定された無効な資格情報コンテナー資格情報」エラーが発生した場合。ファイルが破損しているか、またはしませんが、最新の資格情報に関連付けられていない、回復サービス"、ポータルから新しい資格情報コンテナー資格情報ファイルをダウンロードしてから操作をやり直してください。このエラーは、通常、ユーザーが立て続けに、Azure のポータルでダウンロードの資格情報コンテナー資格情報オプションをクリックした場合は、発生します。この場合は、2 番目の資格情報コンテナーの資格情報ファイルだけでは有効です。

9.  **暗号化設定** ] 画面で、可能性がありますか、パスフレーズを生成またはパスフレーズ (16 文字の最小構成) を指定し、安全な場所に、パスフレーズを保存してください、 <br/> ![暗号化][5] <br/> 。<br/>
> [AZURE.WARNING]パスフレーズが紛失または忘れた; 場合バックアップのデータを回復することはできません。エンドユーザーには、暗号化のパスフレーズを所有しているし、Microsoft には、エンドユーザーが使用されるパスフレーズには、可視性はありません。くださいとして保存します。 安全な場所に回復操作中に必要ななります。

10. クリックすると、 **完了** ボタン、マシンは、資格情報コンテナーに正常に登録されているし、Microsoft Azure へのバックアップを開始する準備ができました。クリックして、登録ワークフロー内で指定した設定を変更する、 **プロパティの変更** オプションで、Azure Backup mmc スナップイン インチ <br/> ![プロパティの変更][6]

## 次のステップ
+ インストールと、Azure Backup の構成を参照してください [よく寄せられる質問](backup-azure-backup-faq.md) ワークフローに関するご質問は。


<!--Image references-->
[1]: ./media/backup-azure-backup-download-register/quickstart.png
[2]: ./media/backup-azure-backup-download-register/agent.png
[3]: ./media/backup-azure-backup-download-register/register.png
[4]: ./media/backup-azure-backup-download-register/vc.png
[5]: ./media/backup-azure-backup-download-register/encryption.png
[6]: ./media/backup-azure-backup-download-register/change.png

<!---HONumber=GIT-SubDir--> 