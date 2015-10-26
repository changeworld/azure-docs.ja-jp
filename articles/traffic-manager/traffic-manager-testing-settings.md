<properties 
   pageTitle="Traffic Manager の設定のテスト | Microsoft Azure"
   description="この記事は、Traffic Manager の設定のテストを支援します。"
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />

# Traffic Manager の設定のテスト

Traffic Manager の設定をテストするには、複数のクライアントを設定してから、プロファイル内の、クラウド サービスと Web サイトで構成されるエンドポイントを一度に 1 つずつ停止するのが最善の方法です。以下の各ヒントでは、Traffic Manager プロファイルをテストする方法について説明しています。

## 基本的なテスト手順

- **DNS TTL を非常に短い時間に設定**して、変更がすぐに反映されるようにします。たとえば 30 秒に設定します。
- テストするプロファイル内の **Azure Cloud Services や Azure Websites の IP アドレスを確認します**。
- **DNS 名を IP アドレスに解決**してそのアドレスを表示できるツールを使用します。会社のドメイン名がプロファイル内のエンドポイントの IP アドレスに解決されることを確認します。その名前解決は Traffic Manager プロファイルと同じトラフィック ルーティング方法で行われる必要があります。Windows を実行しているコンピューターでは、コマンド プロンプトまたは Windows PowerShell プロンプトから Nslookup.exe ツールを使用できます。他にも、インターネット上には IP アドレスを調べることができるツールがあり、すぐに使用できる状態で公開されています。

### nslookup を使用して Traffic Manager プロファイルを確認するには

1. 管理者として、コマンド プロンプトまたは Windows PowerShell プロンプトを開きます。
2. 「`ipconfig /flushdns`」と入力して、DNS リゾルバー キャッシュをフラッシュします。
3. 「`nslookup <your Traffic Manager domain name>`」と入力します。たとえば、nslookup myapp.contoso.trafficmanager.net というコマンドは、プレフィックスが *myapp.contoso* のドメインを確認します。通常、結果として次の情報が表示されます。
   - この Traffic Manager ドメイン名を解決するためにアクセスされる DNS サーバーの DNS 名と IP アドレス。
   - コマンド ラインで "nslookup" の後に入力した Traffic Manager ドメイン名と、その Traffic Manager ドメインから解決された IP アドレス。この 2 番目の IP アドレスを確認することが重要です。この IP アドレスは、テスト対象の Traffic Manager プロファイルに含まれるいずれかのクラウド サービスまたは Web サイトのパブリック仮想 IP (VIP) アドレスと一致している必要があります。

## トラフィック ルーティング方法のテスト

### フェールオーバーによるトラフィック ルーティング方法をテストするには

1. すべてのエンドポイントを稼働状態にします。
2. 1 つのクライアントを使用します。
3. Nslookup.exe ツールまたは同様のユーティリティを使用して、会社のドメイン名の DNS 解決を要求します。
4. 取得した解決済み IP アドレスがプライマリ エンドポイントの IP アドレスに一致することを確認します。
5. プライマリ エンドポイントをダウンさせるか監視ファイルを削除することで、Traffic Manager によってそのエンドポイントがダウンしていると認識されるようにします。
6. Traffic Manager プロファイルの DNS 存続時間 (TTL) と追加の 2 分間待ちます。たとえば、DNS TTL が 300 秒 (5 分) の場合は、7 分待つ必要があります。
7. DNS クライアント キャッシュをフラッシュし、DNS 解決を要求します。Windows では、コマンド プロンプトまたは Windows PowerShell プロンプトで発行した ipconfig /flushdns コマンドで、DNS キャッシュをフラッシュできます。
8. 取得した IP アドレスがセカンダリ エンドポイントの IP アドレスに一致することを確認します。
9. 2 番目のエンドポイントをダウンさせて、次は 3 番目のエンドポイントをダウンさせてという具合に、ここまでの手順を繰り返します。手順を実行するたびに、DNS 解決によって、一覧内の次のエンドポイントの IP アドレスが返されることを確認します。すべてのエンドポイントを停止したら、プライマリ エンドポイントの IP アドレスが再度返されます。

### ラウンド ロビンによるトラフィック ルーティング方法をテストするには

1. すべてのエンドポイントを稼働状態にします。
2. 1 つのクライアントを使用します。
3. Nslookup.exe ツールまたは同様のユーティリティを使用して、会社のドメインの DNS 解決を要求します。
4. 取得した IP アドレスが一覧内の IP アドレスのいずれかに一致することを確認します。
5. DNS クライアント キャッシュをフラッシュし、手順 3. および 4. を何度も繰り返します。返された IP アドレスが、各エンドポイントに対応していることを確認します。このような処理を繰り返します。

### パフォーマンスによるトラフィック ルーティング方法をテストするには

パフォーマンスによるトラフィック ルーティング方法を効果的にテストするには、世界各地のクライアントが必要になります。1 つの方法として、Azure 上にクライアントを作成し、それらのクライアントから会社のドメイン名を使用してサービスの呼び出しをテストできます。または、世界各国に事業所がある会社の場合は、他の地域にあるクライアントにリモート ログインし、そらのクライアントからテストを行うことができます。

無料の Web ベースの DNS 検索と検出サービスを利用できます。これらのサービスの中には、さまざまな場所から DNS 名前解決を確認できるものもあります。たとえば、"DNS 検索" の検索などを実行できます。さらに、Gomez や Keynote のようなサード パーティ製ソリューションもあります。このようなソリューションを使用して、トラフィックがプロファイルに基づいて予期したとおりに振り分けられることを確認できます。

## 関連項目

[Traffic Manager のトラフィック ルーティング方法について](traffic-manager-load-balancing-methods.md)

[Traffic Manager - プロファイルの無効化、有効化、または削除](disable-enable-or-delete-a-profile.md)

[Traffic Manager - エンドポイントの無効化または有効化](disable-or-enable-an-endpoint.md)

[Traffic Manager について](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Traffic Manager の操作 (REST API リファレンス)](http://go.microsoft.com/fwlink/?LinkId=313584)

 

<!---HONumber=Oct15_HO3-->