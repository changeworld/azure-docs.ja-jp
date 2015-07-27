<properties
	pageTitle="Azure Automation とは"
	description="Azure Automation が提供する価値について説明し、よく寄せられる質問への回答を示して Runbook の作成と使用を始められるようにします。"
	services="automation"
	documentationCenter=""
	authors="bwren"
	manager="stevenka"
	editor=""/>

<tags
	ms.service="automation"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="07/06/2015"
	ms.author="bwren"/>

# Azure Automation とは

ユーザーは Microsoft Azure Automation を使用すると、クラウド環境およびエンタープライズ環境で一般的に実行される、手動で実行時間が長く、エラーが起こりやすく、頻繁に繰り返されるタスクを自動化する手段を入手できます。この記事では、Azure Automation に関してよく寄せられる質問に簡単に回答します。異なるトピックの詳細については、このライブラリの他の記事を参照してください。

## Azure Automation にはどのような価値があるのでしょうか。

Azure Automation を使用すると、クラウドおよびエンタープライズ環境で実行する通常の管理タスクの時間を節約でき、その信頼性が向上します。これらのプロセスを Runbook として実装できます。Runbook では、ユーザーの介入なしに複数のタスクを実行したり、一定の間隔で自動的に実行するようにスケジュールを設定したりできます。

## Runbook とは何ですか。

Runbook は、Azure Automation で自動化されたプロセスを実行する一連のタスクです。仮想マシンの開始やログ エントリの作成といった簡単な処理だけでなく、他の小さい Runbook を結合して複数のリソースまたは複数のクラウドを対象とした複雑な処理を実行する複雑な Runbook も作成できます。

たとえば、仮想マシンの作成、ネットワークへの接続、IP アドレスの割り当て、準備完了のユーザーへの通知などの複数の手順が含まれる、新しい仮想マシンをプロビジョニングするための既存の手動プロセスがあるものとします。手動でこれらの各手順を実行するの代わりに、1 つのプロセスとしてこれらすべてのタスクを実行する Runbook を作成できます。Runbook を開始し、仮想マシン名、IP アドレス、受信者の電子メールなどの必要な情報を提供した後は、プロセスが完了するまでユーザーが手を出す必要はありません。


## Runbook が自動化できるものは何ですか。

Azure Automation の Runbook は PowerShell Workflow に基づいているので、PowerShell でできることであれば何でも実行できます。アプリケーションまたはサービスに API がある場合、Runbook はそれを操作できます。それに対する PowerShell モジュールがある場合は、そのモジュールを Azure Automation に読み込み、コマンドレットを Runbook に組み込むことができます。Azure Automation の Runbook は、Azure クラウド内で実行するので、クラウド内のすべてのリソースや、クラウドからアクセスできる外部のリソースにアクセスできます。[ハイブリッド Runbook Worker](automation-hybrid-runbook-worker.md) を使用すると、Runbook はローカル データ センターで実行してローカル リソースを管理できます。


## Runbook はどこで入手するのでしょうか。

[Runbook Gallery](http://msdn.microsoft.com/library/azure/dn781422.aspx) で入手できるマイクロソフトやコミュニティが提供する Runbook は、そのまま環境内で使用することも、独自の目的に合わせてカスタマイズすることもできます。独自の Runbook を作成する方法を知るための参考情報としても役に立ちます。他のユーザーにも役に立つと思われる Runbook をギャラリーに提供して貢献することもできます。


## 独自の Runbook はどうすれば作成できますか

最初から[独自の Runbook を作成する](http://msdn.microsoft.com/library/azure/dn643637.aspx)ことも、[Runbook Gallery](http://msdn.microsoft.com/library/azure/dn781422.aspx) の Runbook を独自の要件に合わせて修正することもできます。PowerShell のコードを直接操作する方がよい場合は、Azure ポータルまたはオフラインで[テキスト エディターを使用して Runbook を編集する](http://msdn.microsoft.com/library/azure/dn879137.aspx)ことができます。基になっているコードは見ずに Runbook を編集したい場合は、Azure プレビュー ポータルの[グラフィカル エディター](automation-graphical-authoring-intro.md)を使用できます。


## Azure Automation と他のオートメーション ツールを関連付けるにはどうすればよいですか。

[Service Management Automation (SMA)](http://technet.microsoft.com/library/dn469260.aspx) は、プライベート クラウドでの管理タスクを自動化するためのものです。[Windows Azure Pack](http://www.microsoft.com/server-cloud/products/windows-azure-pack/default.aspx) のコンポーネントとしてデータ センターにローカルにインストールされます。SMA と Azure Automation は Windows PowerShell Workflow に基づく同じ Runbook 形式を使用しますが、SMA は[グラフィカルな Runbook](automation-graphical-authoring-intro.md) をサポートしません。

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) はオンプレミスのリソースを自動化するためのものです。Azure Automation および Service Management Automation とは異なる形式の Runbook を使用し、スクリプトを作成する必要なしに Runbook を作成するためのグラフィカル インターフェイスを備えています。その Runbook は、Orchestrator 用に特に作成された統合パックのアクティビティで構成されます。

## 詳細な情報はどこで入手できますか。

Azure Automation や独自の Runbook の作成方法の詳細を確認できる多数のリソースが用意されています。

- ここは、**Azure Automation ライブラリ**です。このライブラリの記事では、Azure Automation の構成や管理、また独自の Runbook の作成に関する完全なドキュメントが提供されます。
- [Azure PowerShell コマンドレット](http://msdn.microsoft.com/library/jj156055.aspx)では、Windows PowerShell を使用した Azure の操作の自動化に関する情報が提供されます。Runbook ではこれらのコマンドレットを使用して Azure リソースを操作します。
- [Azure Automation ブログ](http://azure.microsoft.com/blog/tag/azure-automation)には、マイクロソフトからの Azure Automation に関する最新の情報が掲載されています。このブログをサブスクライブすると、Azure Automation チームによる最新の情報を把握できます。
- [Automation フォーラム](http://go.microsoft.com/fwlink/p/?LinkId=390561)では Azure Automation に関する質問を投稿でき、マイクロソフトや Automation コミュニティから回答を取得できます。

## フィードバックを提供できますか。

**フィードバックをお待ちしています。** Azure Automation の Runbook ソリューションや統合モジュールを探索している場合は、スクリプト センターにスクリプトの要求を投稿することができます。Azure Automation に関するフィードバックや機能に関するご要望は、[User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback) にお寄せください。よろしくお願いいたします。

<!---HONumber=July15_HO3-->