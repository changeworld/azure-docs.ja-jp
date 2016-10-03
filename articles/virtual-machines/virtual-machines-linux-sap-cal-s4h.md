<properties 
pageTitle="Microsoft Azure での S/4 HANA または BW/4 HANA のデプロイ | Microsoft Azure" 
description="Microsoft Azure での S/4 HANA または BW/4 HANA のデプロイ" 
services="virtual-machines-linux" 
documentationCenter="" 
authors="hermanndms" 
manager="timlt" 
editor="" 
tags="azure-resource-manager" 
  keywords=""/>
<tags  
  ms.service="virtual-machines-linux" 
  ms.devlang="na" 
  ms.topic="article" 
  ms.tgt_pltfrm="vm-linux" 
  ms.workload="infrastructure-services" 
  ms.date="09/15/2016" 
  ms.author="hermannd"/>


# Microsoft Azure での S/4 HANA または BW/4 HANA のデプロイ 

この記事では、SAP Cloud Appliance Library 3.0 を使用して Microsoft Azure で S/4 HANA をデプロイする方法について説明します。スクリーン ショットでは、その手順を示します。他の SAP HANA ベースのソリューション (BW/4 HANA など) のデプロイも同様のプロセスで機能します。必要なのは別のソリューションを選択することだけです。

SAP Cloud Appliance Library (SAP CAL) の作業は[ここ](https://cal.sap.com/)から開始できます。新しい [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience) のブログもご覧ください。


次のスクリーンショットに、Microsoft Azure で S/4 HANA をデプロイする手順を示します。BW/4 HANA などの他のソリューションでもプロセスは同じです。


![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-1b.jpg)

最初の画像は、Microsoft Azure で利用できるすべての SAP CAL HANA ベースのソリューションを示しています。例として、"SAP S/4 HANA on-premises edition" (スクリーンショットの下部のソリューション) を選択して手順を説明します。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic-2.jpg)

最初に、新しい SAP CAL アカウントを作成する必要があります。Azure には現在 2 つの選択肢 (標準の Azure と、パートナー 21Vianet が運営する中国本土の Azure) が用意されています。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic3b.jpg)

次に、Azure ポータルにある Azure サブスクリプション ID を入力します (入手方法はこのページの以降のセクションで確認できます)。その後、Azure 管理証明書をダウンロードする必要があります。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic6b.jpg)

新しい Azure ポータルの左側に "サブスクリプション" という項目が見つかります。クリックすると、ユーザーのすべてのアクティブなサブスクリプションが表示されます。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic7b.jpg)

サブスクリプションのいずれかを選択し、[管理証明書] を選択すると、新しい Azure Resource Manager モデルの "サービス プリンシパル" を使用する新しい概念の説明が表示されます。SAP CAL はこの新しいモデルにはまだ対応していないため、管理証明書を操作するには "クラシック" モデルと以前の Azure ポータルが必要です。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic4b.jpg)

これは、以前の Azure ポータルです。管理証明書をアップロードすると、顧客サブスクリプション内で仮想マシンを作成する権限が SAP CAL に付与されます。[サブスクリプション] タブの下に、SAP CAL ポータルで入力する必要のあるサブスクリプション ID が表示されます。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic5.jpg)

2 番目のタブで、前に SAP CAL からダウンロードした管理証明書をアップロードできます。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic8.jpg)

ダウンロードした証明書ファイルを選択するための小さなダイアログが表示されます。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic9.jpg)

証明書がアップロードされると、SAP CAl と顧客 Azure サブスクリプション間の接続を SAP CAl 内でテストできます。接続が有効であることを示す小さなポメッセージが表示されます。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic10.jpg)

アカウントのセットアップが完了したら、デプロイするソリューションを選択し、インスタンスを 1 つ作成する必要があります。"基本" モードでは、簡単に作成できます。インスタンス名を入力し、Azure リージョンを選択して、ソリューションのマスター パスワードを定義します。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic11.jpg)

ソリューションのサイズと複雑さに応じた一定の時間が経過すると (推定時間が SAP CAL によって示されます)、"アクティブ" と表示され、使用する準備が整います。この操作は非常にシンプルです。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic12.jpg)

ソリューションの詳細を見てみると、どの種類の VM がデプロイされたかがわかります。ここでは、異なる 3 つのサイズの Azure VM と目的が作成されました。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic13.jpg)

Azure ポータルで、SAP CAL で指定したのと同じインスタンス名で始まる仮想マシンが見つかります。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic14b.jpg)

これで、SAP CAL ポータルの接続ボタンを使用して、ソリューションに接続できるようになりました。ソリューションを処理するためのすべての既定の資格情報を説明するユーザー ガイドへのリンクが含まれた小さいダイアログが表示されます。

![](./media/virtual-machines-linux-sap-cal-s4h/s4h-pic15.jpg)

別のオプションとして、クライアント Windows VM にログインして、事前構成された SAP GUI などを起動することもできます。

<!---HONumber=AcomDC_0921_2016-->