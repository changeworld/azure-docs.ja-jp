<properties title="Azure ポータルで Docker VM 拡張機能を使用する方法" pageTitle="Azure での Linux 用 Docker VM 拡張機能の使用" description="Docker と Azure 仮想マシン拡張機能について説明し、azure-cli コマンド インターフェイスを使用してコマンド ラインから Azure 上に Docker ホストである仮想マシンをプログラムで作成する方法を示します。" metaKeywords="linux, virtual machines, vm, azure, docker, linux containers,  lxc, virtualization" services="virtual-machines" solutions="dev-test" documentationCenter="virtual-machines" authors="rasquill" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.devlang="multiple" ms.topic="article" ms.tgt_pltfrm="vm-linux" ms.workload="infrastructure-services" ms.date="10/21/2014" ms.author="rasquill" />
<!--The next line, with one pound sign at the beginning, is the page title-->

# Azure ポータルでの Docker VM 拡張機能の使用

[Docker][Docker] は、最もよく利用されている仮想化アプローチの 1 つで、データの分離と共有リソースでのコンピューティングの手段として仮想マシンではなく [Linux コンテナー][Linux コンテナー]を使用します。[Azure Linux エージェント][Azure Linux エージェント]に対して Docker VM 拡張機能を使用すれば、Azure 上に Docker VM を作成し、アプリケーション用に任意の数のコンテナーをホストさせることができます。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

このセクションの内容

-   [イメージ ギャラリーからの新しい VM の作成][イメージ ギャラリーからの新しい VM の作成]
-   [Docker 証明書の作成][Docker 証明書の作成]
-   [Docker VM 拡張機能の追加][Docker VM 拡張機能の追加]
-   [Docker クライアントと Azure Docker ホストのテスト][Docker クライアントと Azure Docker ホストのテスト]
-   [次のステップ][次のステップ]

> [WACOM.NOTE] このトピックでは、Azure ポータルで Docker VM を作成する方法について説明します。コマンド ラインで Docker VM を作成する方法については、「[Azure クロスプラットフォーム インターフェイス (xplat-cli) から Docker VM 拡張機能を使用する方法][Azure クロスプラットフォーム インターフェイス (xplat-cli) から Docker VM 拡張機能を使用する方法]」を参照してください。コンテナーとその利点に関する概要については、「[Docker High Level Whiteboard (Docker の概要ホワイトボード)][Docker High Level Whiteboard (Docker の概要ホワイトボード)]」を参照してください。

## <span id="createvm"></span>イメージ ギャラリーからの新しい VM の作成</a>

最初のステップとして、Docker VM 拡張機能をサポートする Linux イメージから Azure VM を作成する必要があります。イメージ ギャラリーから取得した Ubuntu 14.04 LTS イメージをサンプルのサーバー イメージとして、Ubuntu 14.04 デスクトップをクライアントとして使用します。ポータルで、左下の **[+ 新規]** をクリックし、新しい VM インスタンスを作成します。下図に示すように、使用可能な選択項目か、すべて表示されるイメージ ギャラリーから、Ubuntu 14.04 LTS イメージを選択します。

> [WACOM.NOTE] 現時点では、2014 年 7 月以降の Ubuntu 14.04 LTS イメージのみが Docker VM 拡張機能をサポートしています。

![Create a new Ubuntu Image][Create a new Ubuntu Image]

## <a id'dockercerts'>Docker 証明書の作成</a>

VM の作成が完了したら、クライアント コンピューターに Docker がインストールされていることを確認します (詳細については、[Docker のインストール手順][Docker のインストール手順]を参照してください)。

「[Running Docker with https (https を使用した Docker の実行)][Running Docker with https (https を使用した Docker の実行)]」に記載された手順に従って Docker の通信用の証明書とキー ファイルを作成し、クライアント コンピューターの **`~/.docker`** ディレクトリに配置します。

> [WACOM.NOTE] 現時点では、ポータルの Docker VM 拡張機能には Base64 でエンコードした証明書が必要です。

コマンド ラインで **`base64`** を使用するか、別の使い慣れたエンコード ツールを使用して、Base64 でエンコードされたトピックを作成します。証明書とキー ファイルのシンプルなセットをしてこの操作を行った場合、次のようになります。

     ~/.docker$ l
     ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
     ~/.docker$ base64 ca.pem > ca64.pem
     ~/.docker$ base64 server-cert.pem > server-cert64.pem
     ~/.docker$ base64 server-key.pem > server-key64.pem
     ~/.docker$ l
     ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
     ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem

## <a id'adddockerextension'>Docker VM 拡張機能の追加</a>

Docker VM 拡張機能を追加するには、作成した VM インスタンスを見つけ、下図に示す **[拡張機能]** まで下へスクロールし、クリックして VM 拡張機能を表示します。

