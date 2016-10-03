<properties
    pageTitle="Linux ベースの HDInsight でのスクリプト アクション開発 | Microsoft Azure"
    description="Script Action を使って Linux ベースの HDInsight クラスターをカスタマイズする方法について説明します。"
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="larryfr"/>

# HDInsight でのスクリプト アクション開発

スクリプト アクションは、クラスターの構成設定を指定したり、追加のサービス、ツール、その他のソフトウェアをクラスターにインストールしたりして、Azure HDInsight クラスターをカスタマイズする方法です。クラスターの作成時または実行中のクラスターで、Script Action を使用できます。

> [AZURE.NOTE] このドキュメントの情報は、Linux ベースの HDInsight クラスターに固有のものです。Windows ベースのクラスターでのスクリプト アクションの使用方法の詳細については、[HDInsight (Windows) でのスクリプト アクションの開発](hdinsight-hadoop-script-actions.md)に関するページを参照してください。

## スクリプト アクションとは

Script Action は、Azure がクラスター ノードで実行して、構成の変更やソフトウェアのインストールを行う Bash スクリプトです。Script Action はルートとして実行され、完全なアクセス権をクラスター ノードに提供します。

スクリプト アクションは次の方法によって適用できます。

| これを使用してスクリプトを適用する... | クラスター作成時... | 実行中のクラスターで... |
| ----- |:-----:|:-----:|
| Azure ポータル | ✓ | ✓ |
| Azure PowerShell | ✓ | ✓ |
| Azure CLI | &nbsp; | ✓ |
| HDInsight .NET SDK | ✓ | ✓ |
| Azure Resource Manager テンプレート | ✓ | &nbsp; |

これらの方法を使用したスクリプト アクションの適用の詳細については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。

## <a name="bestPracticeScripting"></a>スクリプトの開発におけるベスト プラクティス

HDInsight クラスター向けのカスタム スクリプトを開発する際、留意すべきベスト プラクティスがあります。

