<properties
	pageTitle="Azure App Service での ClearDB MySql データベースの使用に関してよく寄せられる質問 (FAQ) | Microsoft Azure"
	description="Azure App Service での ClearDB MySql データベースの使用に関してよく寄せられる質問に答えます。"
	documentationCenter="php"
	services=""
	authors="sunbuild"
	manager="yochayk"
	editor=""
	tags="mysql"/>

<tags
	ms.service="multiple"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="sumuth"/>

# Azure App Service での ClearDB MySql データベースの使用に関してよく寄せられる質問 (FAQ)

この FAQ では、Azure Web Apps での ClearDB MySQL データベースの使用と購入に関してよく寄せられる質問に答えます。

## Azure で使用できる MySQL にはどのようなオプションがありますか。

いくつかのオプションがあります。

* [ClearDB Shared MySQL データベース](/marketplace/partners/cleardb/databases/)

* [ClearDB MySQL Premium クラスター](/marketplace/partners/cleardb-clusters/cluster/)

* [Azure VM で実行する MySQL クラスター](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)

* [Azure VM で実行する MySQL の 1 つのインスタンス](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)

ClearDB は、ユーザーに代わって MySQL インフラストラクチャを管理する、MySQL のホスティング サービスです。独自の MySQL クラスターやデータベースを Azure 仮想マシン上で実行する場合は、MySQL サーバーを設定し、修正プログラムを適用して常に最新の状態に保つ必要があります。

## Azure Marketplace で Web アプリケーションと MySQL テンプレートを入手するには、クレジット カードが必要ですか。

ご利用のサブスクリプションの種類によります。一般的なサブスクリプションの種類をいくつか示します。

* [従量課金](/offers/ms-azr-0003p/): クレジット カードが必要です。有料の MySQL データベースの購入時にクレジット カードに課金されます。

