
仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。

> [AZURE.NOTE]Azure の仮想マシンでは、オペレーティング システム ディスク、ローカル一時ディスク、オプションのデータ ディスクなど、さまざまな種類のディスクが使用されます。仮想マシンのデータを格納するうえで、データ ディスクはお勧めの手段です。詳細については、「[About Disks and VHDs for Virtual Machines (Virtual Machines 用のディスクと VHD について)](../../virtual-machines-disks-vhds.md)」を参照してください。仮想マシンを削除しない限り、オペレーティング システム ディスクをデタッチすることはできません。

## ディスクの特定

仮想マシンからディスクを切断するには、まず切断するディスクの識別子である LUN 番号を確認する必要があります。そのためには、次の手順に従います。

1. 	Mac、Linux、および Windows の Azure CLI を開き、Azure サブスクリプションに接続します。詳細については、「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../articles/xplat-cli-connect.md)」をご覧ください。

2.  「`azure config
 	mode asm`」と入力して既定の Azure サービス管理モードであることを確認します。

3. 	次のように `azure vm disk list
	<virtual-machine-name>` を使用して仮想マシンに接続されているディスクを確認します。

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

4. 	切断するディスクの LUN (**論理ユニット番号**) を記録しておきます。


## ディスクの切断

ディスクの LUN 番号がわかれば、そのディスクを切断できます。

1. 	次のように `azure vm disk detach
 	<virtual-machine-name> <LUN>` コマンドを実行して仮想マシンから選択したディスクを切断します。

		$azure vm disk detach ubuntuVMasm 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	ディスクが切断されたかどうかは、次のコマンドを実行して確認できます。

		$azure vm disk list ubuntuVMasm
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

切断したディスクはストレージに残りますが、仮想マシンには接続されなくなっています。

<!---HONumber=August15_HO7-->