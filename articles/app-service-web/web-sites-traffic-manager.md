<properties 
	pageTitle="Azure トラフィック マネージャーによる Azure の Web アプリのトラフィックの制御" 
	description="この記事では、Azure の Web アプリに関連して、Azure トラフィック マネージャーの概要情報を提供します。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Azure トラフィック マネージャーによる Azure の Web アプリのトラフィックの制御

> [AZURE.NOTE]この記事では、Azure App Service Web Apps に関連して、Microsoft Azure トラフィック マネージャーの概要情報を提供します。"Azure トラフィック マネージャー自体の詳細については、この記事の最後にある関連情報を参照してください。

## はじめに
Azure トラフィック マネージャーを使用すると、Web クライアントからの要求を Azure App Service の Web アプリに振り分ける方法を制御できます。Web アプリのエンドポイントが Azure トラフィック マネージャーのプロファイルに追加されると、Azure トラフィック マネージャーは Web アプリの状態 (実行中、停止、または削除済み) を追跡して、トラフィックを受信する必要のあるエンドポイントを決定できるようになります。

## 負荷分散方法
Azure トラフィック マネージャーは 3 つの異なる負荷分散方法を使用します。これらの方法について、Azure の Web アプリに関連して、次の一覧で説明します。

* **フェールオーバー**: Web アプリの複製がさまざまなリージョンにある場合、この方法では、すべての Web クライアント トラフィックを処理するように、1 つの Web アプリを構成でき、最初の Web アプリが利用不可になったときにトラフィックを処理するように、異なるリージョン内の別の Web アプリを構成できます。 
	
* **ラウンド ロビン**: Web アプリの複製がさまざまなリージョンにある場合、この方法では、異なるリージョン内の Web アプリ間でトラフィックを均等に振り分けることができます。
	
* **パフォーマンス**: この方法では、クライアントへの最短の往復時間に基づいてトラフィックを振り分けます。この方法は同じリージョン内または異なるリージョン内の Web アプリに使用できます。

Azure トラフィック マネージャーでの負荷分散の詳細については、「[Traffic Manager での負荷分散方法について](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx)」を参照してください。

##Web アプリとトラフィック マネージャーのプロファイル 
Web アプリのトラフィック制御を構成するには、Azure トラフィック マネージャーでプロファイルを作成し、前に説明している 3 つの負荷分散方法のいずれかをプロファイルで指定します。その後、トラフィックを制御するエンドポイント (この場合は Web アプリ) をプロファイルに追加します。Web アプリの状態 (実行中、停止、または削除済み) は定期的にプロファイルに反映されて、その状態に応じて Azure トラフィック マネージャーはトラフィックを振り分けることができます。

Azure トラフィック マネージャーを Azure で使用する場合は、次の点に留意してください。

* 同じリージョンでの Web アプリのみの展開の場合、Web アプリはそのモードには関係なく、フェールオーバーとラウンド ロビンの機能を既に備えています。

* 同じリージョンでの Web アプリのデプロイで、Azure の別のクラウド サービスと連携させる場合、両方の種類のエンドポイントを組み合わせたハイブリッドのシナリオが可能です。

* リージョンごとに 1 つのみの Web アプリ エンドポイントをプロファイルで指定することもできます。1 つのリージョンのエンドポイントとして Web アプリを選択すると、そのリージョン内の残りの Web アプリはそのプロファイルで選択できなくなります。

* Azure トラフィック マネージャーのプロファイルで指定した Web アプリ エンドポイントは、プロファイルで Web アプリの構成ページの **[ドメイン名]** セクションに表示されますが、そこでは構成できません。

* Web アプリをプロファイルに追加した後、Web アプリのポータル ページのダッシュボードの **[サイトの URL]** には、Web アプリのカスタム ドメインを設定していればその URL が表示されます。それ以外の場合は、Traffic Manager のプロファイルの URL (`contoso.trafficmgr.com` など) が表示されます。Web アプリの直接のドメイン名とトラフィック マネージャーの URL の両方が、Web アプリの構成ページの **[ドメイン名]** セクションに表示されます。

* カスタム ドメイン名は予期したとおりに機能しますが、それらのドメイン名を Web アプリに追加するだけでなく、トラフィック マネージャーの URL を参照するように DNS マップを構成する必要もあります。Azure Web アプリのカスタム ドメイン名の設定については、「[Azure Website のカスタム ドメインの構成](web-sites-custom-domain-name.md)」を参照してください。

* 標準モードの Web アプリのみを Azure トラフィック マネージャーのプロファイルに追加できます。

## 次のステップ

Azure トラフィック マネージャーの概念と技術的概要については、「[Traffic Manager Overview (トラフィック マネージャーの概要)](http://msdn.microsoft.com/library/windowsazure/hh744833.aspx)」を参照してください。

Web Apps 用など、Azure トラフィック マネージャーの構成方法については、「[トラフィック マネージャーの構成タスク](http://msdn.microsoft.com/library/windowsazure/hh744830.aspx)」を参照してください。

Azure トラフィック マネージャーでの負荷分散の詳細については、「[Traffic Manager での負荷分散方法について](http://msdn.microsoft.com/library/windowsazure/dn339010.aspx)」を参照してください。

Web Apps での Traffic Manager の使用の詳細については、ブログ記事「[Azure Web サイトでの Azure Traffic Manager の使用](http://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx)」および「[Azure Traffic Manager と Azure Web サイトの統合が可能になりました](http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/)」を参照してください。

<!--HONumber=54--> 