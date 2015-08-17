<properties
	pageTitle="CLI を使った Linux を実行する仮想マシンのイメージのキャプチャ"
	description="Linux を実行する Azure の仮想マシン (VM) のイメージをキャプチャする方法について説明します。"
	services="virtual-machines"
	documentationCenter=""
	authors="karthmut"
	manager="madhana"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="karthmut"/>




# テンプレートとして使用するために CLI を使って Linux 仮想マシンをキャプチャする方法##



ここでは、Linux を実行する Azure 仮想マシンをキャプチャして、他の仮想マシンを作成する際のテンプレートのように使用する方法を示します。このテンプレートには、仮想マシンに接続された OS ディスクやデータ ディスクが含まれます。ネットワーク構成は含まれないため、テンプレートを使用する他の仮想マシンを作成するときは、ネットワーク構成を行う必要があります。



Azure ではこのテンプレートをイメージとして扱い、イメージ リストに格納します。アップロードしたすべてのイメージがこの場所に格納されます。イメージの詳細については、「[Azure の仮想マシン イメージについて][]」を参照してください。



##開始する前に##



これらの手順は、すでに Azure 仮想マシンを作成し、データ ディスクの接続を含め、オペレーティング システムの構成が完了していることを前提としています。まだ完了していない場合は、下記の手順を確認してください。



- [カスタム仮想マシンの作成方法][]

- [データ ディスクを仮想マシンに接続する方法][]



##仮想マシンをキャプチャする##



1. 仮想マシンへの接続詳細については、「[Linux を実行する仮想マシンにログオンする方法][]」を参照してください。



2. 仮想マシンは、状態が **StoppedDeallocated** の場合にのみキャプチャできます。SSH のウィンドウで、次のコマンドを入力し、仮想マシンをシャットダウンします。



        vm shutdown [options] <vm-name>



    `vm shutdown` のオプションには `-p` がありますが、このオプションでは、シャットダウンの際もコンピューティング リソースは保持されます。仮想マシンをキャプチャするにはプロビジョニングを解除する必要があるため、このオプションは**有効にしないでください**。



3. 次のコマンドを実行して、仮想マシン イメージをキャプチャします。



        vm capture <vm-name> <target-image-name> --deleted



    イメージをキャプチャするには仮想マシンを削除する必要があります。そのためには、`--deleted` または `-t` を使用できます。



4. VM イメージ リストを確認すれば、イメージがキャプチャされていることを確認できます。



        vm image list | grep <target-image-name>



    `| grep <target-image-name>` 部は任意ですが、リストでイメージを探すときに役立ちます。



ターミナル出力をはじめ、仮想マシンのキャプチャの例を次に示します。


    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status            Location  DNS Name                  IP Address

    data:    -----------  ----------------  --------  ------------------------  ------------

    data:    kmorig       RoleStateUnknown  West US   kmorig.cloudapp.net       100.92.56.16

    info:    vm list command OK

    ~$ azure vm shutdown kmorig

    info:    Executing command vm shutdown

    + Getting virtual machines

    + Shutting down VM

    info:    vm shutdown command OK

    ~$ azure vm list

    info:    Executing command vm list

    + Getting virtual machines

    data:    Name         Status              Location  DNS Name                  IP Address

    data:    -----------  ------------------  --------  ------------------------  ----------

    data:    kmorig       StoppedDeallocated  West US   kmorig.cloudapp.net

    info:    vm list command OK

    ~$ azure vm capture kmorig kmcaptured --deleted

    info:    Executing command vm capture

    + Getting virtual machines

    + Checking image with name kmcaptured exists

    + Capturing VM

    info:    vm capture command OK

    ~$ azure vm image list | grep kmcaptured

    data:    kmcaptured



詳細やその他のコマンドについては、「[Azure CLI ドキュメントのページ][]」を参照してください。


[Azure CLI ドキュメントのページ]: ../virtual-machines-command-line-tools.md

[Linux を実行する仮想マシンにログオンする方法]: virtual-machines-linux-how-to-log-on.md

[Azure の仮想マシン イメージについて]: http://msdn.microsoft.com/library/azure/dn790290.aspx

[カスタム仮想マシンの作成方法]: virtual-machines-create-custom.md

[データ ディスクを仮想マシンに接続する方法]: storage-windows-attach-disk.md
 

<!---HONumber=August15_HO6-->