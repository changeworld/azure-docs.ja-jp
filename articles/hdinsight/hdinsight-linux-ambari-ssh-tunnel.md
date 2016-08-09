<properties
pageTitle="SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする"
description="SSH トンネルを使用して、Linux ベースの HDInsight ノードでホストされている Web リソースを安全に閲覧する方法について説明します。"
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/22/2016"
ms.author="larryfr"/>

#SSH トンネリングを使用して Ambari Web UI、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする

Linux ベースの HDInsight クラスターは、インターネット経由で Ambari Web UI にアクセスできますが、UI の一部の機能にはアクセスできません。たとえば、Ambari 経由で表示されるその他のサービスの Web UI などです。Ambari Web UI の全機能を使用するには、クラスター ヘッドに対して SSH トンネルを使用する必要があります。

##SSH トンネルが必要な機能

Ambari の一部のメニューは、SSH トンネルなしでは完全に表示されません。これは、クラスターで実行されている他の Hadoop サービスが公開している Web サイトとサービスに依存しているためです。多くの場合、これらの Web サイトはセキュリティで保護されていないので、インターネットで直接公開するのは安全ではありません。場合によっては、Zookeeper ノードなど、他のクラスター ノードで Web サイトが実行されているサービスもあります。

次のサービスは Ambari Web UI が使用しているサービスですが、SSH トンネルなしではアクセスできません。

* JobHistory、
* NameNode、
* スレッド スタック、
* Oozie Web UI
* HBase Master と Logs の UI

クラスターをカスタマイズするスクリプト アクションを使用する場合、インストールするサービスまたはユーティリティで Web UI 公開するには、SSH トンネルが必要です。たとえば、スクリプト アクションを使用して Hue をインストールする場合、SSH トンネルを使用して Hue Web UI にアクセスする必要があります。

##SSH トンネルとは

[Secure Shell (SSH) トンネリング](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling)は、ローカル ワークステーション上のポートに送信されたトラフィックを、HDInsight クラスター ヘッド ノードへの SSH 接続経由でルーティングします。この要求は、ヘッド ノードから発信されたかのように解決されます。応答は、トンネル経由でワークステーションにルーティングされます。

##前提条件

Web トラフィックに SSH トンネルを使用するには、以下が必要です。

* SSH クライアント。Linux および UNIX のディストリビューション、または Macintosh OS X の場合、オペレーティング システムに `ssh`コマンドが用意されています。Windows の場合は [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) をお勧めします。

	> [AZURE.NOTE] `ssh`または PuTTY 以外の SSH クライアントを使用する場合は、クライアントのマニュアルで SSH トンネルの確立方法を確認してください。

* SOCKS プロキシを使用するように構成できる Web ブラウザー

