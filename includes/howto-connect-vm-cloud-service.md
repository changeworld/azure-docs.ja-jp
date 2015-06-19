<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#仮想マシンを仮想ネットワークまたはクラウド サービスと接続する方法

仮想マシンはクラウド サービス内にある必要があります。クラウド サービスはコンテナーとして機能し、インターネット経由で仮想マシンにアクセスするために、一意のパブリック DNS 名、パブリック IP アドレス、一連のエンドポイントを提供します。クラウド サービスは必要に応じて仮想ネットワーク内に含めることができます。 

クラウド サービスが仮想ネットワーク内にない場合、そのクラウド サービス内にある仮想マシンが他の仮想マシンと通信するには他の仮想マシンのパブリック DNS 名を使用する必要があり、そのトラフィックはインターネットに送信されます。クラウド サービスが仮想ネットワークにある場合、そのクラウド サービス内にある仮想マシンは、トラフィックをインターネットに送信することなく、仮想ネットワーク内の他のすべての仮想マシンと通信できます。

同じスタンドアロンのクラウド サービスに仮想マシンを配置する場合には、負荷分散と可用性セットを活用できます。詳細については、[仮想マシンの負荷分散](../../articles/load-balance-virtual-machines/)に関するページと、「[仮想マシンの可用性管理](../../articles/manage-availability-virtual-machines/)」を参照してください。ただし、仮想マシンをサブネットでまとめることや、スタンドアロンのクラウド サービスをオンプレミスのネットワークに接続することはできません。たとえば次のようになります。

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
仮想マシンを仮想ネットワークに配置する場合、負荷分散と可用性セットを利用するために使用するクラウド サービスの数を決めることができます。さらに、オンプレミスのネットワークと同じように仮想マシンをサブネットにまとめ、仮想ネットワークをオンプレミスのネットワークに接続することもできます。たとえば次のようになります。

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

仮想ネットワークは、Azure で仮想マシンを接続するためのお勧めの方法です。また、アプリケーションの各階層を別々のクラウド サービスで構成することもお勧めします。これにより、ロール ベースのアクセス制御 (RBAC) による高度なユーザー権利の委任が可能になります。詳細については、「[Azure プレビュー ポータルでの役割ベースのアクセス制御](../../articles//role-based-access-control-configure/)」を参照してください。ただし、各サブスクリプションで割り当てられるクラウド サービスの最大数である 200 を超えないように、異なるアプリケーション層にあるいくつかの仮想マシンを同じクラウド サービスにまとめることが必要になる場合もあります。

仮想マシンを仮想ネットワークで接続する方法:

1.	Azure の管理ポータルで仮想ネットワークを作成します。詳細については、「[仮想ネットワークの構成タスク](https://msdn.microsoft.com/library/azure/jj156206.aspx)」を参照してください。
2.	Azure の管理ポータルで **[新規]、[コンピューティング]、[クラウド サービス]、[カスタム作成]** の順にクリックし、デプロイメントの一連のクラウド サービスを作成して、可用性セットと負荷分散の設計を反映します。
3.	新しい仮想マシンをそれぞれ作成するときには、正しいクラウド サービスと仮想ネットワークを指定します。仮想ネットワークに既に関連付けられているクラウド サービスがある場合、その名前が既定で選択されます。

以下は Azure の管理ポータルの使用例です。

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)
 
仮想マシンをスタンドアロンのクラウド サービスで接続するには:

1.	Azure の管理ポータルで **[新規]、[コンピューティング]、[クラウド サービス]、[カスタム作成]** の順にクリックして、デプロイメントのクラウド サービスを作成します。
2.	仮想マシンを作成するときは、手順 1. で作成したクラウド サービス名を指定します。 
また、仮想マシンを初めて作成するときに、デプロイメントのクラウド サービスを作成することもできます。

以下は、Azure の管理ポータルで EndpointTest という名前の既存のクラウド サービスを使用する例です。
 
![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##リソース
[仮想マシンの負荷分散](../../articles/load-balance-virtual-machines/)に関するページ

[仮想マシンの可用性管理](../../articles/manage-availability-virtual-machines/)

[仮想ネットワークの構成タスク](https://msdn.microsoft.com/library/azure/jj156206.aspx)

仮想マシンを作成したら、サービスやワークロードがデータを格納するための場所として、データ ディスクを追加することをお勧めします。次を参照してください。

[データ ディスクを Linux 仮想マシンに接続する方法](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[データ ディスクを Windows 仮想マシンに接続する方法](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=45--> 
