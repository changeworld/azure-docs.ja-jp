<properties
	pageTitle="障害発生時のモバイル サービスの復旧 | Microsoft Azure"
	description="障害発生時にモバイル サービスを復旧する方法について説明します。"
	services="mobile-services"
	documentationCenter=""
	authors="christopheranderson"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="christopheranderson"/>

# 障害発生時のモバイル サービスの復旧

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

Azure Mobile Services を使用して、アプリケーションをデプロイした場合、組み込み機能を使用することで、サーバーの障害、ネットワークの中断、データの消失、広範囲に及ぶ設備の損失など、可用性の問題が発生したときにビジネス継続性を維持できます。Azure Mobile Services を使用して、アプリケーションをデプロイすることで、従来のオンプレミスのソリューションをデプロイする場合に設計、実装、および管理する必要があるさまざまなフォールト トレランス機能やインフラストラクチャ機能を利用することができます。Azure により、わずかな料金で潜在的な障害の大部分が軽減されます。

## <a name="prepare"></a>考えられる災害に対応する準備

可用性の問題が発生した場合に簡単に復旧するには、あらかじめ対応する準備を整えておきます。

+ **Azure Mobile Services の SQL Database のデータをバックアップする** モバイル サービス アプリケーションのデータは、Azure SQL Database に格納されています。「[Azure SQL データベースにおけるビジネス継続性]」の説明に従ってバックアップすることをお勧めします。
+ **モバイル サービス スクリプトをバックアップする** [Team Foundation Service] や [GitHub] などのソース管理システムにモバイル サービス スクリプトを保存し、モバイル サービス自体でのコピーのみに頼らないようにすることをお勧めします。スクリプトは、Mobile Services の[ソース管理機能]または [Azure CLI を使用]して、Azure クラシック ポータルからダウンロードできます。Azure クラシック ポータルで "プレビュー" というラベルが付いている機能には、細心の注意を払ってください。それらのスクリプトの復旧は保証されておらず、独自のソース管理の元のスクリプトから復旧することが必要になる場合があります。
+ **セカンダリ モバイル サービスを予約する** モバイル サービスで可用性の問題が発生した場合、Azure の代替リージョンに再デプロイすることが必要になる場合があります。(たとえば、リージョン全体のデータの損失などのまれな状況で) 容量の可用性を確保するためには、代替リージョンにセカンダリ モバイル サービスを作成し、そのモードをプライマリ サービスのモードと同じかそれ以上に設定することをお勧めします。(プライマリ サービスが Basic モードの場合、セカンダリ サービスは Basic と Standard のどちらかに設定できます。ただし、プライマリが Standard の場合、セカンダリも Standard である必要があります)。

## <a name="watch"></a>問題の兆候の監視

次の状況は、復旧操作が必要となる可能性がある問題を示しています。

+ モバイル サービスに接続されているアプリケーションが長時間にわたってモバイル サービスと通信できない。
+ [Azure クラシック ポータル]で、モバイル サービスの状態が "**異常**" と表示されている。
+ Azure クラシック ポータルで、モバイル サービスのどのタブの上部にも "**異常**" というバナーが表示され、管理操作を実行するとエラー メッセージが生成される。
+ [Azure サービス ダッシュボード]に可用性の問題が示されている。

## <a name="recover"></a>災害からの復旧

問題が発生した場合は、サービス ダッシュボードを使用して、ガイダンスと最新情報を取得します。

サービス ダッシュボードが表示されたら、次の手順を実行して、モバイル サービスを Azure の代替リージョンで "実行中" 状態に復元します。あらかじめセカンダリ サービスを作成している場合は、その容量を使用して、プライマリ サービスが復元されます。プライマリ サービスの URL とアプリケーション キーは復旧後も変更されないため、プライマリ サービスを参照するアプリケーションを更新する必要はありません。

停電後にモバイル サービスを復旧するには、以下の手順を実行します。

1. Azure クラシック ポータルで、サービスの状態が "**異常**" と報告されていることを確認します。

2. 既にセカンダリ モバイル サービスを予約している場合は、この手順をスキップできます。

   セカンダリ モバイル サービスをまだ占有に設定していない場合は、Azure の別のリージョンにセカンダリ モバイル サービスを 1 つ作成します。スケール モードをプライマリ サービスのモードと同じに設定します。

3. [Azure CLI を使用したモバイル サービスの自動化]に関するページの説明に従って、サブスクリプションを操作できるように Azure コマンドライン インターフェイス (Azure CLI) を構成します。

