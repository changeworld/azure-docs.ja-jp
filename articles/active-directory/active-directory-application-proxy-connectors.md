<properties
	pageTitle="Azure AD アプリケーション プロキシ コネクタの使用"
	description="Azure AD アプリケーション プロキシにおけるコネクタのグループの作成と管理の方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="StevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/13/2015"
	ms.author="rkarlin"/>


# コネクタ グループを使用して別のネットワークや場所にアプリケーションを発行する

> [AZURE.NOTE]アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。詳細については、「[Azure Active Directory のエディション](active-directory-edition.md)」をご覧ください。

コネクタ グループは、以下をはじめとする、さまざまなシナリオで役立ちます。


- 相互接続されたデータセンターがあるサイトで、一般的には高価で待機時間の長いデータセンター間リンクを使用せずに、データセンター内でできるだけ多くのトラフィックを維持する必要がある場合。各データセンターにコネクタをデプロイし、データセンター内にあるアプリケーションのみにサービスを提供できます。また、貴重なデータセンター間リンクを節約し、ユーザーに完全に透過的なエクスペリエンスを提供できます。
- メインの企業ネットワークに属していない分離されたネットワークにインストールされているアプリケーションの管理。コネクタ グループを使用して、分離されたネットワーク専用のコネクタをインストールし、そのネットワークに対してアプリケーションの分離も実現できます。
- クラウド アクセスを実現するためにアプリケーションが IaaS 上にインストールされている場合。コネクタ グループは、すべてのアプリケーションへのアクセスをセキュリティで保護する共通サービスを提供します。企業ネットワークに依存関係を追加作成する必要はなく、エクスペリエンスが分断することもありません。各クラウド データセンターにコネクタをインストールし、このネットワーク上にあるアプリケーションにのみサービスを提供できます。複数のコネクタをインストールして、高可用性を実現できます。
- マルチ フォレスト環境のサポート。フォレストごとに専用のコネクタをデプロイし、特定のアプリケーションにサービスを提供するよう設定できます。
- 障害復旧サイトでコネクタ グループを使用してフェールオーバーを検出したり、メイン サイトのバックアップとして使用したりできます。
- コネクタ グループを使用して、シングル テナントから複数の企業にサービスを提供できます。


## コネクタ グループの操作
コネクタをグループ化するには、[複数のコネクタがインストールされている](active-directory-application-proxy-enable.md)ことを確認し、それらに名前を付けてから、グループ化します。最後に、特定のアプリに割り当てる必要があります。

## 手順 1: コネクタ グループを作成する
必要な数のコネクタ グループを作成します。コネクタ グループの作成は、Azure ポータルで実行します。ディレクトリを選択し、**[構成]** をクリックします。次に、[アプリケーション プロキシ] の **[コネクタ グループの管理]** をクリックし、グループに名前を付けて、新しいコネクタ グループを作成します。![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png) ![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)
## 手順 2: コネクタをグループに割り当てる
コネクタ グループを作成したら、コネクタを適切なグループに移動します。**[アプリケーション プロキシ]** の **[コネクタの管理]** をクリックし、**[グループ]** で、コネクタごとに目的のグループを選択します。コネクタが新しいグループでアクティブになるのに最大 10 分かかる場合があります。![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)
## 手順 3: コネクタ グループにアプリケーションを割り当てる
最後の手順は、アプリケーションにサービスを提供するコネクタ グループへの各アプリケーションの設定です。Azure ポータルのディレクトリで、グループに割り当てるアプリケーションを選択し、**[構成]** をクリックします。**[コネクタ グループ]** で、アプリケーションで使用するグループを選択します。この変更は直ちに適用されます。![](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png) アプリケーションの発行に関する詳細については、「[独自のドメイン名でアプリケーションを発行する](active-directory-application-proxy-custom-domains.md)」を参照してください。
## 関連項目
アプリケーション プロキシを使ってできることは他にもたくさんあります。

- [アプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)
- [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
- [条件付きアクセスを有効にする](active-directory-application-proxy-conditional-access.md)
- [要求に対応するアプリケーションを利用する](active-directory-application-proxy-claims-aware-apps.md)
- [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)

## アプリケーション プロキシに関するその他の情報
- [オンライン ヘルプ](active-directory-application-proxy-enable.md)
- [アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9 ビデオ](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## その他のリソース

* [Kerberos の制約付き委任について](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=Oct15_HO3-->