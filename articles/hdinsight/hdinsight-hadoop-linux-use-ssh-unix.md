---
title: "Windows、Linux、Unix、または OS X から HDInsight (Hadoop) で SSH を使用する | Microsoft Docs"
description: " Secure Shell (SSH) を使用して HDInsight にアクセスできます。 このドキュメントでは、Windows、Linux、Unix、または OS X の各クライアントから HDInsight で SSH を使用する方法について説明します。."
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
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 37409ad3f50cdd4a7a384c96a57a35ef8c83fb8f


---
# <a name="use-ssh-with-hdinsight-hadoop-from-windows-linux-unix-or-os-x"></a>Windows、Linux、Unix、または OS X から HDInsight (Hadoop) で SSH を使用する

> [!div class="op_single_selector"]
> * [PuTTY (Windows)](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [SSH (Windows、Linux、Unix、OS X)](hdinsight-hadoop-linux-use-ssh-unix.md)

[Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) を使えば、コマンド ライン インターフェイスで Linux ベースの HDInsight クラスターにログインし、コマンドを実行できます。 このドキュメントでは、SSH に関する基本的な情報のほか、HDInsight での SSH の使用に関する特定の情報を提供します。

## <a name="what-is-ssh"></a>SSH とは

SSH は、セキュリティで保護されていないネットワーク経由でリモート サーバーと安全に通信できるようにする暗号化ネットワーク プロトコルです。 SSH は、リモート サーバーへの安全なコマンド ライン ログインを実現するために使用されます。 この場合は、HDInsight クラスターのヘッド ノードまたはエッジ ノードです。

また、SSH を使用すれば、クライアントから HDInsight クラスターへのネットワーク トラフィックをトンネリングすることもできます。 トンネルを使用すると、インターネットに直接公開されていない HDInsight クラスター上のサービスにアクセスできます。 HDInsight での SSH トンネリングの使用の詳細については、[HDInsight での SSH トンネリングの使用](hdinsight-linux-ambari-ssh-tunnel.md)に関するページを参照してください。

## <a name="ssh-clients"></a>SSH クライアント

多くのオペレーティング システムでは、`ssh` および `scp` コマンド ライン ユーティリティを通じて SSH クライアント機能を提供しています。

* __ssh__: リモート コマンド ライン セッションの確立とトンネルの作成に使用できる一般的な SSH クライアント。
* __scp__: SSH プロトコルを使用してローカル システムとリモート システムの間でファイルをコピーするユーティリティ。

Windows では、Windows 10 Anniversary Edition までは SSH クライアントが提供されていませんでした。 このバージョンの Windows には、`ssh`、`scp`、およびその他の Linux コマンドを提供する、開発者向けの Bash on Windows 10 機能が含まれています。 Bash on Windows 10 の使用の詳細については、「[Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about)」を参照してください。

Windows を使用しており、Bash on Windows 10 にアクセスできない場合は、次の SSH クライアントをお勧めします。

* [Git For Windows](https://git-for-windows.github.io/): `ssh` および `scp` コマンド ライン ユーティリティを提供します。
* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/): グラフィカル SSH クライアントを提供します。
* [MobaXterm](http://mobaxterm.mobatek.net/): グラフィカル SSH クライアントを提供します。
* [Cygwin](https://cygwin.com/): `ssh` および `scp` コマンド ライン ユーティリティを提供します。

> [!NOTE]
> このドキュメントの手順では、`ssh` コマンドを使用できるものと想定しています。 puTTY や MobaXterm などのクライアントを使用している場合は、対応するコマンドとパラメーターについて、その製品のドキュメントを参照してください。

## <a name="ssh-authentication"></a>SSH 認証

SSH 接続は、パスワードまたは[公開キー暗号化 (https://en.wikipedia.org/wiki/Public-key_cryptography)](https://en.wikipedia.org/wiki/Public-key_cryptography) を使用して認証できます。 パスワードと違って、キーは攻撃の多くに対して脆弱ではないため、キーを使用するが最も安全なです。 ただし、キーの作成と管理はパスワードを使用するより複雑です。

公開キー暗号化を使用するには、"_公開_" キーと "_秘密_" キーのペアを作成する必要があります。

* **公開キー**は、HDInsight クラスターのノードか、公開キー暗号化で使用する他のサービスに読み込みます。

* **秘密キー**は、SSH クライアントを使用してログインするときに HDInsight クラスターに提示するものであり、自分の身元を検証に使用されます。 このキーは安全に保管してください。 このキーは共有しないようにしてください。

    秘密キーのパスフレーズを作成して、セキュリティを追加することができます。 キーを使用するには、事前にこのパスフレーズを指定しておく必要があります。

### <a name="create-a-public-and-private-key"></a>公開キーと秘密キーの作成

`ssh-keygen` ユーティリティは、HDInsight で使用する公開キーと秘密キーのペアを作成するための最も簡単な手段です。 コマンド ラインで次のコマンドを使用して、HDInsight で使用するための新しいキー ペアを作成します。

> [!NOTE]
> MobaXTerm や puTTY などの GUI SSH クライアントを使用している場合は、そのクライアントのドキュメントでキーの生成方法を調べてください。

    ssh-keygen -t rsa -b 2048

次の情報の入力を求められます。

* ファイルの場所: 場所は既定で `~/.ssh/id_rsa` に設定されます。

* オプションのパスフレーズ: パスフレーズを入力した場合は、HDInsight クラスターに対して認証するときに再入力する必要があります。

> [!IMPORTANT]
> パスフレーズは秘密キーのパスワードです。 認証に秘密キーを使用する場合は、キーを使用する前に、必ずパスフレーズを指定する必要があります。 だれかが秘密キーを入手しても、パスフレーズなしでは使用できません。
>
> パスフレーズを忘れた場合、それをリセットまたは回復する方法はありません。

コマンドが終了すると、2 つの新しいファイルが生成されます。

* __id\_rsa__: このファイルには秘密キーが含まれています。

    > [!WARNING]
    > 公開キーによって保護されたサービスへの不正アクセスを防止するには、このファイルへのアクセスを制限する必要があります。

* __id\_rsa.pub__: このファイルには公開キーが含まれています。 このファイルは、HDInsght クラスターを作成するときに使用します。

    > [!NOTE]
    > "_公開_" キーにだれがアクセスできるかは重要ではありません。 公開キー自体で実行できることは、秘密キーの検証だけです。 SSH サーバーなどのサービスでは、秘密キーによる認証の際に、公開キーを使用して身元が確認されます。

## <a name="configure-ssh-on-hdinsight"></a>HDInsight での SSH の構成

Linux ベースの HDInsight クラスターを作成する場合は、"_SSH ユーザー名_" のほかに、"_パスワード_" または "_公開キー_" を指定する必要があります。 クラスターの作成中に、この情報を使用して、HDInsight クラスター ノードでログインを作成します。 パスワードまたは公開キーは、ユーザー アカウントのセキュリティ保護に使用されます。

クラスター作成時における SSH の構成の詳細については、次のいずれかのドキュメントを参照してください。

* [Azure Portal を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Azure CLI を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
* [Azure PowerShell を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Azure Resource Manager テンプレートを使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* [.NET SDK を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [REST を使用した HDInsight の作成](hdinsight-hadoop-create-linux-clusters-curl-rest.md)

### <a name="additional-ssh-users"></a>追加の SSH ユーザー

作成したクラスターには SSH ユーザーを追加できますが、お勧めしません。

* 新しい SSH ユーザーは、クラスター内の各ノードに手動で追加する必要があります。

* 新しい SSH ユーザーには、HDInsight に対し、既定のユーザーと同じアクセスが付与されます。 SSH ユーザー アカウントに基づいて HDInsight のデータまたはジョブへのアクセスを制限することはできません。

ユーザーごとにアクセスを制限するには、ドメイン参加済み HDInsight クラスターを使用する必要があります。 ドメイン参加済み HDInsight では、Active Directory を使用してクラスター リソースへのアクセスを制御します。

ドメイン参加済み HDInsight クラスターを使用すれば、SSH を使用して接続した後で、Active Directory を使用して認証を行えます。 複数のユーザーが SSH を使用して接続し、その後で、Active Directory アカウントに対する認証を実行できます。 詳細については、「[ドメイン参加済み HDInsight](#domainjoined)」セクションを参照してください。

##<a name="a-idconnecta-connect-to-hdinsight"></a><a id="connect"></a> HDInsight への接続

HDInsight クラスターのすべてのノードで SSH サーバーが実行されている場合、ヘッド ノードまたはエッジ ノードにはパブリック インターネット経由でのみ接続できます。

* "_ヘッド ノード_" に接続するには、`CLUSTERNAME-ssh.azurehdinsight.net` を使用します。ここで、__CLUSTERNAME__ は HDInsight クラスターの名前です。 ポート 22 (SSH の既定値) で接続すると、プライマリ ヘッド ノードに接続します。 ポート 23 は、セカンダリ ヘッド ノードに接続します。

* "_エッジ ノード_" に接続するには、`EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net` を使用します。ここで、__EDGENAME__ はエッジ ノードの名前で、__CLUSTERNAME__ は HDInsight クラスターの名前です。 エッジ ノードに接続するときは、ポート 22 を使用します。

次の例は、SSH ユーザー名 __sshuser__ を使用して、__myhdi__ という名前のクラスターのヘッド ノードとエッジ ノードに接続する方法を示しています。 エッジ ノードの名前は __myedge__ です。

| 目的 | 方法 |
| ----- | ----- |
| プライマリ ヘッド ノードへの接続 | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| セカンダリ ヘッド ノードへの接続 | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| エッジ ノードへの接続 | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

SSH アカウントをセキュリティ保護するためにパスワードを使用している場合は、そのパスワードの入力を求められます。

公開キーを使用して SSH アカウントをセキュリティ保護する場合は、`-i` スイッチを使用して、一致する秘密キーへのパスを指定する必要があります。 次の例は、`-i` スイッチの使用方法を示しています。

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>その他のノードへの接続

ワーカー ノードと Zookeeper ノードには、クラスターの外部からは直接アクセスできませんが、クラスター ヘッド ノードまたはエッジ ノードからはアクセスできます。 そのための一般的な手順は次のとおりです。

1. SSH を使用してヘッド ノードまたはエッジ ノードに接続します。

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. ヘッド ノードまたはエッジ ノードへの SSH 接続から、`ssh` コマンドを使用してクラスター内のワーカー ノードに接続します。

        ssh sshuser@wn0-myhdi

    クラスター内のワーカー ノードの一覧を取得する場合は、「[Ambari REST API を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)」でクラスター ノードの完全修飾ドメイン名を取得する方法の例を参照してください。

SSH アカウントがパスワードを使用してセキュリティ保護されている場合は、パスワードの入力を求められ、その後接続が確立します。

ユーザー アカウントの認証に SSH キーを使用する場合は、ローカル環境が SSH エージェント転送向けに構成されていることを確認する必要があります。

> [!IMPORTANT]
> 次の手順では、Linux または UNIX ベースのシステムを想定して Bash on Windows 10 を使用します。 次の手順をお使いのシステムで利用できない場合は、必要に応じて SSH クライアントのドキュメントを参照してください。

1. テキスト エディターを使用して `~/.ssh/config`を開きます。 このファイルが存在しない場合は、コマンド ラインで「`touch ~/.ssh/config`」と入力して作成できます。

2. このファイルに次のコードを追加します。 *CLUSTERNAME* を、使用する HDInsight クラスターの名前に置き換えます。

        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes

    このエントリにより、HDInsight クラスターに SSH エージェント転送が構成されます。

3. 端末から次のコマンドを使用して、SSH エージェント転送をテストします。

        echo "$SSH_AUTH_SOCK"

    このコマンドでは、次のテキストのような情報が返されます。

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    何も返されない場合は、`ssh-agent` が実行されていないことを示します。 「[Using ssh-agent with ssh (ssh での ssh-agent の使用)](http://mah.everybody.org/docs/ssh)」(http://mah.everybody.org/docs/ssh) でエージェントのスタートアップ スクリプト情報を参照するか、お使いの SSH クライアントのドキュメントで `ssh-agent` をインストールして構成する手順を確認してください。

4. **ssh-agent** が実行していることを確認したら、次のコマンドを使用して SSH 秘密キーをエージェントに追加します。

        ssh-add ~/.ssh/id_rsa

    秘密キーを別のファイルに格納している場合は、 `~/.ssh/id_rsa` をそのファイルのパスに置き換えます。

<a id="domainjoined"></a>
### <a name="domain-joined-hdinsight"></a>ドメイン参加済み HDInsight

[ドメインに参加している HDInsight](hdinsight-domain-joined-introduction.md) は、Kerberos を HDInsight の Hadoop と統合します。 SSH ユーザーは Active Directory ドメイン ユーザーではないため、Active Directory で認証されるまで Hadoop コマンドを実行することはできません。 次の手順を使用して、Active Directory で SSH セッションを認証してください。

1. 「[HDInsight への接続](#connect)」セクションの説明のとおり、SSH を使用してドメイン参加済み HDInsight クラスターに接続します。 たとえば、次のコマンドを実行すると、__sshuser__ という SSH アカウントを使用して __myhdi__ という名前の HDInsight クラスターに接続します。

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. ドメイン ユーザーとパスワードを使用して認証するには、次を使用します。

        kinit

     メッセージが表示されたら、ドメイン ユーザーのドメイン ユーザー名とパスワードを入力します。

    ドメイン参加済み HDInsight クラスターのドメイン ユーザーを構成する方法の詳細については、[ドメイン参加済み HDInsight クラスターの構成](hdinsight-domain-joined-configure.md)に関する記事をご覧ください。

`kinit` コマンドを使用して認証した後、`hdfs dfs -ls /` や `hive` などのHadoop コマンドを使用できるようになります。

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>SSH トンネリング

SSH を使用して、Web 要求などのローカルの要求を HDInsight クラスターにトンネリングできます。 ここでは、最初から HDInsight クラスター ヘッド ノード上にあったかのように、要求が要求されたリソースにルーティングされます。

> [!IMPORTANT]
> SSH トンネルは、Hadoop サービス用の Web UI にアクセスするための要件です。 たとえば、ジョブ履歴 UI とリソース マネージャー UI は、両方とも SSH トンネルでのみアクセスできます。

SSH トンネルの作成と使用の詳細については、「[SSH トンネリングを使用して Ambari Web UI、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

これで、SSH キーを使用して認証する方法、また、HDInsight 上の Hadoop で MapReduce を使用する方法についてご理解いただけたと思います。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight の Hadoop での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce ジョブの使用](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Feb17_HO3-->


