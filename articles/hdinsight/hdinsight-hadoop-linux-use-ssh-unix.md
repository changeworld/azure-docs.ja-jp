---
title: "Linux、Unix、または OS X から Linux ベースの Hadoop で SSH キーを使用する | Microsoft Docs"
description: " Secure Shell (SSH) を使用して Linux ベースの HDInsight にアクセスできます。 このドキュメントでは、Linux、Unix、または OS X の各クライアントから HDInsight で SSH を使用する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 476d9ce8b64f3442031310bd9170c682a9940b2b


---
# <a name="use-ssh-with-linuxbased-hadoop-on-hdinsight-from-linux-unix-or-os-x"></a>Linux、Unix、または OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する
> [!div class="op_single_selector"]
> * [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [Linux、Unix、OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
> 
> 

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) では、コマンドライン インターフェイスを利用し、Linux ベース HDInsight クラスターで操作をリモート実行できます。 このドキュメントでは、Linux、Unix、または OS X の各クライアントから HDInsight で SSH を使用する方法について説明します。

> [!NOTE]
> この記事で説明する手順は、Linux、Unix、または OS X の各クライアントを使用していることを前提とします。 [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about) など、`ssh` と `ssh-keygen` を提供するパッケージがインストールされている場合は、これらの手順を Windows ベースのクライアントで実行できます。
> 
> SSH が Windows ベースのクライアントにインストールされていない場合は、PuTTYのインストールと使用に関する情報が記載された、 [HDInsight (Hadoop) で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md) 方法に関するページの手順に従ってください。
> 
> 

## <a name="prerequisites"></a>前提条件
* Linux、Unix、OS X の各クライアント用の **ssh-keygen** と **ssh**。 通常、このユーティリティはオペレーティング システムに付属しています。また、パッケージ管理システムから入手することもできます。
* HTML5 をサポートする最新の Web ブラウザー

または

* [Azure CLI](../xplat-cli-install.md)。
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

## <a name="what-is-ssh"></a>SSH とは
SSH は、リモート サーバーにログインしたり、リモート サーバーでコマンドをリモート実行したりするためのユーティリティです。 Linux ベースの HDInsight では、SSH によりクラスター ヘッド ノードへの暗号化された接続が確立され、コマンドの入力に使用するコマンド ラインが提供されます。 コマンドは、直接サーバーで実行されます。

### <a name="ssh-user-name"></a>SSH ユーザー名
SSH ユーザー名は HDInsight クラスターの認証に使用する名前です。 クラスター作成中に SSH ユーザー名を指定するとき、このユーザーがクラスターのすべてのノードで作成されます。 クラスターを作成したら、このユーザー名を使用し、HDInsight クラスター ヘッド ノードに接続できます。 ヘッド ノードから、個々のワーカー ノードに接続できます。

### <a name="ssh-password-or-public-key"></a>SSH パスワードまたは公開キー
SSH ユーザーはパスワードと公開キーのいずれかを認証に利用できます。 パスワードはユーザーが考えたテキスト文字列です。公開キーはユーザーを一意に識別するために生成された暗号化キー ペアの片割れです。

キーはパスワードより安全ですが、追加の手順でキーを生成する必要があり、キーが入っているファイルを安全な場所に保管しなければなりません。 そのキー ファイルにアクセスされると、アカウントにもアクセスされます。 また、キー ファイルをなくした場合、アカウントにログインできなくなります。

キー ペアは公開キー (HDInsight サーバーに送信されます) と秘密キー (クライアント コンピューターで保存されます) から構成されます。SSH を利用して HDInsight に接続すると、SSH はコンピューターに保存されている秘密キーを利用し、サーバーで認証します。

## <a name="create-an-ssh-key"></a>SSH キーの作成
クラスターで SSH キーを使用する場合は、次の手順を実行します。 パスワードを使用する場合、このセクションを省略できます。

1. ターミナル セッションを開き、次のコマンドを使用して、既存の SSH キーがあるかどうかを確認します。
   
        ls -al ~/.ssh
   
    ディレクトリ一覧で、次のファイルを探します。 これらは SSH 公開キーの共通名です。
   
   * id\_dsa.pub
   * id\_ecdsa.pub
   * id\_ed25519.pub
   * id\_rsa.pub
2. 既存のファイルを使用しない場合や、既存の SSH キーがない場合は、次のコマンドを使用して新しいファイルを生成します。
   
        ssh-keygen -t rsa
   
    次の情報の入力を求められます。
   
   * ファイルの場所 - 場所は既定で ~/.ssh/id\_rsa に設定されます。
   * パスフレーズ - 1 回入力した後に再入力を求められます。
     
     > [!NOTE]
     > キーにセキュリティで保護されたパスフレーズを使用することを強くお勧めします。 ただし、パスフレーズを忘れた場合、それを回復する方法はありません。
     > 
     > 
     
     コマンドが完了すると、秘密キー (例: **id\_rsa**) と公開キー (例: **id\_rsa.pub**) の 2 つの新しいファイルが作成されます。

## <a name="create-a-linuxbased-hdinsight-cluster"></a>Linux ベースの HDInsight クラスターの作成
Linux ベースの HDInsight クラスターを作成するときには、以前に作成した公開キーを指定する必要があります。 Linux、Unix、または OS X の各クライアントからは、2 つの方法で HDInsight クラスターを作成できます。

* **Azure ポータル** - Web ベースのポータルを使用してクラスターを作成します。
* **Mac、Linux、Windows 用の Azure CLI** - コマンドラインでコマンドを使用してクラスターを作成します。

これらの方法では、それぞれパスワードまたは公開キーが必要です。 Linux ベースの HDInsight クラスターを作成する方法の詳細については、 [HDInsight での Hadoop Linux クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

### <a name="azure-portal"></a>Azure ポータル
[Azure Portal][preview-portal] を使用して Linux ベースの HDInsight クラスターを作成するときは、**SSH ユーザー名**を入力し、**パスワード**と **SSH 公開キー**のどちらかを選択して入力する必要があります。

**SSH 公開キー**を選択した場合は、拡張子が **.pub** のファイルに含まれている公開キーを **[SSH 公開キー]** フィールドに貼り付けるか、**[ファイルの選択]** を選択し、公開キーファイルを参照して選択できます。

![公開キーの入力を求めるフォームの画像](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [!NOTE]
> このキー ファイルは単なるテキスト ファイルです。 内容は次のようになります。
> 
> ```
> ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```
> 
> 

これで、入力したパスワードまたは公開キーを使用して、指定されたユーザーのログインが作成されます。

### <a name="azure-commandline-interface-for-mac-linux-and-windows"></a>Mac、Linux、Windows の Azure コマンド ライン インターフェイス
[Mac、Linux、Windows 用の Azure CLI](../xplat-cli-install.md) で、`azure hdinsight cluster create` コマンドを使用して新しいクラスターを作成できます。

このコマンドの使用方法の詳細については、「 [カスタム オプションを使用した HDInsight での Hadoop Linux クラスターのプロビジョニング](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

## <a name="connect-to-a-linuxbased-hdinsight-cluster"></a>Linux ベースの HDInsight クラスターへの接続
ターミナル セッションから、SSH コマンドを使用して、アドレスとユーザー名を入力し、クラスター ヘッド ノードに接続します。

* **SSH アドレス** - SSH でクラスターに接続するために使用できるアドレスは 2 つあります。
  
  * **ヘッド ノードに接続する**: クラスター名の後に「**-ssh.azurehdinsight.net**」と入力します  (**mycluster-ssh.azurehdinsight.net** など)。
  * **エッジ ノードに接続する**: クラスターが HDInsight 上の R Server である場合、クラスターにはエッジ ノードも含まれます。このエッジ ノードには、**RServer.CLUSTERNAME.ssh.azurehdinsight.net** を使用してアクセスできます。**CLUSTERNAME** は実際のクラスターの名前です。
* **ユーザー名** - クラスターの作成時に指定した SSH ユーザー名。

次の例では、ユーザー **me** として、**mycluster** のプライマリ ヘッド ノードに接続します。

    ssh me@mycluster-ssh.azurehdinsight.net

ユーザー アカウントにパスワードを使用した場合は、そのパスワードの入力を求められます。

パスフレーズで保護された SSH キーを使用した場合は、そのパスフレーズの入力を求められます。 パスフレーズを入力しない場合、SSH はローカルの秘密キーのいずれかをクライアントで使用して自動的に認証を試行します。

> [!NOTE]
> SSH が適切な秘密キーを使用して自動的に認証を行わない場合は、 **-i** パラメーターを使用して、秘密キーのパスを指定します。 次の例は、 `~/.ssh/id_rsa`から秘密キーを読み込みます。
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`
> 
> 

ヘッド ノードのアドレスを使用して接続する際に、ポートが指定されていない場合は、SSH のポートは既定でポート 22 に設定され、HDInsight クラスターのプライマリ ヘッド ノードに接続されます。 ポート 23 を使用すると、セカンダリに接続されます。 ヘッド ノードの詳細については、「 [HDInsight における Hadoop クラスターの可用性と信頼性](hdinsight-high-availability-linux.md)」を参照してください。

### <a name="connect-to-worker-nodes"></a>ワーカー ノードへの接続
ワーカー ノードは、Azure データセンターの外部からは直接アクセスできませんが、SSH を使用してクラスター ヘッド ノードからアクセスできます。

ユーザー アカウントの認証に SSH キーを使用する場合は、クライアントで次の手順を完了する必要があります。

1. テキスト エディターを使用して `~/.ssh/config`を開きます。 このファイルが存在しない場合は、ターミナルで `touch ~/.ssh/config` と入力して作成できます。
2. このファイルに次のコードを追加します。 *CLUSTERNAME* を、使用する HDInsight クラスターの名前に置き換えます。
   
        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes
   
    これは、HDInsight クラスターに SSH エージェント転送を構成します。
3. 端末から次のコマンドを使用して、SSH エージェント転送をテストします。
   
        echo "$SSH_AUTH_SOCK"
   
    次のような情報が返されます。
   
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
   
    何も返されない場合は、 **ssh-agent** が実行していないことを示します。 **ssh-agent**のインストールと構成に関する具体的な手順については、オペレーティング システムのマニュアルまたは「 [Using ssh-agent with ssh (ssh での ssh-agent の使用)](http://mah.everybody.org/docs/ssh)」を参照してください。
4. **ssh-agent** が実行していることを確認したら、次のコマンドを使用して SSH 秘密キーをエージェントに追加します。
   
        ssh-add ~/.ssh/id_rsa
   
    秘密キーを別のファイルに格納している場合は、 `~/.ssh/id_rsa` をそのファイルのパスに置き換えます。

次の手順を使用して、クラスターのワーカー ノードに接続します。

> [!IMPORTANT]
> アカウントの認証に SSH キーを使用する場合は、前の手順を完了してからエージェント転送が動作していることを確認する必要があります。
> 
> 

1. 前述のように、SSH を使用して HDInsight クラスターに接続します。
2. 接続したら、次のコマンドを使用してクラスター内のノードの一覧を取得します。 *ADMINPASSWORD* をクラスターの管理者アカウントのパスワードに置き換えます。 *CLUSTERNAME* をクラスターの名前に置き換えます。
   
        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts
   
    クラスター内のノードの `host_name` などの情報が JSON 形式で返されます。これには、各ノードの完全修飾ドメイン名 (FQDN) が含まれています。 **curl** コマンドによって返された `host_name` エントリの例を次に示します。
   
        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"
3. 接続するワーカー ノードの一覧を取得したら、SSH セッションからサーバーに次のコマンドを使用して、ワーカー ノードへの接続を開きます。
   
        ssh USERNAME@FQDN
   
    *USERNAME* を SSH ユーザー名に置き換え、*FQDN* をワーカー ノードの FQDN に置き換えます。 たとえば、「 `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`」のように入力します。
   
   > [!NOTE]
   > SSH セッションの認証にパスワードを使用する場合は、もう一度パスワードを入力するように求めるメッセージ表示されます。 SSH キーを使用する場合は、何も表示されずに接続が完了します。
   > 
   > 
4. セッションが確立されると、ターミナルのプロンプトが `username@hn#-clustername` から `username@wk#-clustername` に変わり、ワーカー ノードに接続したことを示します。 この時点で実行するすべてのコマンドは、ワーカー ノードで実行されます。
5. ワーカー ノードでの操作が終了したら、 `exit` コマンドを使用してワーカー ノードのセッションを閉じます。 これにより、 `username@hn#-clustername` プロンプトが表示されます。

## <a name="connect-to-a-domainjoined-hdinsight-cluster"></a>ドメイン参加済み HDInsight クラスターへの接続
[ドメインに参加している HDInsight](hdinsight-domain-joined-introduction.md) は、Kerberos を HDInsight の Hadoop と統合します。 SSH ユーザーは Active Direcotry ドメイン ユーザーではないため、このユーザー アカウントでは、ドメイン参加済みクラスターで SSH シェルから Hadoop コマンドを直接実行することはできません。 まず、*kinit* を実行する必要があります。 

**SSH を使用してドメイン参加済み HDInsight クラスターで Hive クエリを実行するには**

1. SSH を使用して、ドメイン参加済み HDInsight クラスターに接続します。  手順については、「[Linux ベースの HDInsight クラスターへの接続](#connect-to-a-linux-based-hdinsight-cluster)」をご覧ください。
2. kinit を実行します。 ドメイン ユーザー名とドメイン ユーザーのパスワードの入力を求められます。 ドメイン参加済み HDInsight クラスターのドメイン ユーザーを構成する方法の詳細については、[ドメイン参加済み HDInsight クラスターの構成](hdinsight-domain-joined-configure.md)に関する記事をご覧ください。
   
    ![HDInsight Hadoop のドメイン参加済み kinit](./media/hdinsight-hadoop-linux-use-ssh-unix/hdinsight-domain-joined-hadoop-kinit.png)
3. 次のように入力して Hive コンソールを開きます。
   
        hive
   
    これで Hive コマンドを実行できます。

## <a name="add-more-accounts"></a>複数のアカウントの追加
1. 新しいユーザー アカウントの公開キーと秘密キーを、 [SSH キーの作成](#create-an-ssh-key-optional) セクションの説明に従って作成します。
   
   > [!NOTE]
   > 秘密キーは、クラスターに接続するためにユーザーが使用するクライアントで生成するか、または生成後にそのクライアントに安全に転送する必要があります。
   > 
   > 
2. クラスターへの SSH セッションから、次のコマンドを使用して新しいユーザーを追加します。
   
        sudo adduser --disabled-password <username>
   
    これにより、新しいユーザー アカウントが作成されますが、パスワード認証は無効になります。
3. 次のコマンドを使用して、キーを格納するディレクトリとファイルを作成します。
   
        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys
4. ナノ エディターが開いたら、新しいユーザー アカウントのパブリック キーの内容をコピーして貼り付けます。 最後に、 **Ctrl + X** キーを使用してファイルを保存し、エディターを終了します。
   
    ![サンプル キーとナノ エディターの画像](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)
5. 次のコマンドを使用して、新しいユーザー アカウントに対する .ssh フォルダーの所有権をとコンテンツを変更します。
   
        sudo chown -hR <username>:<username> /home/<username>/.ssh
6. これで、新しいユーザー アカウントおよびプライベート キーを使用してサーバーへの認証を行えるようになります。

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>SSH トンネリング
SSH を使用して、Web 要求などのローカルの要求を HDInsight クラスターにトンネリングできます。 ここでは、最初から HDInsight クラスター ヘッド ノード上にあったかのように、要求が要求されたリソースにルーティングされます。

> [!IMPORTANT]
> SSH トンネルは、Hadoop サービス用の Web UI にアクセスするための要件です。 たとえば、ジョブ履歴 UI とリソース マネージャー UI は、両方とも SSH トンネルでのみアクセスできます。
> 
> 

SSH トンネルの作成と使用の詳細については、 [SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)方法に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
これで、SSH キーを使用して認証する方法、また、HDInsight 上の Hadoop で MapReduce を使用する方法についてご理解いただけたと思います。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Nov16_HO2-->


