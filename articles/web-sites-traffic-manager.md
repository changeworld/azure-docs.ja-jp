<properties 
	pageTitle="Azure Traffic Manager による Azure Websites のトラフィックの制御" 
	description="この記事では、Azure Websites に関連して、Azure Traffic Manager の概要情報を提供します。" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>

# Azure Traffic Manager による Azure Websites のトラフィックの制御

> [AZURE.NOTE] この記事では、Azure Websites に関連して、Microsoft Azure Traffic Manager の概要情報を提供します。Azure Traffic Manager 自体の詳細については、この記事の最後にある関連情報をご覧ください。

## はじめに
Azure Traffic Manager を使用すると、Web クライアントからの要求を Azure Websites に振り分ける方法を制御できます。Azure Websites のエンドポイントが Azure Traffic Manager のプロファイルに追加されると、Azure Traffic Manager は Web サイトの状態 (実行中、停止、または削除済み) を追跡して、トラフィックを受信する必要のあるエンドポイントを決定できるようになります。

## 負荷分散方法
Azure Traffic Manager は 3 つの異なる負荷分散方法を使用します。これらの方法について、Azure Websites に関連して、次の一覧で説明します。 

* **フェールオーバー**: Web サイトの複製がさまざまなリージョンにある場合、この方法では、すべての Web クライアント トラフィックを処理するように、1 つの Web サイトを構成でき、最初の Web サイトが利用不可になったときにトラフィックを処理するように、異なるリージョン内の別の Web サイトを構成できます。 
	
* **ラウンド ロビン**: Web サイトの複製がさまざまなリージョンにある場合、この方法では、異なるリージョン内の Web サイト間でトラフィックを均等に振り分けることができます。 
	
* **パフォーマンス**: この方法では、クライアントへの最短の往復時間に基づいてトラフィックを振り分けます。この方法は同じリージョン内または異なるリージョン内の Web サイトに使用できます。 

Azure Traffic Manager での負荷分散の詳細については、[Traffic Manager での負荷分散方法について](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx) をご覧ください。

##Azure Websites と Traffic Manager のプロファイル 
Web サイトのトラフィックを制御するように構成するには、Azure Traffic Manager でプロファイルを作成し、前に説明している 3 つの負荷分散方法のいずれかをプロファイルで指定します。その後、トラフィックを制御するエンドポイント (この場合は Web サイト) をプロファイルに追加します。Web サイトの状態 (実行中、停止、または削除済み) は定期的にプロファイルに反映されて、その状態に応じて Azure Traffic Manager はトラフィックを振り分けることができます。

Azure Traffic Manager を Azure で使用する場合は、次の点に留意してください。

* 同じリージョンで Azure Websites のみを展開する場合、Azure Websites はそのモードには関係なく、フェールオーバーとラウンド ロビンの機能を既に備えています。

* 同じリージョンでの Azure Websites の展開で、Azure の別のクラウド サービスと連携させる場合、両方の種類のエンドポイントを組み合わせたハイブリッドのシナリオが可能です。

* リージョンごとに 1 つのみの Web サイト エンドポイントをプロファイルで指定することもできます。1 つのリージョンのエンドポイントとして Web サイトを選択すると、そのリージョン内の残りの Web サイトはそのプロファイルで選択できなくなります。

* Azure Traffic Manager のプロファイルで指定した Web サイト エンドポイントは、プロファイルで Web サイトの構成ページの **[ドメイン名]** セクションに表示されますが、そこでは構成できません。

* Web サイトをプロファイルに追加した後、Web サイトのポータル ページのダッシュボードの **[サイトの URL]** には、Web サイトのカスタム ドメインを設定していればその URL が表示されます。それ以外の場合は、Traffic Manager のプロファイルの URL ( `contoso.trafficmgr.com` など) が表示されます。Web サイトの直接のドメイン名と Traffic Manager の URL の両方が、Web サイトの構成ページの **[ドメイン名]** セクションに表示されます。

* カスタム ドメイン名は予期したとおりに機能しますが、それらのドメイン名を Web サイトに追加するだけでなく、Traffic Manager の URL を参照するように DNS マップを構成する必要もあります。Azure Websites のカスタム ドメイン名の設定については、[Azure Websites のカスタム ドメインの構成](https://www.windowsazure.com/ja-jp/documentation/articles/web-sites-custom-domain-name/) をご覧ください。

* 標準モードの Web サイトのみを Azure Traffic Manager のプロファイルに追加できます。

## 次のステップ

Azure Traffic Manager の概念と技術的概要については、[Traffic Manager Overview (Traffic Manager の概要)](http://msdn.microsoft.com/library/windowsazure/hh744833.aspx) をご覧ください。 

Azure Websites 用など、Azure Traffic Manager の構成方法については、[Traffic Manager の構成タスク](http://msdn.microsoft.com/library/windowsazure/hh744830.aspx) をご覧ください。

Azure Traffic Manager での負荷分散の詳細については、[Traffic Manager での負荷分散方法について](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx) をご覧ください。

Azure Websites での Traffic Manager の使用に関する詳細は、ブログの投稿 
[Using Azure Traffic Manager with Azure Web Sites (Azure Web サイトでの Azure Traffic Manager の使用)](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) と [Azure Traffic Manager can now integrate with Azure Web sites (Azure Traffic Manager と Azure Web サイトの統合が可能に)](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/) をご覧ください。


<!--HONumber=42-->
