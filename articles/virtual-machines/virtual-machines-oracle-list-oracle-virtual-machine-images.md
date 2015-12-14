<properties" pageTitle="title="Oracle VM イメージの一覧 | Microsoft Azure" description="Azure Gallery の Oracle イメージの一覧を取得し、それに基づいて Oracle 仮想マシンを作成する方法を習得します。" services="virtual-machines" documentationCenter="" authors="bbenz" manager="" editor="" tags="azure-service-management, azure-resource-manager"/>

<tags
ms.service="virtual-machines"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="vm-multiple"
ms.workload="infrastructure-services"
ms.date="06/22/2015"
ms.author="bbenz" />

#Oracle 仮想マシン イメージの一覧

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


Oracle イメージに基づく Virtual Machines を作成するには、[Azure ポータル](https://ms.portal.azure.com/)にサインインして **[Marketplace]** をクリック後、**[Compute]** をクリックし、検索ボックスに「**Oracle**」と入力します。イメージを選択し、手順に従って Microsoft Azure でイメージを設定します。[Azure ポータル](https://ms.portal.azure.com/)では、Microsoft による Oracle イメージは Windows で、Oracle による Oracle イメージは Oracle Linux で実行されることに注意してください。

![](media/virtual-machines-oracle-list-oracle-virtual-machine-images/image1.png)

##Windows ベースの仮想マシン イメージ
Azure の Windows Server 上で実行される、利用可能な Oracle 仮想マシン イメージの一覧を次に示します。これらのイメージは従量課金であり、Oracle のライセンス料にはこれらのイメージの使用が含まれています。独自のライセンスを持ち込んで、Windows または Linux 上で Oracle ソフトウェアを実行することもできます。Azure Virtual Machines の料金体系、ライセンス、仮想マシンのギャラリー イメージの詳細は[ここ](http://azure.microsoft.com/pricing/details/virtual-machines/#oracle-software)にあります。特定の Oracle の詳細価格を参照するには、**[Oracle]** タブをクリックします。

###Oracle データベース仮想マシン イメージ
- Oracle Database 12c Enterprise Edition on Windows Server 2012
- Oracle Database 12c Standard Edition on Windows Server 2012
- 基本的なオプションの Oracle Database 12c
- 詳細オプションの Oracle Database 12c
- Oracle Database 11g R2 Enterprise Edition on Windows Server 2008 R2
- Oracle Database 11g R2 Standard Edition on Windows Server 2008 R2
- 基本的なオプションのOracle Database 11g R2 EE
- 詳細オプションのOracle Database 11g R2 EE  

###Oracle WebLogic Server 仮想マシン イメージ
- Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012
- Oracle WebLogic Server 12c Standard Edition on Windows Server 2012
- Oracle WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2
- Oracle WebLogic Server 11g Standard Edition on Windows Server 2008 R2  

###Oracle データベースおよび Oracle WebLogic Server の仮想マシン イメージ  
- Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012
- Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012
- Oracle Database 11g and WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2
- Oracle Database 11g and WebLogic Server 11g Standard Edition on Windows Server 2008 R2

### Java 仮想マシン イメージ
-	Windows Server 2012 R2 の JDK 8
-	Windows Server 2012 R2 の JDK 7
-	Windows Server 2012 の JDK 6


##Oracle Linux 仮想マシン イメージ
Azure の Oracle Linux 上で実行される、事前に構成された利用可能な Oracle 仮想マシン イメージの一覧を次に示します。Oracle のライセンス料には、これらの事前構成された仮想マシン イメージの使用は含まれていません。これらのイメージについて、独自に所有するライセンスを使用しなければなりません。独自のライセンスを持ち込んで、Windows または Linux のカスタム Virtual Machines に Oracle ソフトウェアをインストールして実行することもできます。[Azure での Oracle ライセンス](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html#support)の詳細を以下に説明します。また、[独自のイメージ](virtual-machines-create-upload-vhd-windows-server.md)を使用した仮想マシン作成の詳細についても説明します。これについて、また Oracle や他のワークロードを Azure へ移行する別の方法については、「[Windows ベースの仮想マシンを作成するさまざまな方法](virtual-machines-windows-choices-create-vm.md)」を参照してください。

- Oracle Database 12c Enterprise Edition on Oracle Linux
- Oracle Database 12c Standard Edition on Oracle Linux
- Oracle WebLogic Server 12c Enterprise Edition on Oracle Linux
- Oracle Linux 6.4.0.0.0
- Oracle Linux 7.0.0.0.0

##その他のリソース
[Azure Marketplace の新しい一体型の Oracle イメージ](https://msopentech.com/blog/2015/02/19/new-one-oracle-images-azure-marketplace/)

[Oracle 仮想マシン イメージ - 他の考慮事項](#miscellaneous-considerations-for-oracle-virtual-machine-images-new-article)

<!---HONumber=AcomDC_1203_2015-->