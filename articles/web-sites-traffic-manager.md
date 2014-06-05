<properties linkid="web-sites-traffic-manager" urlDisplayName="Azure トラフィック マネージャーによる Azure の Web サイトのトラフィックの制御" pageTitle="Azure トラフィック マネージャーによる Azure の Web サイトのトラフィックの制御" metaKeywords="Azure Web サイト, トラフィック マネージャー, 要求の振り分け, ラウンド ロビン, フェールオーバー, パフォーマンス" description="この記事では、Azure の Web サイトに関連して、Microsoft Azure トラフィック マネージャーの概要情報を提供します。" metaCanonical="" services="web-sites" documentationCenter="" title="Azure トラフィック マネージャーによる Azure の Web サイトのトラフィックの制御" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

# Azure トラフィック マネージャーによる Azure の Web サイトのトラフィックの制御

> [WACOM.NOTE] この記事では、Azure の Web サイトに関連して、Microsoft Azure トラフィック マネージャーの概要情報を提供します。Azure トラフィック マネージャー自体の詳細については、この記事の最後にある関連情報を参照してください。

## はじめに
Azure トラフィック マネージャーを使用すると、Web クライアントからの要求を Azure の Web サイトに振り分ける方法を制御できます。Azure の Web サイトのエンドポイントが Azure トラフィック マネージャーのプロファイルに追加されると、Azure トラフィック マネージャーは Web サイトの状態 (実行中、停止、または削除済み) を追跡して、トラフィックを受信する必要のあるエンドポイントを決定できるようになります。

## 負荷分散方法
Azure トラフィック マネージャーは 3 つの異なる負荷分散方法を使用します。これらの方法について、Azure の Web サイトに関連して、次の一覧で説明します。

* **フェールオーバー**: Web サイトの複製がさまざまなリージョンにある場合、この方法では、すべての Web クライアント トラフィックを処理するように、1 つの Web サイトを構成でき、最初の Web サイトが利用不可になったときにトラフィックを処理するように、異なるリージョン内の別の Web サイトを構成できます。
	
* **ラウンド ロビン**: Web サイトの複製がさまざまなリージョンにある場合、この方法では、異なるリージョン内の Web サイト間でトラフィックを均等に振り分けることができます。
	
* **パフォーマンス**: この方法では、クライアントへの最短の往復時間に基づいてトラフィックを振り分けます。この方法は同じリージョン内または異なるリージョン内の Web サイトに使用できます。

Azure トラフィック マネージャーでの負荷分散の詳細については、「[Traffic Manager での負荷分散方法について](http://msdn.microsoft.com/ja-jp/library/windowsazure/dn339010.aspx)」を参照してください。

##Azure の Web サイトとトラフィック マネージャーのプロファイル
Web サイトのトラフィックを制御するように構成するには、Azure トラフィック マネージャーでプロファイルを作成し、前に説明している 3 つの負荷分散方法のいずれかをプロファイルで指定します。その後、トラフィックを制御するエンドポイント (この場合は Web サイト) をプロファイルに追加します。Web サイトの状態 (実行中、停止、または削除済み) は定期的にプロファイルに反映されて、その状態に応じて Azure トラフィック マネージャーはトラフィックを振り分けることができます。

Azure トラフィック マネージャーを Azure で使用する場合は、次の点に留意してください。

* 同じリージョンでの Azure の Web サイトのみの展開の場合、Web サイトはそのモードには関係なく、フェールオーバーとラウンド ロビンの機能を既に備えています。

* 同じリージョンでの Azure の Web サイトの展開で、Azure の別のクラウド サービスと連携させる場合、両方の種類のエンドポイントを組み合わせたハイブリッドのシナリオが可能です。

* リージョンごとに 1 つのみの Web サイト エンドポイントをプロファイルで指定することもできます。1 つのリージョンのエンドポイントとして Web サイトを選択すると、そのリージョン内の残りの Web サイトはそのプロファイルで選択できなくなります。

* Azure トラフィック マネージャーのプロファイルで指定した Web サイト エンドポイントは、プロファイルで Web サイトの構成ページの **[ドメイン名]** セクションに表示されますが、そこでは構成できません。

* Web サイトをプロファイルに追加した後、Web サイトのポータル ページのダッシュボードの **[サイトの URL]** には、Web サイトのカスタム ドメインを設定していればその URL が表示されます。それ以外の場合は、トラフィック マネージャーのプロファイルの URL (`contoso.trafficmgr.com` など) が表示されます。Web サイトの直接のドメイン名とトラフィック マネージャーの URL の両方が、Web サイトの構成ページの **[ドメイン名]** セクションに表示されます。

* カスタム ドメイン名は予期したとおりに機能しますが、それらのドメイン名を Web サイトに追加するだけでなく、トラフィック マネージャーの URL を参照するように DNS マップを構成する必要もあります。Azure の Web サイトのカスタム ドメイン名の設定については、「[Configuring a custom domain name for an Azure web site (Azure の Web サイトのカスタム ドメインの構成)](https://www.windowsazure.com/ja-jp/documentation/articles/web-sites-custom-domain-name/)」を参照してください。

* 標準モードの Web サイトのみを Azure トラフィック マネージャーのプロファイルに追加できます。

## 次のステップ

Azure トラフィック マネージャーの概念と技術的概要については、「[Traffic Manager Overview (トラフィック マネージャーの概要)](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh744833.aspx)」を参照してください。

Azure の Web サイト用など、Azure トラフィック マネージャーの構成方法については、「[Traffic Manager Configuration Tasks (トラフィック マネージャーの構成タスク)](http://msdn.microsoft.com/ja-jp/library/windowsazure/hh744830.aspx)」を参照してください。

Azure トラフィック マネージャーでの負荷分散の詳細については、「[Traffic Manager での負荷分散方法について](http://msdn.microsoft.com/ja-jp/library/windowsazure/dn339010.aspx)」を参照してください。


