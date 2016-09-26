<properties
	pageTitle="Azure AD アプリケーション プロキシ コネクタの使用 | Microsoft Azure"
	description="Azure AD アプリケーション プロキシにおけるコネクタのグループの作成と管理の方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2016"
	ms.author="kgremban"/>


# コネクタ グループを使用して別のネットワークや場所にアプリケーションを発行する

> [AZURE.SELECTOR]
- [Azure ポータル](active-directory-application-proxy-connectors-azure-portal.md)
- [Azure クラシック ポータル](active-directory-application-proxy-connectors.md)


コネクタ グループは、次のようなさまざまなシナリオで役立ちます。

- 複数のデータセンターが相互接続されたサイト。この場合、データセンター間リンクは一般的にコストがかかり低速であるため、できるだけ多くのトラフィックをデータセンター内に留める必要があります。各データセンターにコネクタをデプロイし、データセンター内にあるアプリケーションのみにサービスを提供できます。この方法で、データセンター間のリンクを最小限に抑えて、ユーザーに完全に透過的なエクスペリエンスを提供できます。
- メインの企業ネットワークに属していない分離されたネットワークにインストールされているアプリケーションの管理。コネクタ グループを使用して、分離されたネットワークに専用のコネクタをインストールし、そのネットワークに対してアプリケーションの分離も実現できます。
- クラウド アクセスを実現するためにアプリケーションが IaaS 上にインストールされている場合。コネクタ グループは、すべてのアプリケーションへのアクセスをセキュリティで保護する共通サービスを提供します。コネクタ グループによって、企業ネットワークに対する追加の依存関係が生じることも、アプリケーション エクスペリエンスが分断されることもありません。各クラウド データセンターにコネクタをインストールし、このネットワーク上にあるアプリケーションにのみサービスを提供できます。複数のコネクタをインストールして、高可用性を実現できます。
- マルチ フォレスト環境のサポート。フォレストごとに専用のコネクタをデプロイし、特定のアプリケーションにサービスを提供するよう設定できます。
- 障害復旧サイトでコネクタ グループを使用してフェールオーバーを検出したり、メイン サイトのバックアップとして使用したりできます。
- コネクタ グループを使用して、シングル テナントから複数の企業にサービスを提供できます。

## 前提条件: コネクタの作成
コネクタをグループ化するには、[複数のコネクタがインストールされている](active-directory-application-proxy-enable.md)ことを確認し、それらに名前を付けてから、グループ化します。最後に、特定のアプリに割り当てる必要があります。

## 手順 1: コネクタ グループを作成する
必要な数のコネクタ グループを作成します。コネクタ グループの作成は、Azure クラシック ポータルで実行します。

1. ディレクトリを選択し、**[構成]** をクリックします。![アプリケーション プロキシの構成のスクリーンショット - [コネクタ グループの管理] をクリック](./media/active-directory-application-proxy-connectors/app_proxy_connectors_creategroup.png)

2. [アプリケーション プロキシ] の **[コネクタ グループの管理]** をクリックし、グループに名前を付けて、新しいコネクタ グループを作成します。![アプリケーション プロキシ コネクタ グループのスクリーンショット - 新しいグループに名前を付ける](./media/active-directory-application-proxy-connectors/app_proxy_connectors_namegroup.png)

## 手順 2: コネクタをグループに割り当てる
コネクタ グループを作成したら、コネクタを適切なグループに移動します。

1. **[アプリケーション プロキシ]** で、**[コネクタの管理]** をクリックします。
2. **[グループ]** で、コネクタごとに必要なグループを選択します。コネクタが新しいグループでアクティブになるのに最大 10 分かかる場合があります。![アプリケーション プロキシ コネクタのスクリーンショット - ドロップダウン メニューからグループを選択](./media/active-directory-application-proxy-connectors/app_proxy_connectors_connectorlist.png)

## 手順 3: コネクタ グループにアプリケーションを割り当てる
最後の手順は、アプリケーションにサービスを提供するコネクタ グループへの各アプリケーションの設定です。

1. Azure クラシック ポータルのディレクトリで、グループに割り当てるアプリケーションを選択し、**[構成]** をクリックします。
2. **[コネクタ グループ]** で、アプリケーションで使用するグループを選択します。この変更は直ちに適用されます。![アプリケーション プロキシ コネクタグループのスクリーンショット - ドロップダウン メニューからグループを選択](./media/active-directory-application-proxy-connectors/app_proxy_connectors_newgroup.png)


## 関連項目

- [アプリケーション プロキシを有効にする](active-directory-application-proxy-enable.md)
- [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
- [条件付きアクセスを有効にする](active-directory-application-proxy-conditional-access.md)
- [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)

最新のニュースと更新プログラムについては、[アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)をご覧ください。

<!---HONumber=AcomDC_0914_2016-->