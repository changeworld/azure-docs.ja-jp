<properties linkid="manage-services-recovery-configure-backup-vault" urlDisplayName="Configure a Backup Vault" pageTitle="Configure Azure Recovery Services to quickly and easily back-up Windows Server" metaKeywords="disaster recovery" description="Use this tutorial to learn how to use the Backup service in Microsoft's Azure cloud offering to back up Windows Server to the cloud." metaCanonical="" services="recovery-services" documentationCenter="" title="Configure Azure Backup to quickly and easily back-up Windows Server" authors="raynew" solutions="" manager="johndaw" editor="tysonn" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# <span id="configure-a-backup-vault-tutorial"></span></a>Windows Server のバックアップをすばやく簡単に行うための Azure のバックアップの構成

<div class="dev-callout"> 
<strong>注</strong>
 
<p>このチュートリアルを完了するには、Azure アカウントが必要です。このチュートリアルでは、Azure のバックアップ機能を有効にする手順について説明します。以前は、バックアップ サーバーを登録するために、X.509 v3 証明書の作成や取得が必要でした。証明書は現在でもサポートされていますが、サーバーへの Azure 資格情報コンテナーの登録を容易にするために、[クイック スタート] ページから資格情報コンテナーの資格情報を生成できます。 </p>
<ul> 
<li>アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="/ja-jp/pricing/free-trial/">Azure の無料評価版サイト</a>を参照してください。</li> 
 

</ul>
 

</div>

Azure に Windows Server のファイルとデータをバックアップするには、データを保存するリージョンにバックアップ資格情報コンテナーを作成する必要があります。このチュートリアルでは、バックアップの保存に使用する資格情報コンテナーの作成、資格情報コンテナーの資格情報のダウンロード、バックアップ エージェントのインストール、および管理ポータルで使用できるバックアップ管理タスクの概要について説明します。

## <span id="create"></span></a>バックアップ コンテナーの作成

1.  [管理ポータル][管理ポータル]にサインインします。

2.  **[新規]** をクリックして **[データ サービス]**、**[復旧サービス]** の順にポイントし、**[バックアップ資格情報コンテナー]**、**[簡易作成]** の順にクリックします。

3.  **[名前]** ボックスに、バックアップ資格情報コンテナーを識別する表示名を入力します。

4.  **[リージョン]** ボックスで、バックアップ資格情報コンテナーのリージョンを選択します。
    ![新しいバックアップ コンテナー][新しいバックアップ コンテナー]

5.  **[資格情報コンテナーの作成]** をクリックします。

    バックアップ資格情報コンテナーが作成されるまで時間がかかることがあります。状態を確認するには、ポータルの下部にある通知を監視します。バックアップ資格情報コンテナーが作成されたら、資格情報コンテナーが正常に作成されたことを示すメッセージが表示され、復旧サービスのリソースに **[アクティブ]** として表示されます。
    ![バックアップ資格情報コンテナーの作成][バックアップ資格情報コンテナーの作成]

6.  組織アカウントに関連付けたサブスクリプションが複数ある場合は、バックアップ資格情報コンテナーに関連付けた正しいアカウントを選択してください。

## <span id="upload"></span></a>コンテナーの資格情報のダウンロード

資格情報コンテナーの資格情報は、サーバーに Azure サービスを登録する方法として証明書に置き換わります。証明書も使用できますが、資格情報コンテナーの資格情報は、ユーザーが Azure ポータルを使用して資格情報コンテナーの資格情報を生成してダウンロードできるため、より使いやすくなっています。

1.  [管理ポータル][管理ポータル]にサインインします。

2.  **[復旧サービス]** をクリックし、サーバーに登録するバックアップ資格情報コンテナーを選択します。バックアップ資格情報コンテナーの [クイック スタート] ページが表示されます。

3.  [クイック スタート] ページで **[資格情報コンテナーのダウンロード]** をクリックすると、サーバーをバックアップ資格情報コンテナーに登録するために使用する資格情報コンテナーを生成してダウンロードするように求めるメッセージがポータルに表示されます。

4.  ポータルは、資格情報コンテナーの名前と現在の日付の組み合わせを使用して、資格情報コンテナーの資格情報を生成します。**[保存]** をクリックして、資格情報コンテナーの資格情報をローカル アカウントのダウンロード フォルダにダウンロードするか、**[保存]** メニューから **[名前を付けて保存]** を選択して、保存場所を指定します。資格情報コンテナーの資格情報は編集できないため、[開く] をクリックする必要はありません。資格情報のダウンロードが完了すると、フォルダを開くように求めるメッセージが表示されます。[**x**] をクリックしてこのメニューを閉じます。

