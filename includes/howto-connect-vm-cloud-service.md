<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#クラウド サービス内の仮想マシンを相互に接続する方法

仮想マシンを作成すると、そのマシンを含めるクラウド サービスが自動的に作成されます。同じクラウド サービス内に複数の仮想マシンを作成することができるため、仮想マシン間の相互通信を行うことができます。 

> [WACOM.NOTE] VM が同じクラウド サービス内にある場合、追加の作業を行うことで、仮想マシン間での負荷分散や高可用性の管理を行うこともできます。詳細については、「[Azure インフラストラクチャ サービスの負荷分散](../../articles/load-balance-virtual-machines/) 」および「[仮想マシンの可用性管理](../../articles/manage-availability-virtual-machines/)」を参照してください。 

まず、新しいクラウド サービスで仮想マシンを作成する必要があります。その後、同じクラウド サービスに配置される追加の仮想マシンを作成します。これを "接続" します。 

1. 「[カスタム仮想マシンの作成方法](../../articles/virtual-machines-create-custom/)」に示された手順に従って最初の仮想マシンを作成します。

2. 追加の仮想マシンを作成する場合も、基本的な手順は同じですが、クラウド サービスを作成するのではなく、クラウド サービスに仮想マシンを追加する点が異なります。たとえば、*EndpointTest* という名前のクラウド サービスを作成した場合、そのサービスを選択します。次の図で説明します。

	![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

14. このページのフィールドの残りの部分を完成させます。次に、仮想マシンへの接続を作成するには、チェック マークをクリックします。

#リソース

仮想マシンを作成したら、サービスやワークロードがデータを格納するための場所として、データ ディスクを追加することをお勧めします。次を参照してください。

[データ ディスクを Linux 仮想マシンに接続する方法](http://azure.microsoft.com/ja-jp/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[データ ディスクを Windows 仮想マシンに接続する方法](http://azure.microsoft.com/ja-jp/documentation/articles/storage-windows-attach-disk/)



<!--HONumber=35_1-->