![][0]

### 拡張機能の追加

**[+ 新規]** をクリックすると、この VM に追加できる VM 拡張機能が表示されます。

![][1]

### Docker VM 拡張機能の選択

Docker VM 拡張機能を選択します。この操作により、Docker の説明と重要なリンクが表示されます。下部にある **[作成]** をクリックすると、インストール手順が開始されます。

![][2]

![][3]

### 証明書とキー ファイルの追加

下図に示すように、フォームのフィールドに、Base64 でエンコードされた CA 証明書、サーバー証明書、およびサーバー キーを入力します。

![][4]

> [WACOM.NOTE] (上のイメージに示すように) 既定で 4243 が入力されています。ここに任意のエンドポイントを入力できますが、次のステップで、一致するエンドポイントを開く必要があります。既定値を変更した場合は、必ず、次のステップで一致するエンドポイントを開いてください。

## Docker 通信エンドポイントの追加

前に作成したリソース グループの VM が表示されているときに、下へスクロールして **[エンドポイント]** をクリックすると、下に示すように VM 上のエンドポイントが表示されます。

![][5]

**[+ 新規]** をクリックするとエンドポイントが 1 つ追加されます。既定で、エンドポイントの名前 (この例では **docker**) と、プライベート ポートおよびパブリック ポートとして 4243 が入力されています。**[TCP]** と表示されているプロトコルの値はそのままにし、**[OK]** をクリックして、エンドポイントを作成します。

![][6]

## <span id="testclientandserver"></span>Docker クライアントと Azure Docker ホストのテスト</a>

VM のドメイン名を見つけてコピーし、クライアント コンピューターのコマンド ラインで、「`docker --tls -H tcp://`*dockerextension*`.cloudapp.net:4243 info`」と入力します (*dockerextension* は、使用している VM のサブドメインに置き換えます)。

結果は次のようになります。

    $ docker --tls -H tcp://dockerextension.cloudapp.net:4243 info
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
     Pool Name: docker-8:1-131214-pool
     Pool Blocksize: 65.54 kB
     Data file: /var/lib/docker/devicemapper/devicemapper/data
     Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
     Data Space Used: 305.7 MB
     Data Space Total: 107.4 GB
     Metadata Space Used: 729.1 kB
     Metadata Space Total: 2.147 GB
     Library Version: 1.02.82-git (2013-10-04)
    Execution Driver: native-0.2
    Kernel Version: 3.13.0-36-generic
    WARNING: No swap limit support

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## 次のステップ

「[Docker ユーザー ガイド][Docker ユーザー ガイド]」にアクセスして Docker VM の使用を開始する準備が整いました。Azure で Docker VM を短時間で繰り返し作成する方法については、「[Azure クロスプラットフォーム インターフェイス (xplat-cli) から Docker VM 拡張機能を使用する方法][Azure クロスプラットフォーム インターフェイス (xplat-cli) から Docker VM 拡張機能を使用する方法]」を参照してください。

<!--Anchors--> <!--Image references--> <!--Link references-->

  [Docker]: https://www.docker.com/
  [Linux コンテナー]: http://en.wikipedia.org/wiki/LXC
  [Azure Linux エージェント]: ../virtual-machines-linux-agent-user-guide/
  [イメージ ギャラリーからの新しい VM の作成]: #createvm
  [Docker 証明書の作成]: #dockercerts
  [Docker VM 拡張機能の追加]: #adddockerextension
  [Docker クライアントと Azure Docker ホストのテスト]: #testclientandserver
  [次のステップ]: #next-steps
  [Azure クロスプラットフォーム インターフェイス (xplat-cli) から Docker VM 拡張機能を使用する方法]: http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-docker-with-xplat-cli/
  [Docker High Level Whiteboard (Docker の概要ホワイトボード)]: http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard
  [Create a new Ubuntu Image]: ./media/virtual-machines-docker-with-portal/ChooseUbuntu.png
  [Docker のインストール手順]: https://docs.docker.com/installation/#installation
  [Running Docker with https (https を使用した Docker の実行)]: http://docs.docker.com/articles/https/
  [0]: ./media/virtual-machines-docker-with-portal/ClickExtensions.png
  [1]: ./media/virtual-machines-docker-with-portal/ClickAdd.png
  [2]: ./media/virtual-machines-docker-with-portal/ChooseDockerExtension.png
  [3]: ./media/virtual-machines-docker-with-portal/CreateButtonFocus.png
  [4]: ./media/virtual-machines-docker-with-portal/AddExtensionFormFilled.png
  [5]: ./media/virtual-machines-docker-with-portal/AddingEndpoint.png
  [6]: ./media/virtual-machines-docker-with-portal/AddEndpointFormFilledOut.png
  [Docker ユーザー ガイド]: https://docs.docker.com/userguide/
