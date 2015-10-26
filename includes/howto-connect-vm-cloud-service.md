サービス管理デプロイメント モデルで作成された仮想マシンは、常にクラウド サービス内に配置されます。クラウド サービスはコンテナーとして機能し、インターネット経由で仮想マシンにアクセスするために、一意のパブリック DNS 名、パブリック IP アドレス、一連のエンドポイントを提供します。クラウド サービスは仮想ネットワークに配置できますが、必須ではありません。

クラウド サービスが仮想ネットワーク内に含まれていない場合は、*スタンドアロン* クラウド サービスといいます。スタンドアロン クラウド サービス内にある仮想マシンが他の仮想マシンと通信するには、他の仮想マシンのパブリック DNS 名を使用する必要があり、そのトラフィックはインターネットを介して送信されます。クラウド サービスが仮想ネットワークにある場合、そのクラウド サービス内にある仮想マシンは、トラフィックをインターネットに送信することなく、仮想ネットワーク内の他のすべての仮想マシンと通信できます。

同じスタンドアロン クラウド サービスに仮想マシンを配置する場合は、負荷分散と可用性セットを引き続き使用できます。詳細については、[仮想マシンの負荷分散](../articles/load-balance-virtual-machines.md)に関するページと、「[仮想マシンの可用性管理](../articles/manage-availability-virtual-machines.md)」を参照してください。ただし、仮想マシンをサブネットでまとめることや、スタンドアロン クラウド サービスをオンプレミスのネットワークに接続することはできません。次に例を示します。

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)

仮想マシンを仮想ネットワークに配置する場合、負荷分散と可用性セットに使用するクラウド サービスの数を決めることができます。さらに、オンプレミスのネットワークと同じように仮想マシンをサブネットにまとめ、仮想ネットワークをオンプレミスのネットワークに接続することもできます。次に例を示します。

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

仮想ネットワークは、Azure で仮想マシンを接続するためのお勧めの方法です。また、アプリケーションの各階層を別々のクラウド サービスで構成することもお勧めします。ただし、各サブスクリプションで割り当てられるクラウド サービスの最大数である 200 を超えないように、異なるアプリケーション層にあるいくつかの仮想マシンを同じクラウド サービスにまとめることが必要になる場合もあります。これとその他の制限については、「[Azure サブスクリプションとサービスの制限、クォータ、および制約](../azure-subscription-service-limits.md)」をご覧ください。

## 仮想ネットワークでの VM の接続

仮想マシンを仮想ネットワークで接続する方法:

1.	[Azure ポータル](http://manage.windowsazure.com)で仮想ネットワークを作成します。詳細については、「[Virtual Network の構成タスク](../documentation/services/virtual-machines/)」を参照してください。
2.	デプロイで可用性セットと負荷分散用の設計を反映させるため、クラウド サービスのセットを作成します。ポータルで、各クラウド サービスについて **[新規]、[Compute]、[Cloud Services]、[カスタム作成]** の順にクリックします。
3.	新しい仮想マシンを作成するには、それぞれについて **[新規]、[Compute]、[仮想マシン]、[ギャラリーから]** の順にクリックします。VM の適切なクラウド サービスと仮想ネットワークを選択します。仮想ネットワークに既に参加しているクラウド サービスがある場合、その名前が既定で選択されます。

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## スタンドアロン クラウド サービスでの VM の接続

仮想マシンをスタンドアロンのクラウド サービスで接続するには:

1.	[Azure ポータル](http://manage.windowsazure.com)でクラウド サービスを作成します。**[新規]、[Compute]、[クラウド サービス]、[カスタム作成]** の順にクリックします。また、仮想マシンを初めて作成するときに、デプロイするクラウド サービスを作成することもできます。
2.	仮想マシンを作成するときは、前の手順で作成したクラウド サービス名を選択します。![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##リソース
[仮想マシンの負荷分散](../articles/load-balance-virtual-machines.md)

[仮想マシンの可用性管理](../articles/manage-availability-virtual-machines.md)

[Virtual Network の構成タスク](../documentation/services/virtual-machines/)

仮想マシンを作成したら、サービスやワークロードがデータを格納するための場所として、データ ディスクを追加することをお勧めします。次を参照してください。

[データ ディスクを Linux 仮想マシンに接続する方法](../articles/virtual-machines/virtual-machines-linux-how-to-attach-disk.md)

[データ ディスクを Windows 仮想マシンに接続する方法](../articles/virtual-machines/storage-windows-attach-disk.md)

<!---HONumber=Oct15_HO3-->