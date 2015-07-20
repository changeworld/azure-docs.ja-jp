
#CLI を使用してデータ ディスクを仮想マシンから切断する方法

仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。

> [AZURE.NOTE]Azure の仮想マシンでは、オペレーティング システム ディスク、ローカル一時ディスク、オプションのデータ ディスクなど、さまざまな種類のディスクが使用されます。仮想マシンのデータを格納するうえで、データ ディスクはお勧めの手段です。ディスクの詳細については、「[Azure とは](http://go.microsoft.com/fwlink/p/?LinkId=263439)」を参照してください。現在、オペレーティング システム ディスクを切断することはできません。


1. VM に接続されているディスクの一覧を取得します。

        vm disk list <vm-name>

    `<vm-name>` を省略した場合、サブスクリプション内のすべてのディスクの一覧が表示されます。


2. ディスクを切断します。

        vm disk detach <vm-name> <lun>

    `lun` は、切断するディスクを示し、VM のディスクの一覧で番号として表示されます。

ターミナル出力を含む例を次に示します。

    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    data:    2    7         kmlinux-602362868dbb7439.vhd
    info:    vm disk list command OK
    ~$ azure vm disk detach kmlinux 2
    info:    Executing command vm disk detach
    + Getting virtual machines
    + Removing Data-Disk
    info:    vm disk detach command OK
    ~$ azure vm disk list kmlinux
    info:    Executing command vm disk list
    + Fetching disk images
    + Getting virtual machines
    + Getting VM disks
    data:    Lun  Size(GB)  Blob-Name                               OS
    data:    ---  --------  --------------------------------------  -----
    data:         30        kmlinux-kmlinux-2015-02-05.vhd          Linux
    data:    1    5         kmlinux-f8ef0006ab182209.vhd
    info:    vm disk list command OK

<!---HONumber=July15_HO2-->