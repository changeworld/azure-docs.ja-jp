<properties title="Creating an Oracle WebLogic Server 12c and Oracle Database 12c Virtual Machine in Azure" pageTitle="Azure での Oracle WebLogic Server 12c および Oracle Database 12c の仮想マシンの作成" description="Windows Server 2012 で実行中の Oracle WebLogic Server 12c および Oracle Database 12c のイメージを Microsoft Azure で作成する例の手順を実行します。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Azure での Oracle WebLogic Server 12c および Oracle Database 12c の仮想マシンの作成
次の例は、Windows Server 2012 で実行しているマイクロソフト提供の WebLogic Server 12c および Oracle Database 12c のイメージに基づいて、Azure で仮想マシンを作成する方法を示しています。

##Azure で Oracle WebLogic Server 12c および Oracle Database 12c の仮想マシンを作成するには

1. [Azure ポータル](https://ms.portal.azure.com/)にログインします。

2.	**[Marketplace]**、**[Compute]** をクリックし、続いて検索ボックスに**「Oracle」**と入力します。

3.	**[Windows Server 2012 での Oracle Database 12c および WebLogic Server 12c Standard Edition]** または **[Windows Server 2012 での Oracle Database 12c および WebLogic Server 12c Enterprise Edition]** のイメージを選択します。このイメージに関する情報 (最小の推奨サイズなど) を確認し、**[次へ]** をクリックします。

4.	VM の**ホスト名**を指定します。

5.	VM の**ユーザー名**を指定します。なお、このユーザーは VM にリモートログインするためのもので、Oracle データベースのユーザー名ではありませんのでご注意ください。

6.	VM のパスワードを指定し確認するか、または SSH 公開キーを入力します。

7.	**[価格レベル]** を選択。既定では推奨される価格レベルが表示されます。すべての構成オプションを見るには、右上の [すべて表示] をクリックします。

8. 必要に応じて [オプションの構成] を設定します。このとき、次に注意してください。

	1. VM 名で新しいストレージ アカウントを作成するには、**[ストレージ アカウント]** をそのままにします。

	2. **[可用性セット]** を「未構成」のままにします。

	3. この時点では**エンドポイント**を追加しないでください。

9.	[[リソース グループ]](resource-group-portal.md) を選択または作成

10. **[サブスクリプション]** を選択

11. **[場所]** を選択


##この仮想マシンでホストされているデータベースを作成するには
**「Oracle Database 12c 仮想マシンを使用したデータベースを Azure で作成するには」**のセクションで始まる[『Creating an Oracle Database 12c Virtual Machine in Azure (Azure での Oracle Database 12c 仮想マシンの作成)』](virtual-machines-creating-oracle-database-virtual-machine.md)の手順に従います。

##この仮想マシンでホストされている Oracle WebLogic Server 12c を構成するには
**「Oracle WebLogic Server 12c 仮想マシンを Azure で構成するには」**のセクションで始まる[『Creating an Oracle WebLogic Server 12c Virtual Machine in Azure (Azure での Oracle WebLogic Server 12c 仮想マシンの作成)』](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md)の手順に従います。WebLogic Server クラスターを設定する場合、[『Creating an Oracle WebLogic Server 12c cluster in Azure (Azure での Oracle WebLogic Server 12c クラスターの作成)』](virtual-machines-creating-oracle-webLogic-server-12c-cluster.md)も参照してください。

##その他のリソース
[Oracle 仮想マシン イメージ - 他の考慮事項](miscellaneous-considerations-for-oracle-virtual-machine-images-new-article.md)

[Azure の Oracle 仮想マシン イメージ](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

[Java アプリケーションから Oracle Database に接続する](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Microsoft Azure で Linux を使用する Oracle WebLogic Server 12c](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 日間 DBA 12c リリース 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=July15_HO2-->