

![Virtual machines in a standalone cloud service](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

仮想マシンを仮想ネットワークに配置する場合、負荷分散と可用性セットに使用するクラウド サービスの数を決めることができます。 さらに、オンプレミスのネットワークと同じように仮想マシンをサブネットにまとめ、仮想ネットワークをオンプレミスのネットワークに接続することもできます。 次に例を示します。

![Virtual machines in a virtual network](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

仮想ネットワークは、Azure で仮想マシンを接続するためのお勧めの方法です。 また、アプリケーションの各階層を別々のクラウド サービスで構成することもお勧めします。 ただし、各サブスクリプションで割り当てられるクラウド サービスの最大数である 200 を超えないように、異なるアプリケーション層にあるいくつかの仮想マシンを同じクラウド サービスにまとめることが必要になる場合もあります。 これとその他の制限については、「 [Azure サブスクリプションとサービスの制限、クォータ、および制約](../articles/azure-subscription-service-limits.md)」をご覧ください。

## <a name="connect-vms-in-a-virtual-network"></a>仮想ネットワークでの VM の接続
仮想マシンを仮想ネットワークで接続する方法:

1. [Azure ポータル](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md)で仮想ネットワークを作成し、[クラシック デプロイ] を指定します。
2. デプロイメントで可用性セットと負荷分散用の設計を反映させるため、クラウド サービスのセットを作成します。 Azure Portal で、各クラウド サービスについて **[リソースの作成]、[Compute]、[クラウド サービス]** の順にクリックします。

  クラウド サービスの詳細を入力するときは、仮想ネットワークで使用されるものと同じ "_リソース グループ_" を選択します。

3. 新しい仮想マシンを作成するには、**[リソースの作成]、[Compute]** の順にクリックした後、**[おすすめアプリ]** から適切な VM イメージを選択します。

  VM の **[基本]** ブレードで、仮想ネットワークで使用されるものと同じ "_リソース グループ_" を選択します。

  ![VNet を使用する場合の VM の [基本] ブレード](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. VM の**設定**を入力するときは、VM に適した "_クラウド サービス_" または "_仮想ネットワーク_" を選択します。

  その他の項目は、選択内容に基づいて、Azure が選択します。

  ![VNet を使用する場合の VM の [設定] ブレード](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>スタンドアロン クラウド サービスでの VM の接続
仮想マシンをスタンドアロンのクラウド サービスで接続するには:

1. [Azure ポータル](http://portal.azure.com)でクラウド サービスを作成します。 **[新規]、[コンピューティング]、[クラウド サービス]** の順にクリックします。 また、仮想マシンを初めて作成するときに、デプロイメント用のクラウド サービスを作成することもできます。
2. 仮想マシンを作成するときは、クラウド サービスで使用されるものと同じリソース グループを選択します。

  ![仮想マシンを既存のクラウド サービスに追加する](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  VM の詳細を入力するときは、最初の手順で作成したクラウド サービスの名前を選択します。

  ![Selecting a cloud service for a virtual machine](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
