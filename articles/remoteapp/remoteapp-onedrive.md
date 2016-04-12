<properties
   pageTitle="OneDrive for Business と Azure RemoteApp を統合する方法 | Microsoft Azure"
   description="OneDrive for Business と Azure RemoteApp を使用する方法について説明します。"
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="03/31/2016"
   ms.author="elizapo"/>

# OneDrive for Business と Azure RemoteApp を統合する方法

Azure RemoteApp では、OneDrive for Business をファイル リポジトリとして使用できます。OneDrive for Business は、すべてのデバイスとワークスペースでファイルを同期した状態にしておくための優れた方法です。ユーザーの[ユーザー プロファイル ディスク](remoteapp-upd.md) (UPD) は、ユーザーが Azure RemoteApp アプリのファイルを格納できる場所ですが、これらのファイルには Azure RemoteApp を使用しないとアクセスできません。一方、OneDrive for Business を使用すると、ユーザーはいつでもどこでもファイルにアクセスできます。Azure RemoteApp を経由する必要はありません。この記事では、サポートされている OneDrive for Business のバージョンと、管理者が Azure RemoteApp 用に OneDrive for Business をセットアップする際のさまざまな方法について説明します。

## OneDrive のサポートされているすべてのバージョン

OneDrive には、OneDrive と OneDrive for Business という 2 つのバージョンがあります。Azure RemoteApp では、OneDrive for Business のみがサポートされています。個人用の OneDrive は機能しますが、公式にはサポートされていません。また、Azure RemoteApp (および RDSH/Citrix/ターミナル サーバー) でサポートされているのは、OneDrive for Business の最新バージョン (次世代同期クライアントとも呼ばれます) のみです。

>[AZURE.NOTE]  OneDrive (コンシューマー/個人向けエディション) は、Azure RemoteApp ではサポートされていません。OneDrive for Business にも、Windows Server 上での動作が確認されていないためにサポートされていないバージョンがあります。新しいクライアント (次世代同期クライアント) と以前の Groove バージョンの両方が Azure RemoteApp で正常に動作するように見えるかもしれませんが、[https://support.microsoft.com/ja-jp/kb/2965687](https://support.microsoft.com/kb/2965687) で説明されているように、以前の同期エンジンは Citrix/ターミナル サーバー (Windows Server) では機能が完全ではありません。

## OneDrive for Business のさまざまなセットアップ オプション

- **OneDrive for Business 同期エンジンの従来のセットアップ:** 現時点で、このオプションは Azure RemoteApp、RDSH、Citrix デプロイでサポートされていません。
- **OneDrive for Business の "仮想化"/ローカル ファット クライアントからセッションへのリダイレクト:** OneDrive をクライアント デバイスのドライブのフォルダーに同期している場合、そのドライブを Azure RemoteApp に[リダイレクト](remoteapp-redirection.md)することを選択できます。そのドライブはすべてのユーザーのクライアントで同じであり、すべてのユーザーの OneDrive はそのドライブのフォルダーに同期された状態になります。ユーザーが他のクライアントから RemoteApp にアクセスしている場合、これらのファイルは利用できない可能性があります (回避方法 - ユーザーはオンライン バージョンの OneDrive を使用すれば、いつでもファイルにアクセスできます)。 
- **ファイルをキャッシュ/同期せずに Azure RemoteApp 環境内で OneDrive for Business をドライブ化:** (OneDrive for Business の HTTP URL を VM 上のドライブにマップ) RDSH 環境内で OneDrive for Business をネットワーク ドライブにマップすることが可能です。これを使用できるシナリオは次のとおりです。 
	- Azure RemoteApp へのアクセスにシン クライアント (ローカル ストレージなし) が使用される場合。アプリケーションはファイルが OneDrive for Business に格納されることを必要としますが、ローカルにあるように "見える" ことが望ましく、管理者はファイルを VM に同期することを望みません。
	- 同期するように選択されている多数の大きなファイルが OneDrive for Business に存在する場合。同期のワークロードを考慮すると、ユーザーがファイルを使用する際に、すべてのファイルが同期されない場合があります。また、ファイルの合計サイズが 50 GB を超える場合は、ファイルを UPD に格納できません。

上記のシナリオでは、管理者は VM 上のドライブをユーザーの OneDrive for Business の HTTP URL にマップするオプションを選ぶことができます。次のようなオプションで、これを有効にできます。

- Office バイナリをイメージに置き、OneDrive for Business 同期エンジンをアクティブにしない。
- Office バイナリをイメージに置かない。この場合、デスクトップ エクスペリエンス パックが前提条件となります。具体的には、WebClient サービス (WebDAV とも呼ばれます) をデスクトップ エクスペリエンス パックの一部としてインストールする必要があります。 

### Windows Server 2012 R2 にデスクトップ エクスペリエンス パックをインストールする
デスクトップ エクスペリエンス パックをインストールするには、次の手順に従います。

1. サーバー マネージャーで、**[管理]、[役割と機能の追加]** の順にクリックします。
2. **[機能]** をクリックし、**[ユーザー インターフェイスとインフラストラクチャ]、[デスクトップ エクスペリエンス]** の順にクリックします。

### OneDrive for Business の URL にドライブをマップする

サポート記事 ([https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712)) の指示に従います。
 
セットアップで重要な手順は、**[サインインしたままにする]** を選択することです。

大まかな手順は次のとおりです。

1.	ドライブの URL を探します。ドライブ マッピングに使用する URL は、オンラインで OneDrive for Business のホーム ディレクトリを参照したときに取得できる URL です。次に例を示します。
 
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.	RemoteApp セッションでブラウザーを使用してその URL を開き、**[サインインしたままにする]** を選択してから、自分のアカウントで URL にログインします。
3.	エクスプローラーを開き、上記の URL にドライブをマップします。URL が解決されない場合は、次のような短い形式を使用できます。
	
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com

	これで、マップされたドライブが直ちに作成されます。ドライブは次のようになります。
 
	![OneDrive for Business as a mapped network drive](./media/remoteapp-onedrive/ra-mappeddrive.png)

<!---HONumber=AcomDC_0406_2016-->