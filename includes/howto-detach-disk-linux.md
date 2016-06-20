仮想マシン (VM) に接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。そうすれば、ディスクは VM から削除されますが、ストレージからは削除されません。再びディスク上の既存のデータを使用する場合は、同じ VM や別の仮想マシンに再接続できます。

> [AZURE.NOTE] Azure の VM では、オペレーティング システム ディスク、ローカル一時ディスク、オプションのデータ ディスクなど、さまざまな種類のディスクが使用されます。詳細については、「[About Disks and VHDs for Virtual Machines (Virtual Machines 用のディスクと VHD について)](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md)」を参照してください。VM を削除しない限り、オペレーティング システム ディスクをデタッチすることはできません。

## ディスクの特定

VM からディスクを切断するには、まず切断するディスクの識別子である LUN 番号を確認する必要があります。そのためには、次の手順に従います。

1. 	Azure CLI を開いて、[Azure サブスクリプションに接続](../articles/xplat-cli-connect.md)します。Azure サービス管理モード (`azure config mode asm`) であることを確認します。

2. 	`azure vm disk list
	<virtual-machine-name>` を使用して、VM に接続されているディスクを確認します。

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		data:    0    30        ubuntuVM-76f7ee1ef0f6dddc.vhd
		info:    vm disk list command OK

3. 	切断するディスクの LUN (**論理ユニット番号**) を記録しておきます。


## ディスクの切断

ディスクの LUN 番号がわかれば、そのディスクを切断できます。

1. 	`azure vm disk detach
 	<virtual-machine-name> <LUN>` コマンドを実行して、選択したディスクを仮想マシンから切断します。

		$azure vm disk detach UbuntuVM 0
		info:    Executing command vm disk detach
		+ Getting virtual machines
		+ Removing Data-Disk
		info:    vm disk detach command OK

2. 	ディスクが切断されたかどうかは、次のコマンドを実行して確認できます。

		$azure vm disk list UbuntuVM
		info:    Executing command vm disk list
		+ Fetching disk images
		+ Getting virtual machines
		+ Getting VM disks
		data:    Lun  Size(GB)  Blob-Name                         OS
		data:    ---  --------  --------------------------------  -----
		data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
		data:    1    10        test.VHD
		info:    vm disk list command OK

切断したディスクはストレージに残りますが、仮想マシンには接続されなくなっています。

<!---HONumber=AcomDC_0608_2016-->