<properties 
   pageTitle="Traffic Manager の設定のテスト"
   description="この記事は、Traffic Manager の設定のテストを支援します。"
   services="traffic-manager"
   documentationCenter=""
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/27/2015"
   ms.author="cherylmc" />

# Traffic Manager の設定のテスト

Traffic Manager の設定をテストする最良の方法は、複数のクライアントを設定し、プロファイル内のクラウド サービスと Web サイトから構成されるエンドポイントを一度に 1 つずつダウンさせることです。以下に、Traffic Manager の設定をテストするために役立つヒントを示します。

## 基本的なテスト手順

- **DNS TTL を非常に短い時間に設定**して、変更がすぐに反映されるようにします。たとえば 30 秒に設定します。
- **テストするプロファイル内の Azure クラウド サービスおよび Web サイトの IP アドレスを確認**します。
- **DNS 名を IP アドレスに解決**してそのアドレスを表示できるツールを使用します。会社のドメイン名がプロファイル内のエンドポイントの IP アドレスに解決されることを確認します。その名前解決は Traffic Manager プロファイルと同じ負荷分散方式で行われる必要があります。Windows を実行するコンピューターの場合は、コマンドや Windows PowerShell プロンプトから Nslookup.exe ツールを使用できます。他にも、インターネット上には IP アドレスを調べることができるツールがあり、すぐに使用できる状態で公開されています。

### nslookup コマンドを使用して Traffic Manager プロファイルを確認するには

1. 管理者として、コマンド プロンプトまたは Windows PowerShell プロンプトを開きます。
2.  `ipconfig /flushdns` と入力して、DNS リゾルバー キャッシュをフラッシュします。
3.  `nslookup <your Traffic Manager domain name>` と入力します。たとえば、次のコマンドは、プレフィックスが  *myapp.contoso* のドメイン名を確認します。
    nslookup myapp.contoso.trafficmanager.net
   通常、結果として次の情報が表示されます。
   - この Traffic Manager ドメイン名を解決するためにアクセスされる DNS サーバーの DNS 名と IP アドレス
   - コマンド ラインで "nslookup" の後に入力したトラフィック マネージャー ドメイン名と、そのトラフィック マネージャー ドメインから解決された IP アドレス。この 2 番目の IP アドレスを確認することが重要です。この IP アドレスが、テスト対象のトラフィック マネージャー プロファイル内のいずれかのクラウド サービスまたは Web サイトのパブリック仮想 IP (VIP) に一致する必要があります。

## 負荷分散方法のテスト

### フェールオーバー負荷分散方法をテストするには

1. すべてのエンドポイントを稼働状態にします。
2. 1 つのクライアントを使用します。
3. Nslookup.exe ツールまたは同様のユーティリティを使用して、会社のドメイン名の DNS 解決を要求します。
4. 取得した解決済み IP アドレスがプライマリ エンドポイントの IP アドレスに一致することを確認します。
5. プライマリ エンドポイントをダウンさせるか監視ファイルを削除することで、Traffic Manager によってそのエンドポイントがダウンしていると認識されるようにします。
6. Traffic Manager プロファイルの DNS 存続時間 (TTL) と追加の 2 分間待ちます。たとえば、DNS TTL が 300 秒 (5 分) の場合は、7 分待つ必要があります。
7. DNS クライアント キャッシュをフラッシュし、DNS 解決を要求します。Windows では、コマンド プロンプトまたは Windows PowerShell プロンプトで発行される ipconfig/flushdns コマンドで、DNS キャッシュをフラッシュできます。
8. 取得した IP アドレスがセカンダリ エンドポイントの IP アドレスに一致することを確認します。
9. 2 番目のエンドポイントをダウンさせて、次は 3 番目のエンドポイントをダウンさせてという具合に、ここまでの手順を繰り返します。毎回、DNS 解決によって一覧内の次のエンドポイントの IP アドレスが返されることを確認します。すべてのエンドポイントをダウンさせたら、プライマリ エンドポイントの IP アドレスが再度返されます。

### ラウンド ロビン負荷分散方法をテストするには

1. すべてのエンドポイントを稼働状態にします。
2. 1 つのクライアントを使用します。
3. Nslookup.exe ツールまたは同様のユーティリティを使用して、会社のドメインの DNS 解決を要求します。
4. 取得した IP アドレスが一覧内の IP アドレスのいずれかに一致することを確認します。
5. DNS クライアント キャッシュをフラッシュし、手順 3. および 4. を何度も繰り返します。各エンドポイントのさまざまな IP アドレスが順番に返されます。このような処理が繰り返されます。

### パフォーマンス負荷分散方法をテストするには

パフォーマンス負荷分散方法を効果的にテストするには、世界中のさまざまな地域にあるクライアントが必要です。1 つの方法として、Azure 上にクライアントを作成し、それらのクライアントから会社のドメイン名を使用してサービスの呼び出しをテストできます。または、世界各国に事業所がある会社の場合は、世界の他の地域にあるクライアントにリモート ログインし、そらのクライアントからテストを行うことができます。

無料で利用できる Web ベースの DNS 参照および dig サービスがあります。これらのサービスの中には、さまざまな場所から DNS 名前解決を確認できるものもあります。たとえば、「DNS 参照」で検索してください。さらに、Gomez や Keynote のようなサード パーティ製ソリューションもあります。このようなソリューションを使用して、トラフィックがプロファイルに基づいて予期したとおりに振り分けられることを確認できます。

## 関連項目

[Traffic Manager の負荷分散方法について](traffic-manager-load-balancing-methods.md)

[Traffic Manager を構成する方法](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Traffic Manager の概要](traffic-manager-overview.md)

[クラウド サービス](http://go.microsoft.com/fwlink/p/?LinkId=314074)

[Web サイト](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Traffic Manager の操作 (REST API リファレンス)](http://go.microsoft.com/fwlink/?LinkId=313584)


<!--HONumber=49-->