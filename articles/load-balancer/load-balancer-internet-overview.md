
<properties 
   pageTitle="インターネットに接続するロード バランサーの概要 |Microsoft Azure"
   description="インターネットに接続するロード バランサーとその機能の概要ロード バランサーが仮想マシンとクラウド サービスを使用して Azure で機能する方法"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="joaoma" />


# 複数の Virtual Machines またはサービス間でインターネットに接続するロード バランサー

エンドポイントの 1 つの使用方法は、Azure Load Balancer を構成して、複数の仮想マシンまたはサービス間で特定の種類のトラフィックを分散することです。たとえば、複数の Web サーバーまたは Web ロール間で Web 要求のトラフィックの負荷を分散できます。

Azure Load Balancer は、着信トラフィックのパブリック IP アドレスとポート番号を仮想マシンのプライベート IP アドレスとポート番号にマップし、仮想マシンからの応答トラフィックはその逆にマップします。

>[AZURE.NOTE] Azure ロード バランサーは、既定の設定を使用して、複数の仮想マシン インスタンス間でハッシュ分散ネットワーク トラフィックを提供します (ハッシュ分散の詳細については、「[ロード バランサーの機能](load-balancer-overview.md#load-balancer-features)」を参照してください)。セッション アフィニティの詳細については、「[ロード バランサー分散モード](load-balancer-distribution-mode.md)」をご覧ください。

クラウド サービスに Web ロールまたは Worker ロールのインスタンスが含まれる場合、サービス定義でパブリック エンドポイントを定義できます。
 
Servicedefinition.csdef ファイルにはエンドポイント構成が含まれ、Web ロールまたは Worker ロールのデプロイメント用の複数のロール インスタンスがある場合は、ロード バランサーをセットアップできます。インスタンスをクラウドのデプロイメントに追加するには、サービス構成ファイル (.csfg) のインスタンス数を変更します。

次の図は、暗号化された Web トラフィック用の負荷分散されたエンドポイントを示しています。このエンドポイントは、パブリックとプライベートの TCP ポートが 443 である 3 台の仮想マシン間で共有されています。この 3 台の仮想マシンは、1 つの負荷分散セット内にあります。


![パブリックのロード バランサーの例](./media/load-balancer-internet-overview/IC727496.png))

複数のインターネット クライアントがクラウド サービスのパブリック IP アドレスと TCP ポート 443 に Web ページ要求を送信すると、Azure Load Balancer は、負荷分散セット内の 3 台の仮想マシン間でこれらの要求のハッシュ ベースの負荷分散を行います。ロード バランサーのアルゴリズムの詳細については、[ロード バランサーの概要に関するページ](load-balancer-overview.md#load-balancer-features)を参照してください。


## 次のステップ

インターネットに接続するロード バランサーについて学習した後は、[内部ロード バランサー](load-balancer-internal-overview.md)に関する記事を読み、クラウドのデプロイに適たロード バランサーの種類を確認します。

[インターネットに接続するロード バランサーの作成を開始](load-balancer-get-started-internet-arm-ps.md)し、特定のロード バランサーのネットワーク トラフィックの動作に適した種類の[分散モード](load-balancer-distribution-mode.md)を構成することもできます。

アプリケーションでロード バランサーの背後にあるサーバーの接続を維持する必要がある場合は、[ロード バランサーの TCP アイドル タイムアウトの設定](load-balancer-tcp-idle-timeout.md)に関する詳細を確認してください。Azure Load Balancer 使用時にアイドル接続動作について理解するのに役立ちます。


 

<!---HONumber=AcomDC_0427_2016-->