* [無料評価版](https://azure.microsoft.com/pricing/free-trial/): Microsoft Azure サービスに使用できるクレジットが含まれていますが、サードパーティのリソースは購入できません。サード パーティのサービスや有料の MySQL データベースを購入するには、クレジット カードを有効にしたサブスクリプションを使用する必要があります。Web Apps では無料の ClearDB MySQL データベースを作成できます。

* [MSDN サブスクリプション](/pricing/member-offers/msdn-benefits/) と **MSDN 向け開発テスト用の従量課金制プラン**: 無料評価版と同様に、MSDN サブスクリプションで ClearDB から有料の MySQL ソリューションを購入するには、クレジット カードが必要です。

* [エンタープライズ契約 (EA)](/pricing/enterprise-agreement/): EA のお客様の場合、Azure Marketplace (サード パーティ) での購入はすべて EA に対して課金されます (四半期ごとに個別の請求書が発行されます)。Marketplace での購入はすべて、年額コミットメントとは別に課金されます。現時点では、アゼルバイジャン、クロアチア、ノルウェー、プエルトリコで登録されたお客様は Azure ストアをご利用いただけませんのでご注意ください。

*   [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): Web Apps 向けの無料の ClearDB データベースのみを作成することができます。無料で作成できる ClearDB MySQL データベースの数に制限はありません。なお、無料のデータベースは試用を目的としているため、運用中の Web アプリケーションには使用できません。

## Azure Marketplace から Web アプリケーションと MySQL に 3.50 ドルを課金されているのはなぜですか。

既定のデータベース オプションは Titan で、価格は 3.50 ドルです。データベースの作成時には価格が表示されないため、このデータベースを誤って購入してしまった可能性があります。今後プロセスの改善に努めてまいりますが、それまでは、Web アプリケーションとデータベースについて選択した価格レベルをすべて確認してから、**[作成]** をクリックしてリソースのデプロイを開始してください。

## 自分が所有する Azure 仮想マシンで MySQL を実行しています。自分の Azure Web アプリケーションを自分のデータベースに接続することはできますか。

はい。Azure VM にご自身の Web アプリケーションへのリモート アクセス権が付与されていれば、そのアプリケーションをご自身のデータベースに接続していただけます。詳細については、「[Windows Server 2012 R2 を実行するクラシック デプロイ モデルで作成された仮想マシンに MySQL をインストールする](virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md)」を参照してください。

## ClearDB Premium MySQL クラスターがサポートされている国を教えてください。

[ClearDB Premium MySQL クラスター](/marketplace/partners/cleardb-clusters/cluster/)は、インド、オーストラリア、ブラジル南部、中国を除く、全世界のすべての Azure リージョンでご利用いただけます。

## ClearDB Premium クラスター ソリューションでデータベースを作成する前に新しいクラスターを作成できますか。

いいえ。空の ClearDB クラスターの作成はサポートされていません。Azure ポータルではクラスター内にデータベースを作成できるので、データベースの作成と同時に新しいクラスターが作成されることはあります。

## アプリケーションが使用中の ClearDB MySQL データベースを削除しようとすると、警告されますか。

いいえ。アプリケーションが使用中の Marketplace での購入物を削除しようとしても、警告は表示されません。

## ClearDB データベースを作成できるリージョンを教えてください。

Azure Marketplace は、アゼルバイジャン、クロアチア、ノルウェー、プエルトリコで登録されたお客様にはご利用いただけません。これらのリージョンでは、ClearDB を使用できません。

## 運用中の Web アプリケーションとデータベースにはどの価格レベルを選択すればよいですか。

Web Apps には Basic 以上の価格レベルを使用してください。ClearDB には Saturn か Jupiter をお勧めします。[Web Apps](/pricing/details/app-service/) と [ClearDB MySQL データベース](/marketplace/partners/cleardb/databases/)の両方について、各価格レベルの機能と制限事項を確認し、ニーズに合ったレベルを選択してください。

## ClearDB データベースのプランをアップグレードする方法を教えてください。

[ClearDB アップグレード ウィザード](https://www.cleardb.com/store/azure/upgrade)を使用してください。現時点では、Azure ポータルからはアップグレードできません。

## Azure ポータルに ClearDB データベースが表示されないのはなぜですか。

Azure Resource Manager または[新しい Azure ポータル](https://portal.azure.com)を使って作成した ClearDB データベースは、[以前の Azure ポータル](https://manage.windowsazure.com)には表示されません。これは回避するには、データベースを Web アプリケーションにを手動でリンクします。同様に、[古いポータル](https://manage.windowsazure.com)で作成した ClearDB データベースは、[新しい Azure ポータル](https://portal.azure.com)には表示されません。後者のシナリオの解決策はありません。

## データベースがダウンしたときはどこに問い合わせればよいですか。

データベース関連の問題については、[ClearDB のサポート](https://www.cleardb.com/developers/help/support)にお問い合わせください。その際、Azure サブスクリプションの情報を提供できるよう準備しておいてください。

## ClearDB MySQL データベース クラスター ソリューションに追加ユーザーを作成できますか。  

いいえ。追加ユーザーを作成することはできません。ただし、ClearDB データベース クラスターに追加のデータベースを作成することはできます。

## Basic/Pro シリーズのデータベースを、Planetary プランと同様、ClearDB ポータルですぐにインプレース アップグレードできますか。

はい、Basic シリーズのデータベースはインプレース アップグレードできます (Basic 60 ～ Basic 500)。Pro シリーズも、Pro 60 を除き、インプレース アップグレードできます (Pro 125 ～ Pro 1000)。現在、Pro 60 データベースのアップグレードはサポートされていません。

## サブスクリプションから別のサブスクリプションにリソースを移行すると、ClearDB MySQL データベースも移行されますか。  

サブスクリプション間でリソースの移行を実行する場合、いくつかの[制限事項](./app-service-web/app-service-move-resources.md)が適用されます。ClearDB MySQL データベースは、サード パーティのサービスであるため、Azure サブスクリプションの移行時にこのデータベースは移行されません。Azure リソースを移行する前に MySQL データベースの移行に対処していない場合、ClearDB MySQL データベースが無効化される可能性があります。事前に手動でデータベースを移行してから、Web アプリの Azure サブスクリプションを移行してください。

## エンタープライズ契約 (EA) サブスクリプションでスケーラブル WordPress を購入することはできますか。

プロセスはどのサブスクリプションでも同じです。[Azure ポータル](https://portal.azure.com/)で Azure Marketplace に移動し、[[スケーラブル WordPress]](https://portal.azure.com/?feature.customportal=false#create/WordPress.ScalableWordPress) を選択してアプリの作成を開始します。スケーラブル WordPress でサポートされる ClearDB の価格レベルは Saturn と Jupiter のみです。EA のクレジットは、Standard Web Apps 価格レベルで実行されている Web アプリケーションと、有料の ClearDB (Shared) MySQL データベースの両方に使用できます。[/marketplace/faq/](/marketplace/faq/) ストアで購入した料金は EA に対して課金されます (四半期ごとに個別の請求書が発行されます)。

## ClearDB データベースのサブスクリプションをクレジット カードから EA に移行できますか。

既存の ClearDB データベースには、既存のサブスクリプションに関連付けられているクレジット カードが引き続き使用されます。EA サブスクリプションを使用するには、次の方法で新しいデータベースにデータを移行する必要があります。

* EA サブスクリプションで新しい ClearDB データベースを購入します。
* 新しいデータベースにデータを移行します。
* 新しいデータベースを使用するようにアプリケーションを更新します。
* 既存の ClearDB データベースを削除します。

MySQL (ClearDB) を使用して新しい Web アプリケーションを作成するか、MySQL データベース (ClearDB) を作成する場合、選択したサブスクリプションによってサービスの支払い方法が変わります。EA サブスクリプションの場合は、Azure ポータルで ClearDB などのサード パーティ サービスを調達することができます。EA サブスクリプションは年額コミットメントとは別に課金され、四半期ごとに未払い分が請求されます。EA のお客様は、サード パーティの Marketplace サービスの支払いのために、クレジット カードなどの支払方法を設定する必要があります。

## EA サブスクリプションでの ClearDB リソースの料金はどこで確認できますか。

ダイレクト EA のお客様の場合、Azure Marketplace の料金はエンタープライズ ポータルに表示されます。なお、Marketplace での購入および使用はすべて年額コミットメントとは別に課金され、四半期ごとに未払い分が請求されます。EA のお客様は、サード パーティのサービス プロバイダーに直接お支払いいただく必要があります。そのためには、EA アカウントでクレジット カードなどの支払方法を有効にします。

インダイレクト EA のお客様の場合は、エンタープライズ ポータルの **[サブスクリプションの管理]** ページで Azure Marketplace のサブスクリプションを確認できますが、価格は非表示になっています。Marketplace の料金については、LSP にお問い合わせください。

EA Azure の登録管理者は、Azure Marketplace のサード パーティ サービスへのアクセスを管理することができます。エンタープライズ ポータルの [アカウント] セクションの下にある [アカウントとサブスクリプションの管理] で、ストアでのサード パーティ サービスの購入を無効または再度有効にします。

## EA サブスクリプションでの ClearDB サービスの料金については、どこに問い合わせればよいですか。

EA 登録での料金に関するご質問は、[エンタープライズ カスタマー サポート](http://aka.ms/AzureEntSupport)にお問い合わせください。EA ポータル サポート チームがご質問に回答し、問題解決のお手伝いをします。

 



## 詳細情報

[Azure Marketplace FAQ](/marketplace/faq/)

<!---HONumber=AcomDC_0330_2016------>