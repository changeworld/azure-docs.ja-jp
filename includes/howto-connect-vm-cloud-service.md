
仮想マシンは常にクラウド サービス内に配置されます。クラウド サービスはコンテナーとして機能し、インターネット経由で仮想マシンにアクセスするために、一意のパブリック DNS 名、パブリック IP アドレス、一連のエンドポイントを提供します。クラウド サービスは必要に応じて仮想ネットワーク内に含めることができます。

クラウド サービスが仮想ネットワーク内に含まれていない場合は、*スタンドアロン* クラウド サービスといいます。そのクラウド サービス内にある仮想マシンが他の仮想マシンと通信するには、他の仮想マシンのパブリック DNS 名を使用する必要があり、そのトラフィックはインターネットを介して送信されます。クラウド サービスが仮想ネットワークにある場合、そのクラウド サービス内にある仮想マシンは、トラフィックをインターネットに送信することなく、仮想ネットワーク内の他のすべての仮想マシンと通信できます。

同じスタンドアロン クラウド サービスに仮想マシンを配置する場合は、負荷分散と可用性セットを引き続き使用できます。詳細については、[仮想マシンの負荷分散](../articles/load-balance-virtual-machines.md)に関するページと、「[仮想マシンの可用性管理](../articles/manage-availability-virtual-machines.md)」を参照してください。ただし、仮想マシンをサブネットでまとめることや、スタンドアロン クラウド サービスをオンプレミスのネットワークに接続することはできません。たとえば次のようになります。

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
仮想マシンを仮想ネットワークに配置する場合、負荷分散と可用性セットに使用するクラウド サービスの数を決めることができます。さらに、オンプレミスのネットワークと同じように仮想マシンをサブネットにまとめ、仮想ネットワークをオンプレミスのネットワークに接続することもできます。たとえば次のようになります。

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

仮想ネットワークは、Azure で仮想マシンを接続するためのお勧めの方法です。また、アプリケーションの各階層を別々のクラウド サービスで構成することもお勧めします。これにより、ロール ベースのアクセス制御 (RBAC) による高度なユーザー権利の委任が可能になります。詳細については、「[Azure プレビュー ポータルでのロールベースのアクセス制御](../articles/role-based-access-control-configure.md)」を参照してください。ただし、各サブスクリプションで割り当てられるクラウド サービスの最大数である 200 を超えないように、異なるアプリケーション層にあるいくつかの仮想マシンを同じクラウド サービスにまとめることが必要になる場合があります。

## 仮想ネットワークでの VM の接続

仮想マシンを仮想ネットワークで接続する方法:

1.	Azure 管理ポータルで仮想ネットワークを作成します。詳細については、「[仮想ネットワークの構成タスク](https://msdn.microsoft.com/library/azure/jj156206.aspx)」を参照してください。
2.	Azure 管理ポータルでデプロイの一連のクラウド サービスを作成し、**[新規]、[Compute]、[クラウド サービス]、[カスタム作成]** の順にクリックして可用性セットと負荷分散の設計を反映します。
3.	新しい仮想マシンをそれぞれ作成するときには、正しいクラウド サービスと仮想ネットワークを指定します。仮想ネットワークに既に関連付けられているクラウド サービスがある場合、その名前が既定で選択されます。

以下は Azure 管理ポータルの使用例です。

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## スタンドアロン クラウド サービスでの VM の接続
 
仮想マシンをスタンドアロン クラウド サービスで接続するには以下の手順に従います。

1.	Azure 管理ポータルでデプロイするクラウド サービスを作成します。**[新規]、[Compute]、[クラウド サービス]、[カスタム作成]** の順にクリックします。
2.	仮想マシンを作成するときは、前の手順で作成したクラウド サービス名を選択します。また、仮想マシンを初めて作成するときに、デプロイするクラウド サービスを作成することもできます。

以下は、Azure 管理ポータルで EndpointTest という名前の既存のクラウド サービスを使用する例です。
 
![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##リソース
[仮想マシンの負荷分散](../articles/load-balance-virtual-machines.md)

[仮想マシンの可用性管理](../articles/manage-availability-virtual-machines.md)

[仮想ネットワークの構成タスク](https://msdn.microsoft.com/library/azure/jj156206.aspx)

仮想マシンを作成したら、サービスやワークロードがデータを格納するための場所として、データ ディスクを追加することをお勧めします。次を参照してください。

[データ ディスクを Linux 仮想マシンに接続する方法](../articles/virtual-machines/virtual-machines-linux-how-to-attach-disk.md)

[データ ディスクを Windows 仮想マシンに接続する方法](../articles/virtual-machines/storage-windows-attach-disk.md)

<!---HONumber=62-->