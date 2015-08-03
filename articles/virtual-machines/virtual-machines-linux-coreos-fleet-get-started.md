<properties
	pageTitle="Azure 上の CoreOS で fleet を使ってみる"
	description="Azure の CoreOS Linux 仮想マシン上での fleet と Docker を使用方法を紹介します。"
	services="virtual-machines"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor="madhana"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-linux"
	ms.workload="infrastructure-services"
	ms.date="03/17/2015"
	ms.author="danlep"/>

# Azure 上の CoreOS で fleet を使ってみる

この記事では、[fleet](https://github.com/coreos/fleet) と [Docker](https://www.docker.com/) を使用して、[CoreOS] 仮想マシンのクラスターでアプリケーションを実行する簡単な例を 2 つ紹介します。

これらの例を使用するには、「[Azure 上で CoreOS を使用する方法]」の説明に従がって、3 ノード構成の CoreOS クラスターを設定します。 設定が終わったら、CoreOS デプロイメントの極めて基本的な要素について理解し、動作中のクラスターとクライアント コンピューターが準備できたことになります。これらの例では、同じクラスター名を使用します。また、これらの例では **fleet** コマンドの実行にローカルの Linux ホストを使用することが前提になっています。




## <a id='simple'>例 1: Docker を使用した Hello World</a>

これは、1 つの Docker コンテナーで実行されている単純な「Hello World」アプリケーションです。ここでは [BusyBox Docker Hub イメージ] が使用されています。

Linux クライアント コンピューターで、任意のテキスト エディターを使用して **systemd** ユニット ファイルを作成し、`helloworld.service` という名前を付けます (詳細については、[ユニット ファイル]を参照してください)。

```
[Unit]
Description=HelloWorld
After=docker.service
Requires=docker.service

[Service]

TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill busybox1
ExecStartPre=-/usr/bin/docker rm busybox1
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name busybox1 busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=/usr/bin/docker stop busybox1

```

次に CoreOS クラスターに接続し、以下に示す **fleetctl** コマンドを実行してユニットを開始します。出力に、ユニットが開始したこと、およびユニットの場所が表示されます。


```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start helloworld.service


Unit helloworld.service launched on 62f0f66e.../100.79.86.62
```

>[AZURE.NOTE]リモート **fleetctl** コマンドを **--tunnel** パラメーターなしで実行するには、要求がトンネリングされるように FLEETCTL_TUNNEL 環境変数を設定します。たとえば、「`export FLEETCTL_TUNNEL=coreos-cluster.cloudapp.net:22`」のように入力します


コンテナーに接続すると、サービスの出力を参照できます。

```
fleetctl --tunnel coreos-cloudapp.cluster.net:22 journal helloworld.service

Mar 04 21:29:26 node-1 docker[57876]: Hello World
Mar 04 21:29:27 node-1 docker[57876]: Hello World
Mar 04 21:29:28 node-1 docker[57876]: Hello World
Mar 04 21:29:29 node-1 docker[57876]: Hello World
Mar 04 21:29:30 node-1 docker[57876]: Hello World
Mar 04 21:29:31 node-1 docker[57876]: Hello World
Mar 04 21:29:32 node-1 docker[57876]: Hello World
Mar 04 21:29:33 node-1 docker[57876]: Hello World
Mar 04 21:29:34 node-1 docker[57876]: Hello World
Mar 04 21:29:35 node-1 docker[57876]: Hello World
```

クリーンアップするには、そのユニットを停止し、アンロードします。

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop helloworld.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload helloworld.service
```


## <a id='highavail'>例 2: 高可用性の Apache サーバー</a>

CoreOS、Docker、および** fleet **を使用する利点の 1 つは、可用性の高い方法だとサービス実行が簡単になるということです。この例では、3 つの同じコンテナーで構成される、Apache Web サーバーを実行するサービスをデプロイします。これらのコンテナーは、クラスター内にある 3 つの VM 上で実行されます。この例は、 「[fleet を使用してコンテナーを起動する]」で説明した例に似ており、 [CoreOS Apache Docker Hub イメージ]が使用されています。

>[AZURE.NOTE]高可用性の Apache サーバーを実行するには、仮想マシン上で負荷分散された HTTP エンドポイントを構成する必要があります (パブリック ポート 80、プライベート ポート 80) 。これは、CoreOS クラスターの作成後に、Azure 管理ポータルまたは **azure vm endpoint** コマンドを使用すると実行できます。詳細については、「[負荷分散セットの構成]」を参照してください。

クライアント コンピューターで、任意のテキスト エディターを使用して **systemd** テンプレート ユニット ファイルを作成し、apache@.service という名前を付けます。このテンプレートを使用して、apache@1.service 、apache@2.service、および apache@3.service という名前の 3 つの独立したインスタンスを起動します。

```
[Unit]
Description=High Availability Apache
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill apache1
ExecStartPre=-/usr/bin/docker rm apache1
ExecStartPre=/usr/bin/docker pull coreos/apache
ExecStart=/usr/bin/docker run -rm --name apache1 -p 80:80 coreos/apache /usr/sbin/apache2ctl -D FOREGROUND
ExecStop=/usr/bin/docker stop apache1

[X-Fleet]
X-Conflicts=apache@*.service
```

>[AZURE.NOTE]特定の CoreOS ホストで実行できるのは、このコンテナーに含まれる 1 つのインスタンスのみであることが、 `X-Conflicts` 属性によって通知されます。詳細については、[ユニット ファイル]を参照してください。

次に、CoreOS クラスター上で、これらのユニット インスタンスを開始します。3 つの異なるマシン上でユニット インスタンスが実行されていることを確認してください。

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 start apache@{1,2,3}.service

unit apache@3.service launched on 00c927e4.../100.79.62.16
unit apache@1.service launched on 62f0f66e.../100.79.86.62
unit apache@2.service launched on df85f2d1.../100.78.126.15

```
これらのユニットのいずれか 1 つで実行されている Apache サーバーに接続するには、その CoreOS クラスターをホストしているクラウド サービスに簡単な要求を送信します。

`curl http://coreos-cluster.cloudapp.net`

次のような既定テキストが、Apache サーバーから返されます。

```
<htm\l><body><h1>It works!</h1>
<p>This is the default web page for this server.</p>
<p>The web server software is running but no content has been added, yet.</p>
</body></html>
```

Apache サービスが続行することを確認するために、クラスター内の 1 つまたは複数の仮想マシンをシャット ダウンすることもできます。

完了したら、ユニットを停止してアンロードします。

```
fleetctl --tunnel coreos-cluster.cloudapp.net:22 stop apache@{1,2,3}.service
fleetctl --tunnel coreos-cluster.cloudapp.net:22 unload apache@{1,2,3}.service

```

## 次のステップ

Azure 上の 3 ノード構成 CoreOS クラスターでは、この他さまざまな操作を試すことができます。「[Tim Park による CoreOS のチュートリアル]」、「[Patrick Chanezon による CoreOS チュートリアル]」、「[Docker]」のドキュメント、および「[CoreOS の概要]」を参照しながら、より複雑なクラスターを作成する方法と、Docker を使用したより興味深いアプリケーションを作成する方法を確認できます。

Azure での Linux VM およびオープン ソース環境の使用に関する詳細は、「[Azure 上での Linux およびオープン ソース コンピューティング]」を参照してください。

<!--Link references-->
[Azure Command-Line Interface (Azure)]: ../xplat-cli.md
[CoreOS]: https://coreos.com/
[CoreOS の概要]: https://coreos.com/using-coreos/
[CoreOS with Azure]: https://coreos.com/docs/running-coreos/cloud-providers/azure/
[Tim Park による CoreOS のチュートリアル]: https://github.com/timfpark/coreos-azure
[Patrick Chanezon による CoreOS チュートリアル]: https://github.com/chanezon/azure-linux/tree/master/coreos/cloud-init
[Docker]: http://docker.io
[YAML]: http://yaml.org/
[Azure 上で CoreOS を使用する方法]: virtual-machines-linux-coreos-how-to.md
[負荷分散セットの構成]: http://msdn.microsoft.com/library/azure/dn655055.aspx
[fleet を使用してコンテナーを起動する]: https://coreos.com/docs/launching-containers/launching/launching-containers-fleet/
[ユニット ファイル]: https://coreos.com/docs/launching-containers/launching/fleet-unit-files/
[BusyBox Docker Hub イメージ]: https://registry.hub.docker.com/_/busybox/
[CoreOS Apache Docker Hub イメージ]: https://registry.hub.docker.com/u/coreos/apache/
[Azure 上での Linux およびオープン ソース コンピューティング]: virtual-machines-linux-opensource.md
 

<!---HONumber=July15_HO4-->