- [Hadoop のバージョンを対象にする](#bPS1)
- [スクリプト リソースへの安定したリンクの提供](#bPS2)
- [プリコンパイル済みリソースの使用](#bPS4)
- [クラスターのカスタマイズ スクリプトはべき等にする](#bPS3)
- [クラスターのアーキテクチャの高可用性を確保](#bPS5)
- [Azure BLOB ストレージを使用するカスタム コンポーネントの構成](#bPS6)
- [STDOUT および STDERR に情報を書き込む](#bPS7)
- [LF 行の終わりで、ファイルを ASCII として保存する](#bps8)
- [再試行ロジックを使用して一時的なエラーから回復する](#bps9)

> [AZURE.IMPORTANT] スクリプト アクションは 60 分以内に完了する必要があります。そうしないと、タイムアウトします。ノードのプロビジョニング中、スクリプトは他のセットアップ プロセスや構成プロセスと同時に実行されます。CPU 時間やネットワーク帯域幅などのリソースの競合が原因で、開発環境の場合よりスクリプトの完了に時間がかかる可能性があります。

### <a name="bPS1"></a>Hadoop のバージョンを対象にする

HDInsight のバージョンが異なれば、異なるバージョンの Hadoop サービスとコンポーネントがインストールされます。スクリプトに特定のバージョンのサービスまたはコンポーネントが期待される場合、必要なコンポーネントを含むバージョンの HDInsight スクリプトのみを使用する必要があります。HDInsight に含まれているコンポーネントのバージョンの情報は、[HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md)に関するドキュメントで確認できます。

### <a name="bPS2"></a>スクリプト リソースへの安定したリンクの提供

スクリプトとスクリプトで使用されるリソースがクラスターの有効期間全体で使用できること、実行中これらのファイルのバージョンが変更されないことを確認する必要があります。これらのリソースは、スケーリング処理中に、新しいノードをクラスターに追加する場合に必要です。

ベスト プラクティスとして、すべてをダウンロードして、Azure Storage アカウントのサブスクリプションにアーカイブする方法があります。

> [AZURE.IMPORTANT] 使用されるストレージ アカウントは、クラスターの既定のストレージ アカウントかその他のストレージ アカウントにおける読み取り専用のパブリック コンテナーのいずれかにする必要があります。

たとえば、マイクロソフトから提供されるサンプルは、HDInsight チームによって管理される読み取り専用のパブリック コンテナーである、[https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) ストレージ アカウントに格納されています。

### <a name="bPS4"></a>プリコンパイル済みリソースの使用

スクリプトの実行にかかる時間を短縮するために、ソース コードからリソースをコンパイルする操作を行わないようにしてください。代わりに、リソースを事前にコンパイルし、バイナリ バージョンを Azure Blob ストレージに格納して、スクリプトからクラスターにすばやくダウンロードできるようにします。

### <a name="bPS3"></a>クラスターのカスタマイズ スクリプトはべき等にする

スクリプトが複数回実行された場合に、クラスターが実行されるたびに同じ状態に戻ることをスクリプトが確認するという意味で、スクリプトはべき等であるように設計する必要があります。

たとえば、カスタム スクリプトが最初の実行時にアプリケーションを /usr/local/bin にインストールする場合、その後スクリプトを実行するたびに、スクリプトはアプリケーションが /usr/local/bin に既に存在するかどうかを確認した後で、スクリプト内の他のステップを続行する必要があります。

### <a name="bPS5"></a>クラスターのアーキテクチャの高可用性を確保

Linux ベースの HDInsight クラスターは、クラスター内で有効な 2 つのヘッド ノードを提供し、スクリプト アクションがその両方のノードで実行されます。インストールするコンポーネントで 1 つのヘッド ノードしか期待されない場合は、そのコンポーネントがクラスターの 2 つのヘッド ノードのいずれかにしかインストールされないスクリプトを設計する必要があります。

> [AZURE.IMPORTANT] HDInsight の一部としてインストールされている既定のサービスは必要に応じて 2 つのヘッド ノードの間でフェールオーバーされるように設計されていますが、この機能はスクリプト アクションでインストールされるカスタム コンポーネントには拡張されません。スクリプト アクションからインストールされるコンポーネントを高可用性にする必要がある場合は、使用可能な 2 つのヘッド ノードを使用する独自のフェールオーバー メカニズムを実装する必要があります。

### <a name="bPS6"></a>Azure BLOB ストレージを使用するカスタム コンポーネントの構成

クラスターにインストールするコンポーネントに、Hadoop 分散ファイル システム (HDFS) ストレージを使用する既定の構成が含まれる可能性があります。HDInsight は、既定のストレージとして Azure BLOB ストレージ (WASB) を使用します。これは、クラスターが削除された場合でも、データを保持する HDFS 互換ファイル システムを提供します。HDFS の代わりに WASB を使用するように、インストールするコンポーネントを構成する必要があります。

たとえば、次の場合 giraph-examples.jar ファイルがローカル ファイル システムから WASB にコピーされます。

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>STDOUT および STDERR に情報を書き込む

スクリプトの実行時に STDOUT および STDERR に書き込まれる情報はログに記録され、Ambari の Web UI を使用して表示することができます。

> [AZURE.NOTE] Ambari は、クラスターが正常に作成された場合にのみ使用できます。クラスターの作成時にスクリプト アクションを使用して作成に失敗した場合は、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)に関するトラブルシューティング セクションで、ログに記録された情報にアクセスする他の方法を確認してください。

ほとんどのユーティリティとインストール パッケージは情報を STDOUT および STDERR に書き込みますが、ログ記録を追加することもできます。STDOUT にテキストを送信するには、`echo` を使用します。次に例を示します。

        echo "Getting ready to install Foo"

既定では、`echo` は、文字列を STDOUT に送信します。STDERR に転送するには、`echo` の前に `>&2` を追加します。次に例を示します。

        >&2 echo "An error occurred installing Foo"

これにより、STDOUT (既定の 1 であるため記載していません) に送信された情報が STDERR (2) にリダイレクトされます。IO リダイレクトの詳細については、[http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html) を参照してください。

スクリプト アクションによってログに記録される情報の表示の詳細については、[スクリプト アクションを使用した HDInsight クラスターのカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)に関するページを参照してください。

###<a name="bps8"></a>LF 行の終わりで、ファイルを ASCII として保存する

Bash スクリプトは、行が LF で終了する ASCII 形式で保存する必要があります。ファイルが UTF-8 として保存される場合、ファイルの先頭や、Windows エディターでは一般的な CRLF 行の終わりにバイト オーダー マークが含まれる可能性があるため、スクリプトが次のようなエラーで失敗します。

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a> 再試行ロジックを使用して一時的なエラーから回復する

ファイルのダウンロード時、apt-get を使用したパッケージのインストール時、またはインターネット経由でデータを転送するその他の操作時に、一時的なネットワーク エラーにより、操作に失敗する場合があります。たとえば、通信対象のリモート リソースが、バックアップ ノードへのフェールオーバー中である可能性があります。

一時的なエラーに対するスクリプトの回復力を高めるには、再試行ロジックを実装します。以下に示す関数の例では、渡された任意のコマンドを実行し、(コマンドが失敗した場合に) 最大 3 回まで再試行します。再試行の間隔は 2 秒です。

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

この関数の使用例を次に示します。

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>カスタム スクリプトのためのヘルパー メソッド

スクリプト アクションのヘルパー メソッドは、カスタム スクリプトの記述で利用できるユーティリティです。これらは [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) で定義されており、次を使用してスクリプトに含めることができます。

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

これにより、スクリプトで、次のヘルパーが使用できるようになります。

| ヘルパーの使用 | Description |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | 元の URL から指定されたファイルのパスに、ファイルをダウンロードします。既定では、既存のファイルは上書きされません。 |
| `untar_file TARFILE DESTDIR` | tar ファイルを (`-xf` を使用して) インストール先ディレクトリに抽出します。 |
| `test_is_headnode` | クラスターのヘッド ノードで実行された場合は 1 を返し、それ以外の場合は 0 を返します。 |
| `test_is_datanode` | 現在のノードがデータ (ワーカー) ノードの場合は 1 を返し、それ以外の場合は 0 を返します。 |
| `test_is_first_datanode` | 現在のノードが最初のデータ (ワーカー) ノード (workernode0) の場合は 1 を返し、それ以外の場合は 0 を返します。 |
| `get_headnodes` | クラスターのヘッドノードの完全修飾ドメイン名が返されます。名前はコンマで区切られます。エラーの場合は、空の文字列が返されます。 |
| `get_primary_headnode` | プライマリ ヘッドノードの完全修飾ドメイン名を取得します。エラーの場合は、空の文字列が返されます。 |
| `get_secondary_headnode` | セカンダリ ヘッドノードの完全修飾ドメイン名を取得します。エラーの場合は、空の文字列が返されます。 |
| `get_primary_headnode_number` | プライマリ ヘッドノードの数値のサフィックスを取得します。エラーの場合は、空の文字列が返されます。 |
| `get_secondary_headnode_number` | セカンダリ ヘッドノードの数値のサフィックスを取得します。エラーの場合は、空の文字列が返されます。 |

## <a name="commonusage"></a>一般的な使用パターン

このセクションでは、独自のカスタム スクリプトの書き込み中に発生する可能性がある一般的な使用状況パターンの実装についてのガイダンスを提供します。

### スクリプトにパラメーターを渡す

場合によっては、スクリプトにパラメーターが必要な場合があります。たとえば、Ambari の REST API から情報を取得するには、クラスターの管理者パスワードが必要になる場合があります。

スクリプトに渡されるパラメーターは_位置指定パラメーター_と呼ばれ、最初のパラメーターでは `$1` に、2 番目のパラメーターでは `$2` に割り当てられ、以下同様です。`$0` にはスクリプト自体の名前が含まれています。

パラメーターとしてスクリプトに渡される値は、その値がリテラルとして扱われ、含まれている '!' などの文字に特別な処理が指定されないように、単一引用符 (') で囲む必要があります。

### 環境変数の設定

環境変数を設定すると、次のように実行されます。

    VARIABLENAME=value

VARIABLENAME は、変数の名前です。この後に変数にアクセスするには、`$VARIABLENAME` を使用します。たとえば、PASSWORD という名前の環境変数として位置指定パラメーターで指定された値を割り当てるには、次のように使用します。

    PASSWORD=$1

以後のこの情報へのアクセスでは `$PASSWORD` が使用されます。

スクリプト内で設定された環境変数は、スクリプトのスコープ内でのみ存在します。場合によっては、スクリプトの終了後に永続化されるシステム全体の環境変数を追加する必要があります。通常、これは SSH を使用してクラスターに接続するユーザーが、スクリプトによってインストールされるコンポーネントを使用できるようにするためです。これは、環境変数を `/etc/environment` に追加することで実行できます。たとえば、次の場合、__HADOOP\_CONF\_DIR__ が追加されます。

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### カスタム スクリプトを保管する場所へのアクセス

クラスターのカスタマイズに使用したスクリプトは、クラスターの既定のストレージ アカウント、またはその他のストレージ アカウントがある場合は読み取り専用のパブリック コンテナーに格納する必要があります。スクリプトが他の場所に配置されているリソースにアクセスする場合、パブリックでもアクセスできる (少なくともパブリック読み取り専用にする) ようにする必要があります。たとえば、`download_file` を使用してクラスターにファイルをダウンロードすることができます。

クラスター (既定のストレージ アカウントなど) からアクセスできる Azure のストレージ アカウントにファイルを格納すると、このストレージが Azure ネットワーク内にあるため、アクセスが速くなります。

## <a name="deployScript"></a>スクリプト アクションのデプロイ用チェックリスト

スクリプトのデプロイを準備する際に実行した手順を次に示します。

- カスタム スクリプトが含まれているファイルを、デプロイ時にクラスター ノードからアクセス可能な場所に配置します。これには、クラスターのデプロイ時に指定された既定または追加のストレージ アカウント、またはその他のパブリックにアクセス可能なストレージ コンテナーを指定できます。

- スクリプトがべき等に実行されるようにチェックを追加し、スクリプトが同じノードで複数回実行できるようにします。

- /tmp などの一時ファイル ディレクトリを使用して、スクリプトで使用するダウンロード済みファイルを維持し、スクリプトの実行後にそれらをクリーンアップします。

- OS レベル設定や Hadoop サービス構成ファイルが変更された場合、スクリプトに設定された環境変数などの OS レベル設定を有効にするために、HDInsight サービスを再起動することをお勧めします。

## <a name="runScriptAction"></a>Script Action の実行方法

スクリプト アクションを使用して、Azure ポータル、Azure PowerShell、Azure Resource Manager (ARM) テンプレート、または HDInsight .NET SDK によって HDInsight クラスターをカスタマイズすることができます。手順については、[スクリプト アクションの使用方法](hdinsight-hadoop-customize-cluster-linux.md)に関するページを参照してください。

## <a name="sampleScripts"></a>カスタム スクリプトのサンプル

Microsoft は、HDInsight クラスターにコンポーネントをインストールするサンプル スクリプトを提供しています。サンプル スクリプトとその使用方法については、以下のリンクから入手できます。

- [HDInsight クラスターに Hue をインストールして使用する](hdinsight-hadoop-hue-linux.md)
- [HDInsight Hadoop クラスターに R をインストールして使用する](hdinsight-hadoop-r-scripts-linux.md)
- [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install-linux.md)
- [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install-linux.md)

> [AZURE.NOTE] 上のリンクのドキュメントは、Linux ベースの HDInsight クラスターに固有のものです。Windows ベースの HDInsight と連携するスクリプトについては、[HDInsight でのスクリプト アクションの開発 (Windows)](hdinsight-hadoop-script-actions.md) に関する記述を参照するか、各記事の上部にあるリンクを使用します。

##トラブルシューティング

次に、開発したスクリプトを使用しているときに発生するエラーを示します。

__エラー__: `$'\r': command not found`。`syntax error: unexpected end of file` が続くこともあります。

_原因_: このエラーはスクリプトの行が CRLF で終了する場合に発生します。Unix システムでは、LF が行の終わりとしてのみ想定されます。

Windows の多くのテキスト エディターでは CRLF が一般的な行の終わりであるため、この問題は、Windows 環境でスクリプトが作成されたときに特に多く発生します。

_解決_: これがテキスト エディターのオプションである場合は、行の終わりとして Unix 形式または LE を選択します。Unix システムで次のコマンドを使用して CRLF を LF に変更することもできます。

> [AZURE.NOTE] CRLF 行の終わりが LF に変更されるという点で、次のコマンドはほぼ同等です。システムで使用できるユーティリティに基づいて、いずれかを選択します。

| コマンド | メモ |
| ------- | ----- |
| `unix2dos -b INFILE` | 元のファイルは .BAK の拡張子でバックアップされます |
| `tr -d '\r' < INFILE > OUTFILE` | OUTFILE には改行が LF のみのバージョンが含まれます |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | 新しいファイルを作成せずに、ファイルを直接変更します |
| ```sed 's/$'"/`echo \\r`/" INFILE > OUTFILE``` | OUTFILE には改行が LF のみのバージョンが含まれます。

__エラー__: `line 1: #!/usr/bin/env: No such file or directory`。

_原因_: このエラーはスクリプトがバイト オーダー マーク (BOM) を含む UTF-8 として保存された場合に発生します。

_解決_: ファイルを ASCII として、または BOM なしの UTF-8 として保存します。Linux または Unix システムで次のコマンドを使用して、BOM なしの新しいファイルを作成することもできます。

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

上記のコマンドで、__INFILE__ を BOM を含むファイルに置き換えます。__OUTFILE__ は、BOM なしのスクリプトを含む新しいファイルの名前にする必要があります。

## <a name="seeAlso"></a>次のステップ

* [スクリプト アクションを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)方法を学習します。

* [HDInsight .NET SDK リファレンス](https://msdn.microsoft.com/library/mt271028.aspx)を使用して、HDInsight を管理する .NET アプリケーションの作成の詳細について理解します。

* [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) を使用して、REST を使って HDInsight クラスターで管理操作を実行する方法について理解します。

<!---HONumber=AcomDC_0921_2016-->