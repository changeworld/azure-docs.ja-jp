
ディスクの詳細については、[About Disks and VHDs for Virtual Machines (Virtual Machines 用のディスクと VHD について)](../articles/virtual-machines-disks-vhds.md) を参照してください。

<a id="attachempty"></a>
## 方法: 空のディスクの接続
空のディスクを接続すると、Azure が .vhd ファイルを作成してそれをストレージ アカウントに保存するため、データ ディスクを比較的簡単に追加できます。

1.  Mac、Linux、および Windows の Azure CLI を開き、Azure サブスクリプションに接続します。詳細については、「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../articles/xplat-cli-connect.md)」をご覧ください。

2.  「`azure config
 	mode asm`」と入力して既定の Azure サービス管理モードであることを確認します。

3.  `azure vm disk attach-new` コマンドを使用して、次のように新しいディスクを作成して接続します。_ubuntuVMasm_ はサブスクリプションで作成した Linux 仮想マシンの名前に置き換えることに注意してください。この例では、30 は GB 単位でのディスクのサイズです。

        azure vm disk attach-new ubuntuVMasm 30

4.	データ ディスクが作成されて接続されると、次のように `azure vm disk list
    <virtual-machine-name>` の出力に表示されます。

        $ azure vm disk list ubuntuVMasm
        info:    Executing command vm disk list
        + Fetching disk images
        + Getting virtual machines
        + Getting VM disks
        data:    Lun  Size(GB)  Blob-Name                         OS
        data:    ---  --------  --------------------------------  -----
        data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
        data:    0    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
        info:    vm disk list command OK

<a id="attachexisting"></a>
## 方法: 既存のディスクの接続

既存のディスクを接続する場合は、ストレージ アカウントで利用できる .vhd を持っている必要があります。

1. 	Mac、Linux、および Windows の Azure CLI を開き、Azure サブスクリプションに接続します。詳細については、「[Azure コマンド ライン インターフェイス (Azure CLI) からの Azure サブスクリプションへの接続](../articles/xplat-cli-connect.md)」をご覧ください。

2.  既定の Azure サービス管理モードであることを確認します。リソース管理モードに変更している場合は、「`azure config mode asm`」と入力して戻します。

3.	接続する VHD が Azure サブスクリプションに既にアップロードされているかどうか、以下を使用して確認します。

        $azure vm disk list
    	info:    Executing command vm disk list
    	+ Fetching disk images
    	data:    Name                                          OS
    	data:    --------------------------------------------  -----
    	data:    myTestVhd                                     Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060029150744  Linux
    	data:    ubuntuVMasm-ubuntuVMasm-0-201508060040530369
    	info:    vm disk list command OK

4.  使用するディスクが見つからない場合は、`azure vm disk create` または `azure vm disk upload` を使用してローカル VHD をサブスクリプションにアップロードできます。次に例を示します。

        $azure vm disk create myTestVhd2 .\TempDisk\test.VHD -l "East US" -o Linux
		info:    Executing command vm disk create
		+ Retrieving storage accounts
		info:    VHD size : 10 GB
		info:    Uploading 10485760.5 KB
		Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
		info:    Finishing computing MD5 hash, 16% is complete.
		info:    https://portalvhdsq1s6mc7mqf4gn.blob.core.windows.net/disks/test.VHD was
		uploaded successfully
		info:    vm disk create command OK

	`azure vm disk upload` コマンドを使用して特定のストレージ アカウントに VHD をアップロードすることもできます。Azure Virtual Machine データ ディスクの管理用コマンドの詳細については、[こちら](../virtual-machines-command-line-tools.md#commands-to-manage-your-azure-virtual-machine-data-disks)を参照してください。

5.  次のコマンドを入力して、アップロードした VHD を仮想マシンに接続します。

		$azure vm disk attach ubuntuVMasm myTestVhd
		info:    Executing command vm disk attach
		+ Getting virtual machines
		+ Adding Data-Disk
		info:    vm disk attach command OK

	忘れずに、_ubuntuVMasm_ は仮想マシンの名前に、_myTestVhd_ は目的の VHD に置き換えてください。

6.	ディスクが仮想マシンに接続されたかどうかは、次のように `azure vm disk list
 	<virtual-machine-name>` コマンドで確認できます。

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


> [AZURE.NOTE]データ ディスクを追加した後、仮想マシンがストレージとしてそのディスクを使用できるように、仮想マシンにログオンしてディスクを初期化する必要があります。

<!---HONumber=August15_HO7-->