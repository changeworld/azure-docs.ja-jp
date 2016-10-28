<properties
	pageTitle="仮想マシン スケール セットの自動スケールに関するトラブルシューティング | Microsoft Azure"
	description="仮想マシン スケール セットの自動スケールに関するトラブルシューティングを行います。よくある問題とその解決方法について説明します。"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/28/2016"
	ms.author="guybo"/>
    
# 仮想マシン スケール セットの自動スケールに関するトラブルシューティング

**問題** – Azure Resource Manager で仮想マシン スケール セットを使用して自動スケール インフラストラクチャを作成しました。このとき、https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale のようなテンプレートをデプロイしました。定義したスケール規則は正常に機能しましたが、仮想マシンの負荷をいくら増やしても、自動スケールが実行されません。

## トラブルシューティングの手順

次のような点を検討します。

- 各 VM のコア数はいくつですか。すべてのコアを読み込んでいますか。 上の例の Azure Quickstart テンプレートには do\_work.php スクリプトが含まれ、1 つのコアが読み込まれます。Standard\_A1 よりも大きい VM を使用する場合、これを複数回読み込む必要があります。VM のコア数を確認する方法については、「[Azure の Windows 仮想マシンのサイズ](../virtual-machines/virtual-machines-windows-sizes.md)」をご覧ください。

- VM スケール セット内の VM 数はいくつですか。VM ごとに設定を行っていませんか。

    自動スケール規則で定義された時間内に、スケール セット内の**すべての** VM における平均 CPU 使用率がしきい値を超えた場合にのみ、スケールアウト イベントが発生します。

- スケール イベントを見逃していませんか。

    Azure ポータルで監査ログを確認し、スケール イベントを探してください。スケールアップやスケールダウンを見逃している可能性があります。"Scale" でフィルター処理できます。

	![Audit Logs][audit]

- スケールインとスケールアウトのしきい値の間に十分な差を確保していますか。

    たとえば、平均 CPU 使用率が 5 分間にわたって 50% を超えた場合にスケールアウトし、平均 CPU 使用率が 50% を割り込んだときにスケールインする規則を設定したとします。CPU 使用率がこのしきい値に近付くと、スケール アクションによってセットのサイズが絶え間なく増減されるという "フラッピング" 問題が発生します。自動スケール サービスはこの "フラッピング" を防止しようとするため、自動スケールが実行されなくなる場合があります。そのため、スケールアウトとスケールインのしきい値の間に十分な差を付けて、自動スケーリングが実行される余裕を確保する必要があります。

- 独自の JSON テンプレートを作成しましたか。

    JSON テンプレートは間違いが起きやすいため、最初のうちは上の例のような確実に機能するテンプレートを基にして、少しずつ変更を加えるようにしてください。JSON テンプレートでは、診断拡張機能のストレージ アカウント、スケール セット、Microsoft.Insights リソースを関連付け、パフォーマンス データ メトリックの名前を正しく参照する必要があります。この名前は、Windows と Linux で異なります。

- 手動でスケールインまたはスケールアウトできますか。

    "capacity" 設定を変えた VM スケール セット リソースを再デプロイして、VM 数を手動で変更してみてください。そのためのテンプレートの例については、https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing を参照してください。必要に応じてテンプレートを編集して、スケール セットで使用されている仮想マシン サイズと同じにしてください。VM 数を手動で変更できた場合、問題の原因が自動スケールに限定されます。

- Microsoft.Compute/virtualMachineScaleSet を確認してください。また、[Azure リソース エクスプローラー](https://resources.azure.com/)で Microsoft.Insights リソースを確認してください。

    このツールは Azure Resource Manager リソースの状態を表示できるため、トラブルシューティングには欠かせません。サブスクリプションをクリックし、トラブルシューティングを行うリソース グループを表示します。Compute リソースプロバイダーの下で、作成した VM スケール セットを探し、インスタンス ビューでデプロイの状態を確認します。また、VM スケール セット内の VM のインスタンス ビューも確認します。次に Microsoft.Insights リソースプロバイダーに移動し、自動スケール規則が適切かどうかを確認します。

- 診断拡張機能が動作し、パフォーマンス データを出力していますか。
 
    Azure Resource Manager の自動スケールは、診断拡張機能と呼ばれる VM 拡張機能によって動作します (Linux 診断拡張機能と Windows 診断拡張機能に分かれています)。テンプレートで定義されているストレージ アカウントにパフォーマンス データが出力されます。その後、このデータは Azure Insights サービスによって集計されます。

    VM がダウンした場合など、Insights サービスが VM からデータを読み取れない場合には、Insights サービスからメールが送信されます。メールが届いていないかどうか確認してください (Azure アカウントの作成時に指定したメール アドレスです)。

    自分でデータを確認することもできます。Cloud Explorer を使用して Azure ストレージ アカウントを確認します。たとえば、[Visual Studio Cloud Explorer](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2) を使用して、現在利用中の Azure サブスクリプションにログインし、デプロイ テンプレートの診断拡張機能の定義で参照されている診断ストレージ アカウントの名前を選択します。

	![Cloud Explorer][explorer]

    ここでは、各 VM のデータが格納される一連のテーブルが表示されています。例として、Linux と CPU のメトリックで最新の行を見てみましょう。Visual Studio Cloud Explorer ではクエリ言語がサポートされるため、「Timestamp gt datetime’2016-02-02T21:20:00Z’」のようなクエリを実行することで最新のイベントを確実に取得できます (時刻が UTC の場合)。ここに表示されるデータは、設定したスケール規則に対応しているでしょうか。 次の例では、仮想マシン 20 の CPU 使用率がこの 5 分間で 100% に上昇しています。

	![Storage Tables][tables]

    データがない場合、VM で実行されている診断拡張機能に問題があることがわかります。データが存在する場合は、スケール規則と Insights サービスのどちらかに問題があることがわかります。[Azure の状態](https://azure.microsoft.com/status/)を確認してください。

    これまでの手順を終えても問題が解決しない場合は、[MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp) または [Stack Overflow](http://stackoverflow.com/questions/tagged/azure) のフォーラムをご利用になるか、サポートまでお問い合わせください。テンプレートと、パフォーマンス データのビューを共有できるように準備しておいてください。

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png

<!---HONumber=AcomDC_0427_2016-->