* __(省略可能)__: [FoxyProxy](http://getfoxyproxy.org/,) などのプラグイン。FoxyProxy は、トンネル経由で特定の要求のみをルーティングするルールを適用できます。

	> [AZURE.WARNING] FoxyProxy などのプラグインを使用しない場合、ブラウザー経由で送信されたすべての要求が、トンネル経由でルーティングされる場合があります。その結果、ブラウザーの Web ページの読み込みが低速になる可能性があります。

##<a name="usessh"></a>SSH コマンドを使用してトンネルを作成する

次のように `ssh` コマンドを使用して、SSH トンネルを作成します。__USERNAME__ は、実際の HDInsight クラスターの SSH ユーザーに置き換えます。また、__CLUSTERNAME__ は、HDInsight クラスターの名前に置き換えます。

	ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

これで、ローカル ポート 9876 へのトラフィックを SSH 経由でクラスターにルーティングする接続が作成されます。オプションは次のとおりです。

* **D 9876** - トンネル経由でトラフィックをルーティングするローカル ポートです。

* **C** - すべてのデータを圧縮します (Web トラフィックの大部分はテキストであるため)。

* **2** - SSH プロトコル バージョンを強制的に 2 のみに指定します。

* **q** - Quiet モードです。

* **T** - pseudo-tty の割り当てを無効にします (ポートの転送だけを行うため)。

* **n** - STDIN を読み取らないようにします (ポートの転送だけを行うため)。

* **N** - リモート コマンドを実行しません (ポートの転送だけを行うため)。

* **f** - バックグラウンドで実行します。

SSH キーを使用してクラスターを構成した場合は、SSH 秘密キーのパスを `-i` パラメーターで指定する必要があることがあります。

コマンドが完了すると、ローカル コンピューター上でポート 9876 に送信されるトラフィックは、クラスターのヘッド ノードに Secure Sockets Layer (SSL) 経由でルーティングされ、そのヘッド ノードで生成されたかのように見えます。

##<a name="useputty"></a>PuTTY を使用してトンネルを作成する

次の手順に従ってPuTTY を使用して、SSH トンネルを作成します。

1. PuTTY を開き、接続情報を入力します。HDInsight で PuTTY を使用する方法については、「[HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)」を参照してください。

2. ダイアログの左にある **[カテゴリ]** セクションで、**[接続]**、**[SSH]** の順に展開し、**[トンネル]** を選択します。

3. **[SSH ポートの転送を管理するオプション]** フォームに次の情報を入力します。

	* **[ソース ポート]** - 転送するクライアント上のポート(**9876** など)

	* **[宛先]** - Linux ベースの HDInsight クラスターの SSH アドレス(**mycluster-ssh.azurehdinsight.net** など)

	* **[動的]** - 動的な SOCKS プロキシを有効にします。

	![トンネリング オプションの画像](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. **[追加]** をクリックして設定を追加し、**[開く]** をクリックして SSH 接続を開きます。

5. プロンプトが表示されたら、サーバーにログインします。これにより、SSH セッションが確立され、トンネルが有効になります。

##ブラウザーからトンネルを使用する

> [AZURE.NOTE] このセクションの手順では、FireFox ブラウザーを使用します。FireFox は、Linux、UNIX、Macintosh OS X、Windows の各システムで無料で使用できるためです。Google Chrome、Microsoft Edge、Apple Safari などの最近のブラウザーでも動作するはずですが、一部の手順で使用されている FoxyProxy プラグインがブラウザーによっては動作しない可能性があります。

1. **SOCKS v5** プロキシとして **localhost:9876** を使用するようにブラウザーを構成します。Firefox の設定は次のようになります。9876 以外のポートを使用する場合は、そのポート番号に変更します。

	![Firefox の設定の画像](./media/hdinsight-linux-ambari-ssh-tunnel/socks.png)

	> [AZURE.NOTE] **[リモート DNS]** を選択するとドメイン ネーム システム (DNS) 要求は HDInsight クラスターを使用して解決されます。選択しないと、DNS はローカルに解決されます。

2. Firefox でプロキシ設定を有効または無効にして、[http://www.whatismyip.com/](http://www.whatismyip.com/) などのサイトにアクセスして、トラフィックがトンネルを経由しているかどうかを確認します。設定が有効の場合、IP アドレスは Microsoft Azure データセンター内のコンピューターの IP アドレスになります。

###ブラウザー拡張

ブラウザーをトンネル ワークを使用するように構成する場合、通常、一部のトラフィックをトンネルを経由で送信しないように設定する必要があることがあります。[FoxyProxy](http://getfoxyproxy.org/) などのブラウザー拡張機能では、URL 要求に対するパターン マッチングがサポートされるため (FoxyProxy Standard または Plus のみ)、特定の URL の要求のみがトンネル経由で送信されます。

FoxyProxy Standard をインストール済みの場合は、次の手順を使用して、HDInsight の トラフィックだけをトンネル経由で転送するように構成します。

1. ブラウザーで FoxyProxy 拡張を開きます。Firefox などで、アドレス フィールドの横にある FoxyProxy アイコンを選択します。

	![foxyproxy のアイコン](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxy.png)

2. **[新しいプロキシの追加]**、**[全般]** タブの順に選択し、**HDInsightProxy** のプロキシ名を入力します。

	![foxyproxy の全般](./media/hdinsight-linux-ambari-ssh-tunnel/foxygeneral.png)

3. **[プロキシの詳細]** タブを選択し、次のフィールドを入力します。

	* **[ホストまたは IP アドレス]** - ローカル マシンで SSH トンネルを使用するため「localhost」と入力します。

	* **[ポート]** - SSH トンネル用に使用するポート。

	* **[SOCKS プロキシ]** - ブラウザーでのプロキシとしてのトンネルの使用を有効にします。

	* **[SOCKS v5]** - プロキシに必要なバージョンを設定します。

	![foxyproxy のプロキシ](./media/hdinsight-linux-ambari-ssh-tunnel/foxyproxyproxy.png)

4. **[URL パターン]** タブを選択し、**[新しいパターンの追加]** を選択します。以下を使用してパターンを定義し、**[OK]** をクリックします。

	* **[パターン名]** - **clusternodes** - パターンのフレンドリ名です。

	* **URL パターン** - ***internal.cloudapp.net*** -クラスター ノードの内部の完全修飾ドメイン名と一致するパターンを定義します。

	![foxyproxy のパターン](./media/hdinsight-linux-ambari-ssh-tunnel/foxypattern.png)

    設定に次の情報を使用して、もう 1 つのパターンを追加します。

    * __[パターン名]__ - headnode
    * __[URL パターン]__ - *headnodehost*

    [OK] を選択してこのパターンを保存します。

4. **[OK]** をクリックしてプロキシを追加し、**[プロキシの設定]** を閉じます。

5. FoxyProxy ダイアログの上部で、**[モードの選択]** を **[定義済みのパターンと優先順位に基づいてプロキシを使用]** に変更し、**[閉じる]** を選択します。

	![foxyproxy の選択モード](./media/hdinsight-linux-ambari-ssh-tunnel/selectmode.png)

これらを完了すると、__internal.cloudapp.net__ という文字列を含む URL の要求のみが SSL トンネル経由で送信されます。

##Ambari Web UI を使用して確認する

クラスターが確立したら、次の手順で、Ambari Web からサービス Web UI にアクセスできることを確認します。

1. ブラウザーで http://headnodehost:8080 に移動します。この `headnodehost` アドレスはトンネル経由でクラスターに送信され、Ambari が実行されているヘッド ノードに解決されます。プロンプトが表示されたら、クラスターの管理者ユーザー名 (admin) とパスワードを入力します。Ambari Web UI からもう一度入力を求められることがあります。その場合は、情報を再入力します。
    
    > [AZURE.NOTE] http://headnodehost:8080 アドレスを使用してクラスターに接続すると、HTTP を使用して、Ambari が実行されているヘッド ノードにトンネル経由で直接接続され、通信は SSH トンネルを使用して保護されます。トンネルを使用せずにインターネット経由で接続すると、通信は HTTPS を使用して保護されます。HTTPS を使用してインターネット経由で接続するには、https://CLUSTERNAME.azurehdinsight.net を使用します。__CLUSTERNAME__ はクラスターの名前です。

2. Ambari Web UI でページの左側にある一覧から [HDFS] を選択します。

	![HDFS が選択された画像](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. HDFS サービスの情報が表示されたら、__[Quick Links]__ を選択します。クラスターのヘッド ノードの一覧が表示されます。ヘッド ノードのいずれかを選択し、__[NameNode UI]__ を選択します。

	![QuickLinks メニューが展開された画像](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

	> [AZURE.NOTE] インターネット接続が低速な場合、またはヘッド ノードの負荷が高い場合は、__[Quick Links]__ を選択したときにメニューではなく待機インジケーターが表示されることがあります。その場合は、サーバーからデータが取得されるまで 1 ～ 2 分待ってから、改めて一覧を表示してみてください。
    >
	> モニターの解像度が低い場合、またはブラウザー ウィンドウが最大化されていない場合は、__[Quick Links]__ メニューの一部のエントリが画面の右側で切れて表示されることがあります。その場合は、マウスを使用してメニューを拡大するか、右矢印キーを使用して画面を右にスクロールして、メニューの残りを表示します。

4. 次のようなページが表示されます。

	![NameNode UI の画像](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

	> [AZURE.NOTE] このページの URL は、\_\_http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster__ のようになります。この URL は、ノードの内部の完全修飾ドメイン名 (FQDN) を使用しているので、アクセスするには SSH トンネルを使用する必要があります。

##次のステップ

ここでは、SSH トンネルを作成し、使用する方法について説明しました。Ambari を使用してクラスターを監視および管理する方法については、次を参照してください。

* [Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)

HDInsight での SSH の使用方法の詳細については、以下の記事を参照してください。

* [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

* [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

<!---HONumber=AcomDC_0727_2016-->