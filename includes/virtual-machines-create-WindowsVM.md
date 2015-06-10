1. [Azure の管理ポータル](http://manage.windowsazure.com)にサインインします。まだサブスクリプションをお持ちでない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)をお試しください。

2. ウィンドウの下部にあるコマンド バーで、**[新規]** をクリックします。

3. **[コンピューティング]** で、**[仮想マシン]**、**[ギャラリーから]** の順にクリックします。

	![Navigate to From Gallery in the Command Bar](./media/virtual-machines-create-WindowsVM/fromgallery.png)

4. 最初の画面の **[イメージの選択]** で、イメージ ギャラリーの一覧の 1 つから仮想マシンのイメージを選択できます(利用できるイメージは使用しているサブスクリプションによって異なる場合があります)。 矢印をクリックして続行します。

	![Choose an image](./media/virtual-machines-create-WindowsVM/chooseimage.png)

5. 2 番目の画面では、コンピューターの名前、サイズ、管理ユーザーの名前とパスワードを選択します。Azure の仮想マシンを試用する場合には、下の画像に示しているフィールドに入力してください。それ以外の場合は、アプリケーションやワークロードの実行に必要な階層とサイズを選択してください。次に、フィールドの入力内容について補足説明します。

	- **[新しいユーザー名]** とは、サーバーの管理に使用する管理アカウントの名前です。このアカウント用の一意のパスワードを作成し、忘れないように記憶してください。**仮想マシンにログオンする際に、このユーザー名とパスワードが必要になります。**

	- 仮想マシンのサイズによって、使用料金が変わります。また、接続できるデータ ディスク数などの構成オプションも料金に影響します。詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](http://go.microsoft.com/fwlink/p/?LinkId=466520)」を参照してください。

	![Configure the properties of the virtual machine](./media/virtual-machines-create-WindowsVM/vmconfiguration.png)



6. 3 番目の画面では、リソースのネットワーク、ストレージ、可用性を構成できます。次に、フィールドの入力内容について補足説明します。


	- **[クラウド サービス DNS 名]** は、仮想マシンへの接続に使用する URI の一部となるグルーバル DNS 名です。クラウド サービス名は Azure 上で一意な名前にする必要があるため、独自のクラウド サービス名を考えてください。クラウド サービスは、[複数の仮想マシン](../articles/cloud-services-connect-virtual-machine.md)を使用するシナリオで重要となります。

	- **[リージョン/アフィニティ グループ/仮想ネットワーク]** では、現在の場所に対応するリージョンを指定します。代わりに仮想ネットワークを指定することもできます。

	>[AZURE.NOTE]仮想マシンで仮想ネットワークを使用する場合、仮想マシンの作成時に仮想ネットワークを指定する**必要があります**。仮想マシンの作成後に VM を仮想ネットワークに参加させることはできません。詳細については、「[仮想ネットワークの概要](http://go.microsoft.com/fwlink/p/?LinkID=294063)」を参照してください。

	- エンドポイントの構成の詳細については、「[仮想マシンに対してエンドポイントを設定する方法](../articles/virtual-machines-set-up-endpoints-classic-portal.md)」を参照してください。

	![Configure the connected resources of the virtual machine](./media/virtual-machines-create-WindowsVM/resourceconfiguration.png)

7. 4 番目の構成画面では、VM エージェントと、使用可能な一部の拡張機能を構成できます。チェック マークをクリックして、仮想マシンを作成します。


	![Configure VM Agent and extensions for the virtual machine](./media/virtual-machines-create-WindowsVM/agent-and-extensions.png)

	>[AZURE.NOTE]VM エージェントには、仮想マシンの操作と管理に役立つ拡張機能をインストールするための環境が用意されています。詳細については、[拡張機能の使用に関するページ](http://go.microsoft.com/FWLink/p/?LinkID=390493)を参照してください。

8. 仮想マシンが作成されると、管理ポータルの **[仮想マシン]** に新しい仮想マシンが表示されます。対応するクラウド サービスとストレージ アカウントも作成され、それぞれのセクションに表示されます。仮想マシンとクラウド サービスが自動的に起動し、その状態がいずれも "**実行中**" として表示されます。

	![Configure VM Agent and the endpoints of the virtual machine](./media/virtual-machines-create-WindowsVM/vmcreated.png)

<!---HONumber=58-->