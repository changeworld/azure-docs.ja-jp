<properties 
    pageTitle="Linux VM に Apache Qpid PROTON-C をインストールする方法 |Microsoft Azure"
    description="Azure Virtual Machines を使用して CentOS Linux VM を作成し、Apache Qpid Proton-C ライブラリをビルドしてインストールする方法です。"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />


# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Azure Linux VM に Apache Qpid Proton-C をインストールする

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

このセクションでは、Azure Virtual Machines を使用して CentOS Linux VM を作成する方法と、Python および PHP 言語バインドと共に Apache Qpid PROTON-C ライブラリをダウンロード、ビルド、インストールする方法を示します。 次の手順を完了すると、このガイドに含まれる Python および PHP のサンプルを実行できます。

最初の手順は、[Azure クラシック ポータル][]を使用して実行します。 次のスクリーンショットでは、"scott centos" という名前の CentOS VM を作成する方法を示します。

![Azure Linux VM 上の Proton][0]

プロビジョニングが済むと、ポータルには次のように表示されます。

![Azure Linux VM 上の Proton][1]

コンピューターにログオンするには、SSH のエンドポイント ポートを知る必要があります。 この値は、新しく作成した VM を選択して **[エンドポイント]** タブをクリックすることにより、[Azure クラシック ポータル][]から取得できます。 次のスクリーンショットでは、このコンピューターのパブリック SSH ポートが 57146 であることが示されています。

![Azure Linux VM 上の Proton][2]

これで、SSH を使用してコンピューターに接続できます。 この例では、次のスクリーンショットのように PuTTY ツールを使用します。

![Azure Linux VM 上の Proton][3]

Python および PHP アプリの場合、この例では Apache の Proton クライアント ライブラリを使用します。 これらのライブラリは、[http://qpid.apache.org/download.html](http://qpid.apache.org/download.html) からダウンロードできます。 配布パッケージの Readme ファイルで、依存しているものをインストールして Proton をビルドするために必要な手順が説明されています。 手順の概要を次に示します。

1.  yum 構成ファイル (/etc/yum.conf) を編集し、カーネル ヘッダーに対する更新の除外 (\# exclude=kernel\*) のコメントを外します。 これは、gcc コンパイラーをインストールするために必要です。

2.  前提条件となるパッケージをインストールします。

    ```
    # required dependencies 
    yum install gcc cmake libuuid-devel
    
    # dependencies needed for ssl support
    yum install openssl-devel
    
    # dependencies needed for bindings
    yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
    
    # dependencies needed for python docs
    yum install epydoc
    ```

1.  Proton ライブラリをダウンロードします。

    ```
    [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
    Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 868226 (848K) [application/x-gzip]
    Saving to: ‘qpid-proton-0.9.tar.gz’
    
    qpid-proton-0.9.tar.gz                               
    
    100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
    
    2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
    ```

1.  配布アーカイブから Proton コードを抽出します。

    ```
    tar xvfz qpid-proton-0.9.tar.gz
    ```

1.  Readme ファイルから抜粋した次の手順を使用し、コードをビルドしてインストールします。

    ```
    From the directory where you found this README file:    
    
    mkdir build cd build
            
    # Set the install prefix. You may need to adjust depending on your      
    # system.       
    cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
            
    # Omit the docs target if you do not wish to build or install       
    # documentation.        
    make all docs
            
    # Note that this step will require root privileges.     
    make install
    ```

この手順を実行すると、Proton がコンピューターにインストールされて、使用可能な状態になります。

## <a name="next-steps"></a>次のステップ

さらに詳しい情報については、 次のリンク先を参照してください。

- [Service Bus AMQP の概要][]

[Service Bus AMQP の概要]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Azure クラシック ポータル]: http://manage.windowsazure.com





<!--HONumber=Oct16_HO2-->