## <span id="download"></span></a>バックアップ エージェントのダウンロードとインストール

1.  [管理ポータル][管理ポータル]で。

2.  **[復旧サービス]** をクリックし、バックアップ資格情報コンテナーを選択して [クイック スタート] ページを表示します。

3.  [クイック スタート] ページで、ダウンロードするエージェントの種類を選択します。**[Azure Backup エージェントのダウンロード]**、**[Windows Server と System Center Data Protection Manager]**、または **[Windows Server Essentials]** を選択できます。詳細情報

    -   [Windows Server 2012 および System Center 2012 SP1 - Data Protection Manager の Azure のバックアップ エージェントのインストール][Windows Server 2012 および System Center 2012 SP1 - Data Protection Manager の Azure のバックアップ エージェントのインストール]
    -   [Windows Server 2012 Essentials の Azure のバックアップ エージェントのインストール][Windows Server 2012 Essentials の Azure のバックアップ エージェントのインストール]

エージェントがインストールされたら、適切なローカル管理インターフェイス (Microsoft 管理コンソール スナップイン、System Center Data Protection Manager コンソール、Windows Server Essentials ダッシュボードなど) を使用して、サーバーのバックアップ ポリシーを構成できます。

## <span id="manage"></span></a>バックアップ資格情報コンテナーとサーバーの管理

1.  [管理ポータル][管理ポータル]にサインインします。

2.  **[復旧サービス]** をクリックし、バックアップ資格情報コンテナーの名前をクリックして、[クイック スタート] ページを表示します。

3.  **[ダッシュボード]** をクリックしてサーバーの使用状況の概要を表示します。[ダッシュボード] の下部で、次のタスクを実行できます。

    -   **証明書の管理**。証明書を使用してサーバーを登録した場合は、これを使用して証明書を更新します。資格情報コンテナーの資格情報を使用している場合は、**[証明書の管理]** を使用しないでください。
    -   **削除**。現在のバックアップ コンテナーを削除します。バックアップ コンテナーがもう使用されていない場合は、そのコンテナーを削除してストレージ領域を解放できます。**[削除]** は、登録されているサーバーすべてが資格情報コンテナーから削除された場合にのみ有効になります。
    -   **資格情報コンテナーの資格情報**。[概要] メニュー項目を使用して資格情報コンテナーの資格情報を構成します。

4.  **[保護された項目]** をクリックして、サーバーからバックアップされた項目を表示します。この一覧は、情報の提供のみを目的としています。
    
	![保護されている項目][保護されている項目]

5.  **[サーバー]** をクリックして、この資格情報コンテナーに登録されたサーバーの名前を表示します。ここでは、次のタスクを実行できます。

    -   **再登録を許可**。サーバーに対してこのオプションが選択されている場合、エージェントで登録ウィザードを使用して、サーバーをバックアップ コンテナーにもう一度登録できます。証明書でエラーが発生したとき、またはサーバーを再構築する必要があった場合などに、再登録が必要になる可能性があります。再登録を行えるのは、サーバー名ごとに 1 回だけです。
    -   **削除**。バックアップ コンテナーからサーバーを削除します。サーバーに関連付けられている保存されたデータすべてがすぐに削除されます。

        ![削除されたサーバー][削除されたサーバー]

## <span id="next"></span></a>次のステップ

-   Azure のバックアップの詳細については、「[Azure のバックアップの概要][Azure のバックアップの概要]」を参照してください。

-   [Azure のバックアップ フォーラム][Azure のバックアップ フォーラム]にアクセスします。

  [Azure の無料評価版サイト]: /ja-jp/pricing/free-trial/
  [管理ポータル]: https://manage.windowsazure.com
  [新しいバックアップ コンテナー]: http://i.imgur.com/506c7ch.png
  [バックアップ資格情報コンテナーの作成]: http://i.imgur.com/grtLcKM.png
  [Windows Server 2012 および System Center 2012 SP1 - Data Protection Manager の Azure のバックアップ エージェントのインストール]: http://technet.microsoft.com/ja-jp/library/hh831761.aspx#BKMK_installagent
  [Windows Server 2012 Essentials の Azure のバックアップ エージェントのインストール]: http://technet.microsoft.com/ja-jp/library/jj884318.aspx
  [保護されている項目]: ./media/backup-configure-vault/RS_protecteditems.png
  [削除されたサーバー]: ./media/backup-configure-vault/RS_deletedserver.png
  [Azure のバックアップの概要]: http://go.microsoft.com/fwlink/p/?LinkId=222425
  [Azure のバックアップ フォーラム]: http://go.microsoft.com/fwlink/p/?LinkId=290933
