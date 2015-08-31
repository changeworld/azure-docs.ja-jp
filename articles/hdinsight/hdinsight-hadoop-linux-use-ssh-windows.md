<properties
   pageTitle="Windows と Linux ベースのクラスターの Hadoop の間で SSH キーを使用する | Microsoft Azure"
   description="SSH キーを作成して、Linux ベースの HDInsight クラスターに対する認証に使用する方法について説明します。PuTTY SSH クライアントを使用して Windows ベースのクライアントからクラスターに接続します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/24/2015"
   ms.author="larryfr"/>

#HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する (プレビュー)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
- [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

Linux ベースの Azure HDInsight クラスターには、パスワードまたは SSH キーを使用した Secure Shell (SSH) アクセスのオプションが用意されています。このドキュメントでは、PuTTY SSH クライアントを使用した Windows ベースのクライアントからの HDInsight への接続について説明します。

> [AZURE.NOTE]この記事の手順では、Windows ベースのクライアントを使用していることを前提としています。Linux、Unix、OS X クライアントを使用する場合は、[「Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する」](hdinsight-hadoop-linux-use-ssh-unix.md)をご覧ください。

##前提条件

* **PuTTY** と **PuTTYGen** (Windows クライアント)。これらのユーティリティは [http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) から入手できます。

* HTML5 をサポートする最新の Web ブラウザー

または

* [Mac、Linux、Windows 用の Azure CLI](../xplat-cli.md)

##SSH とは

SSH は、リモート サーバーにログインしたり、リモート サーバーでコマンドをリモート実行したりするためのユーティリティです。Linux ベースの HDInsight では、SSH によりクラスター ヘッド ノードへの暗号化された接続が確立され、コマンドの入力に使用するコマンド ラインが提供されます。コマンドは、直接サーバーで実行されます。

##SSH キーの作成 (オプション)

Linux ベースの HDInsight クラスターを作成するとき、SSH キーを使用したサーバーへの認証にパスワードまたは SSH キーを使用することを選択できます。証明書ベースであるため、SSH キーの方がより安全です。クラスターで SSH キーを使用する場合は、次の手順を実行します。

1. PuTTYGen を開きます。

2. **[生成するキーの種類]** に **[SSH-2 RSA]** を選択し、**[生成]** をクリックします。

	![PuTTYGen インターフェイス](./media/hdinsight-hadoop-linux-use-ssh-windows/puttygen.png)

3. 進行状況バーが塗りつぶされるまで、バーの下の領域にマウスを移動します。マウスを移動すると、キーの生成に使用するランダム データが生成されます。

	![マウスの移動](./media/hdinsight-hadoop-linux-use-ssh-windows/movingmouse.png)

	キーが生成されると、パブリック キーが表示されます。

4. セキュリティを強化するために、**[キーのパスフレーズ]** フィールドにパスフレーズを入力し、**[パスフレーズの確認]** フィールドに同じ値を入力します。

	![パスフレーズ](./media/hdinsight-hadoop-linux-use-ssh-windows/key.png)

	> [AZURE.NOTE]キーにセキュリティで保護されたパスフレーズを使用することを強くお勧めします。ただし、パスフレーズを忘れた場合、それを回復する方法はありません。

5. **[プライベート キーの保存]** をクリックして、キーを **.ppk** ファイルに保存します。このキーは、Linux ベースの HDInsight クラスターへの認証に使用されます。

	> [AZURE.NOTE]こキーは Linux ベースの HDInsight クラスターへのアクセスに使用できるため、セキュリティ保護された場所に保存する必要があります。

6. **[パブリック キーの保存]** をクリックして、キーを **.txt** ファイルとして保存します。これにより、Linux ベースの HDInsight クラスターを今後追加で作成するときにパブリック キーを再利用できます。

	> [AZURE.NOTE]また、パブリック キーは PuTTYGen の上部に表示されます。Azure プレビュー ポータルを使用してクラスターを作成するときに、このフィールドを右クリックし、値をコピーして、フォームに貼り付けることができます。

##Linux ベースの HDInsight クラスターの作成

Linux ベースの HDInsight クラスターを作成するときには、以前に作成した公開キーを指定する必要があります。Windows ベースのクライアントから、次の 2 つの方法で、Linux ベースの HDInsight クラスターを作成できます。

* **Azure プレビュー ポータル** - Web ベースのポータルを使用してクラスターを作成します。

* **Mac、Linux、Windows 用の Azure CLI** - コマンドラインでコマンドを使用してクラスターを作成します。

これらの各メソッドにはパブリック キーが必要です。Linux ベースの HDInsight クラスターを作成する方法の詳細については、[HDInsight での Hadoop Linux クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

###Azure プレビュー ポータル

[Azure プレビュー ポータル][preview-portal]を使用して Linux ベースの HDInsight クラスターを作成する場合は、**[SSH ユーザー名]** を入力し、**[パスワード]** と **[SSH 公開キー]** のどちらを入力するかを選択する必要があります。

**[SSH 公開キー]** を選択した場合は、公開キー(PuttyGen の __OpenSSH authorized\_keys ファイル__ フィールドに貼り付けるための公開キーに表示されている) を [__SSH 公開キー]__ フィールドに貼り付けるか、__[ファイルの選択]__ を選択し、公開キーを含むファイルを参照して選択できます。

![公開キーの入力を求めるフォームの画像](./media/hdinsight-hadoop-linux-use-ssh-windows/ssh-key.png)

これにより、指定したユーザーのログインが作成され、パスワード認証または SSH キー認証のいずれかが有効になります。

###Mac、Linux、Windows の Azure コマンド ライン インターフェイス

[Mac、Linux、Windows の Azure CLI](../xplat-cli.md) では、`azure hdinsight cluster create` コマンドを使用して新しいクラスターを作成できます。

このコマンドの使用方法の詳細については、「[カスタム オプションを使用した HDInsight での Hadoop Linux クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

##Linux ベースの HDInsight クラスターへの接続

1. PuTTY を開きます。

	![putty インターフェイス](./media/hdinsight-hadoop-linux-use-ssh-windows/putty.png)

2. ユーザー アカウントの作成時に SSH キーを指定した場合は、次の手順に従って、クラスターへの認証に使用するプライベート キーを選択する必要があります。

	**[カテゴリ]** で **[接続]**、**[SSH]** の順に展開し、**[認証]** を選択します。最後に、**[参照]** をクリックし、プライベート キーが含まれた .ppk ファイルを選択します。

	![putty インターフェイス、秘密キーの選択](./media/hdinsight-hadoop-linux-use-ssh-windows/puttykey.png)

3. **[カテゴリ]** で **[セッション]** を選択します。**[PuTTY セッションの基本設定]** 画面で、**[ホスト名 (または IP アドレス)]** フィールドに HDInsight サーバーの SSH アドレスを入力します。SSH アドレスは、クラスター名に続けて「**-ssh.azurehdinsight.net**」と入力します (**mycluster-ssh.azurehdinsight.net** など)。

	![ssh アドレスを入力した putty インターフェイス](./media/hdinsight-hadoop-linux-use-ssh-windows/puttyaddress.png)

4. 後で使用するために接続情報を保存するには、**[保存されたセッション]** にこの接続の名前を入力し、**[保存]** をクリックします。接続が、保存済みセッションの一覧に追加されます。

5. **[開く]** をクリックして、クラスターに接続します。

	> [AZURE.NOTE]クラスターに初めて接続する場合は、セキュリティ の警告が返されます。問題はありません。**[はい]** を選択し、サーバーの RSA2 キーをキャッシュして続行します。

6. プロンプトが表示されたら、クラスターの作成時に入力したユーザーを入力します。ユーザーにパスワードを指定している場合は、その入力も求められます。

> [AZURE.NOTE]上記の手順ではポート 22 を使用していると仮定しています。これは、HDInsight クラスターの headnode0 に接続します。ポート 23 を使用した場合は、headnode1 に接続します。ヘッド ノードの詳細については、「[HDInsight における Hadoop クラスターの可用性と信頼性](hdinsight-high-availability-linux.md)」を参照してください。

###ワーカー ノードへの接続

ワーカー ノードは、Azure データセンターの外部からは直接アクセスできませんが、SSH を使用してクラスター ヘッド ノードからアクセスできます。

ユーザー アカウントの作成時に SSH キーを指定した場合は、ワーカー ノードに接続するクラスターを認証するときに、次の手順に従ってプライベート キーを使用する必要があります。

1. [http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) から Pageant をインストールします。このユーティリティを使用して、PuTTY の SSH キーをキャッシュします。

2. Pageant を実行します。ステータス トレイにアイコンが表示されます。アイコンを右クリックし **[キーの追加]** を選択します。

    ![キーの追加](./media/hdinsight-hadoop-linux-use-ssh-windows/addkey.png)

3. [参照] ダイアログ ボックスが表示されたら、キーを含む .ppk ファイルを選択し、**[開く]** をクリックします。キーは Pageant に追加され、クラスターに接続すると PuTTY に渡されます。

    > [AZURE.IMPORTANT]SSH キーを使用してアカウントのセキュリティを保護する場合は、前の手順を完了してからワーカー ノードに接続する必要があります。

4. PuTTY を開きます。

5. SSH キーを使用して認証する場合は、**[カテゴリ]** セクションで **[接続]**、**[SSH]** の順に展開し、**[認証]** を選択します。

    **[認証パラメーター]** セクションで、**[Allow agent forwarding]** を有効にします。これにより、PuTTY はワーカー ノードに接続するときに、自動的にクラスター ヘッド ノードに接続して証明書認証を渡すことができます。

    ![エージェント転送の許可](./media/hdinsight-hadoop-linux-use-ssh-windows/allowforwarding.png)

6. 上記の説明と同様に、クラスターに接続します。認証に SSH キーを使用する場合は、キーを選択する必要はありません。Pageant に追加された SSH キーがクラスターの認証に使用されます。

7. 接続が確立されたら、次のコマンドを使用してクラスター内のノードの一覧を取得します。*ADMINPASSWORD* をクラスターの管理者アカウントのパスワードに置き換えます。*CLUSTERNAME* をクラスターの名前に置き換えます。

        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts

    クラスター内のノードの `host_name` などの情報が JSON 形式で返されます。これには、各ノードの完全修飾ドメイン名 (FQDN) が含まれています。次は、**curl** コマンドによって返された `host_name` エントリの例です。

        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"

8. 接続するワーカー ノードの一覧を取得したら、PuTTY セッションから次のコマンドを使用して、ワーカー ノードへの接続を開きます。

        ssh USERNAME@FQDN

    *USERNAME* を SSH ユーザー名、*FQDN* をワーカー ノードの FQDN に置き換えます (例: `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`)。

    > [AZURE.NOTE]SSH セッションの認証にパスワードを使用する場合は、もう一度パスワードを入力するように求めるメッセージ表示されます。SSH キーを使用する場合は、何も表示されずに接続が完了します。

9. セッションが確立されると、PuTTY セッションのプロンプトが `username@headnode` から `username@workernode` に変わり、ワーカー ノードに接続したことを示します。この時点で実行するすべてのコマンドは、ワーカー ノードで実行されます。

10. ワーカー ノードでの操作が終了したら、`exit` コマンドを使用してワーカー ノードのセッションを閉じます。これにより、`username@headnode` プロンプトが表示されます。

##複数のアカウントの追加

クラスターに複数のアカウントを追加する必要がある場合は、次の手順を実行します。

1. 前述の説明に従って、新しいユーザー アカウントの新しいパブリック キーとプライベート キーを生成します。

2. クラスターへの SSH セッションから、次のコマンドを使用して新しいユーザーを追加します。

		sudo adduser --disabled-password <username>

	これにより、新しいユーザー アカウントが作成されますが、パスワード認証は無効になります。

3. 次のコマンドを使用して、キーを格納するディレクトリとファイルを作成します。

        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys

4. ナノ エディターが開いたら、新しいユーザー アカウントのパブリック キーの内容をコピーして貼り付けます。最後に、**Ctrl + X** キーを使用してファイルを保存し、エディターを終了します。

	![サンプル キーとナノ エディターの画像](./media/hdinsight-hadoop-linux-use-ssh-windows/nano.png)

5. 次のコマンドを使用して、新しいユーザー アカウントに対する .ssh フォルダーの所有権をとコンテンツを変更します。

		sudo chown -hR <username>:<username> /home/<username>/.ssh

6. これで、新しいユーザー アカウントおよびプライベート キーを使用してサーバーへの認証を行えるようになります。

##<a id="tunnel"></a>SSH トンネリング

SSH を使用して、Web 要求などのローカルの要求を HDInsight クラスターにトンネリングできます。ここでは、最初から HDInsight クラスター ヘッド ノード上にあったかのように、要求が要求済みリソースにルーティングされます。

> [AZURE.IMPORTANT]SSH トンネルは、sopme Hadoop サービス用の Web UI にアクセスするための要件です。たとえば、ジョブ履歴 UI とリソース マネージャー UI は、両方とも SSH トンネルでのみアクセスできます。

SSH トンネルを作成し、ブラウザーでこれを使用してクラスターに接続するように構成するには、次の手順を実行します。

1. PuTTY を開き、前の[Linux ベースの HDInsight クラスターへの接続](#connect-to-a-linux-based-hdinsight-cluster)セクションで説明したように接続情報を入力します。

2. ダイアログの左にある **[カテゴリ]** セクションで、**[接続]**、**[SSH]** の順に展開し、**[トンネル]** を選択します。

3. **[SSH ポートの転送を管理するオプション]** フォームに次の情報を入力します。

	* **[ソース ポート]** - 転送するクライアント上のポート(**9876** など)

	* **[宛先]** - Linux ベースの HDInsight クラスターの SSH アドレス(**mycluster-ssh.azurehdinsight.net** など)

	* **[動的]** - 動的な SOCKS プロキシを有効にします。

	![トンネリング オプションの画像](./media/hdinsight-hadoop-linux-use-ssh-windows/puttytunnel.png)

4. **[追加]** をクリックして設定を追加し、**[開く]** をクリックして SSH 接続を開きます。

5. プロンプトが表示されたら、サーバーにログインします。これにより、SSH セッションが確立され、トンネルが有効になります。

6. Firefox などのクライアント プログラムで **SOCKS v5** プロキシとして **localhost:9876** を使用するように構成します。Firefox の設定は次のようになります。

	![Firefox の設定の画像](./media/hdinsight-hadoop-linux-use-ssh-windows/socks.png)

	> [AZURE.NOTE]**[リモート DNS]** を選択すると DNS 要求は HDInsight クラスターを使用して解決されます。選択しないと、DNS はローカルに解決されます。

	Firefox でプロキシ設定を有効または無効にして、[http://www.whatismyip.com/](http://www.whatismyip.com/) などのサイトにアクセスして、トラフィックがトンネルを経由しているかどうか確認できます。設定が有効の場合、IP アドレスは Microsoft Azure データセンター内のコンピューターの IP アドレスになります。

###ブラウザー拡張

ブラウザーをトンネル ワークを使用するように構成する場合、通常、一部のトラフィックをトンネルを経由で送信しないように設定する必要があることがあります。[FoxyProxy](http://getfoxyproxy.org/) などのブラウザー拡張機能では、URL 要求に対するパターン マッチングがサポートされるため (FoxyProxy Standard または Plus のみ)、特定の URL の要求のみがトンネル経由で送信されます。

FoxyProxy Standard をインストール済みの場合は、次の手順を使用して、HDInsight の トラフィックだけをトンネル経由で転送するように構成します。

1. ブラウザーで FoxyProxy 拡張を開きます。Firefox などで、アドレス フィールドの横にある FoxyProxy アイコンを選択します。

	![foxyproxy のアイコン](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxy.png)

2. **[新しいプロキシの追加]**、**[全般]** タブの順に選択し、**HDInsightProxy** のプロキシ名を入力します。

	![foxyproxy の全般](./media/hdinsight-hadoop-linux-use-ssh-windows/foxygeneral.png)

3. **[プロキシの詳細]** タブを選択し、次のフィールドを入力します。

	* **[ホストまたは IP アドレス]** - ローカル マシンで SSH トンネルを使用するため「localhost」と入力します。

	* **[ポート]** - SSH トンネル用に使用するポート。

	* **[SOCKS プロキシ]** - ブラウザーでのプロキシとしてのトンネルの使用を有効にします。

	* **[SOCKS v5]** - プロキシに必要なバージョンを設定します。

	![foxyproxy のプロキシ](./media/hdinsight-hadoop-linux-use-ssh-windows/foxyproxyproxy.png)

4. **[URL パターン]** タブを選択し、**[新しいパターンの追加]** を選択します。以下を使用してパターンを定義し、**[OK]** をクリックします。

	* **[パターン名]** - **headnode** - パターンのフレンドリ名です。

	* **[URL パターン]** - ***headnode*** - **headnode** という単語を含む URL と一致するパターンを定義します。

	![foxyproxy のパターン](./media/hdinsight-hadoop-linux-use-ssh-windows/foxypattern.png)

4. **[OK]** をクリックしてプロキシを追加し、**[プロキシの設定]** を閉じます。

5. FoxyProxy ダイアログの上部で、**[モードの選択]** を **[定義済みのパターンと優先順位に基づいてプロキシを使用]** に変更し、**[閉じる]** を選択します。

	![foxyproxy の選択モード](./media/hdinsight-hadoop-linux-use-ssh-windows/selectmode.png)

これらを完了すると、**headnode** という文字列を含む URL の要求のみが SSH トンネル経由で送信されます。

##次のステップ

これで、SSH キーを使用して認証する方法、また、HDInsight 上の Hadoop で MapReduce を使用する方法についてご理解いただけたと思います。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)

* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)

* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/

<!---HONumber=August15_HO8-->