4. これで、セカンダリ サービスを使用して、プライマリ サービスを復旧できます。

	> [AZURE.IMPORTANT] 移行コマンドでは、ファイルを移行するだけでなく、プライマリ サービスのホスト名がセカンダリ サービスにポイントするように更新し、クライアント アプリケーションを更新する必要がないようにします。ただし、ホスト名が新しいサービスに決定するまで最長 30 分かかります。このため、障害回復シナリオでは移行コマンドのみを使用することをお勧めします。

	> [AZURE.IMPORTANT] この手順のコマンドを実行すると、セカンダリ サービスの容量を使用してプライマリ サービスを復旧できるように、セカンダリ サービスは削除されます。スクリプトと設定を保持する場合は、コマンドを実行する前に、バックアップすることをお勧めします。

	準備ができたら、次のコマンドを実行します。

		azure mobile migrate PrimaryService SecondaryService
		info:    Executing command mobile migrate
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it and the host name for 'PrimaryService' may not resolve for up to 30 minutes. Do you want to migrate the mobile service 'PrimaryService'? [y/n]: y
		+ Performing migration
		+ Migration with id '0123456789abcdef0123456789abcdef' started. The migration may take several minutes to complete.
		+ Cleaning up
		info:    Migration complete. It may take 30 minutes for DNS to resolve to the migrated site.
		info:    mobile migrate command OK

    > [AZURE.NOTE] コマンドが完了してから Azure クラシック ポータルに変更が反映されるまでに数分かかる場合があります。

5. すべてのスクリプトをソース管理内の元のスクリプトと比較して、正しく復旧されていることを確認します。ほとんどの場合、スクリプトはデータが失われることなく自動的に復旧されます。ただし、相違がある場合は、そのスクリプトを手動で復旧できます。

6. 復旧したサービスが Azure SQL Database と通信していることを確認します。復旧コマンドでは、モバイル サービスが復旧されますが、元のデータベースへの接続が保持されます。Azure のプライマリ リージョンの問題がデータベースにも影響を与えている場合は、復旧したサービスが正常に実行されないことがあります。特定のリージョンのデータベースの状態は、Azure サービス ダッシュボードを使用して確認できます。元のデータベースが動作していない場合は、次の手順でデータベースを復旧できます。
	+ 「[Azure SQL Database におけるビジネス継続性]」の説明に従って、モバイル サービスを復旧した Azure のリージョンに対して Azure SQL Database を復旧します。
	+ Azure クラシック ポータルで、モバイル サービスの **[構成]** タブにある [データベースの変更] をクリックし、新しく復旧したデータベースを選択します。

7. これでモバイル サービスは、別の物理的な場所でホストされるようになりました。実行中のサイトを更新できるように、発行と git の資格情報を更新する必要があります。

	+ **.NET バックエンド** を使用している場合、「[Publish your mobile service (モバイル サービスの発行)](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md#publish-your-mobile-service)」で説明されるように発行プロファイルをもう一度設定します。これで新しいサービスの場所を指定する発行の詳細が更新されます。
	+ **Javascript バックエンド**を使用し、ポータルでサービスを管理する場合、追加の操作を行う必要はありません。

	+ **Javascript バックエンド**を使用してノードでサービスを管理する場合、新しいリポジトリを指定する Git リモートを更新します。これを実行するには、Git リモートから git ファイルのパスを削除します。

		1. 現在の配信元のリモートを探します。

				git remote -v
				 origin  https://myservice.scm.azure-mobile.net/myservice.git (fetch)
				 origin  https://myservice.scm.azure-mobile.net/myservice.git (push)

		3. 最後の .git ファイルのパスを指定せずに同じ URL を使用してリモートを更新します (git remote set-url origin https://myservice.scm.azure-mobile.net)。
		4. 配信元からプルして正常に機能していることを確認します。

これで、モバイル サービスが Azure の新しいリージョンに復旧され、元の URL を使用して、ストア アプリからトラフィックを受け取っている状態になります。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Azure SQL Database におけるビジネス継続性]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Azure SQL データベースにおけるビジネス継続性]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/
[Github]: https://github.com/
[ソース管理機能]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[Azure CLI を使用]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[Azure クラシック ポータル]: http://manage.windowsazure.com/
[Azure サービス ダッシュボード]: http://www.windowsazure.com/support/service-dashboard/
[Azure CLI を使用したモバイル サービスの自動化]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/

<!---HONumber=AcomDC_0211_2016-->