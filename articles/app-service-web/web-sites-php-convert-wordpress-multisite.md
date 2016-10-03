<properties 
	pageTitle="Azure App Service での WordPress から Multisite への変換" 
	description="Azure のギャラリーを使って作成された既存の WordPress Web アプリを取得し、WordPress Multisite に変換する方法を説明します。" 
	services="app-service\web" 
	documentationCenter="php" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="08/11/2016" 
	ms.author="robmcm"/>



# Azure App Service での WordPress から Multisite への変換

## Overview

*執筆者: [Ben Lobaugh][ben-lobaugh] \([Microsoft Open Technologies Inc.][ms-open-tech]*)

このチュートリアルでは、Azure のギャラリーを使って作成された既存の WordPress Web アプリを取得し WordPress Multisite インストールに変換する方法を説明します。また、インストール内の各サブサイトにカスタム ドメインを割り当てる方法も説明します。

ここでは、WordPress のインストールが既にあるものとします。ない場合は、「[Azure でギャラリーから WordPress Web サイトを作成する][website-from-gallery]」の説明に従ってください。

既存の WordPress シングル サイト インストールから Multisite への変換は、通常はごく簡単で、ここでの初期手順の多くは [WordPress Codex][wordpress-codex-create-a-network] の「[Create A Network (ネットワークの作成)](http://codex.wordpress.org)」から引用したものです。

それでは始めましょう。

## Multisite の許可

まず、`wp-config.php` ファイルの **WP\_ALLOW\_MULTISITE** 定数を使用して、Multisite を有効にする必要があります。Web アプリ ファイルを編集するには、2 つの方法があります。FTP を使用する方法と、Git を使用する方法です。どちらのセットアップ方法にも慣れていない場合は、以下のチュートリアルを参照してください。

* [MySQL および FTP による PHP Web サイト][website-w-mysql-and-ftp-ftp-setup]

* [MySQL および Git による PHP Web サイト][website-w-mysql-and-git-git-setup]

任意のエディターで `wp-config.php` ファイルを開き、以下の行を `/* That's all, stop editing! Happy blogging. */` 行の上に追加します。

	/* Multisite */

	define( 'WP_ALLOW_MULTISITE', true );

必ずファイルを保存し、サーバーにアップロードし直してください。

## ネットワークのセットアップ

Web アプリの *wp-admin* 領域にログインし、**[ツール]** メニューに **[Network Setup]** という新しい項目があることを確認します。**[Network Setup]** をクリックし、ネットワークの詳細を入力します。

![[Network Setup] 画面][wordpress-network-setup]

このチュートリアルでは、*Sub-directories* サイト スキーマを使用します。このスキーマは常に機能する必要があるためです。このチュートリアルの後の方では、各サブサイトのカスタム ドメインをセットアップします。ただし、[Azure ポータル](https://portal.azure.com)を通じてドメインをマップし、ワイルドカード DNS を適切にセットアップすると、サブドメイン インストールをセットアップすることができます。

サブドメインのセットアップとサブディレクトリのセットアップの詳細については、WordPress Codex の「[Types of multisite network (マルチサイト ネットワークの種類)][wordpress-codex-types-of-networks]」を参照してください。

## ネットワークの有効化

これで、ネットワークがデータベースで構成されましたが、ネットワーク機能を有効にするにはもう 1 つ手順が残っています。`wp-config.php` 設定の最終処理を行い、`web.config` が各サイトを適切にルーティングしていることを確認します。


*[ネットワーク セットアップ]* ページで **[インストール]** ボタンをクリックすると、`wp-config.php` ファイルと `web.config` ファイルが自動的に更新されます。ただし、これらのファイルを必ずチェックし、正常に更新されていることを確認してください。正常に更新されていない場合は、この画面に必要な更新が表示されます。ファイルを編集して保存します。


これらの更新を行った後で、いったんログアウトしてから、この wp-admin ダッシュボードに再ログインする必要があります。

この時点で、管理バーに **[My Sites]** というメニューが追加されています。このメニューを使用すると、**Network Admin** ダッシュボードを通じて新しいネットワークを管理できます。

## カスタム ドメインの追加

[WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] プラグインを利用すると、ネットワーク上のサイトにカスタム ドメインを簡単に追加できます。このプラグインを正常に機能させるには、ポータルとドメイン レジストラーで追加のセットアップを行う必要があります。

## Web アプリへのドメイン マッピングの有効化

**無料** [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) プラン モードでは Web Apps へのカスタム ドメインの追加はサポートされません。**Shared** モードまたは **Standard** モードに切り替える必要があります。これを行うには、次の手順を実行します。

* Azure ポータルにログインして、自分の Web アプリに移動します。
* **[設定]** で **[スケールアップ]** タブをクリックします。
* **[全般]** で、*[Shared]* または *[Standard]* を選択します。
* **[保存]** をクリックします。

設定してある使用方法やその他の構成によっては、変更の確認と、Web アプリへの今後の課金の承認を求めるメッセージが表示される場合があります。

新しい設定の処理には少し時間がかかるので、ドメインのセットアップを開始します。

## ドメインの確認

Azure Web Apps でドメインをサイトにマップするには、まず、ドメインをマップする権限が与えられていることを確認する必要があります。そのためには、新しい CNAME レコードを DNS エントリに追加します。

* 自分のドメインの DNS マネージャーにログインします
* 新しい CNAME *awverify* を作成します
* *awverify* が *awverify.YOUR\_DOMAIN.azurewebsites.net* を指すようにします

場合によっては、DNS の変更が完全に有効になるまで少し時間がかかります。以下の手順がすぐにはうまく動作しない場合は、しばらくしてからやり直してください。

## Web アプリへのドメインの追加

Azure ポータルを通じて Web アプリに戻り、**[設定]** をクリックし、**[Custom domains and SSL]** をクリックします。

*SSL 設定*が表示されると、Web アプリに割り当てるすべてのドメインを入力するためのフィールドが表示されます。ドメインがこの一覧に表示されない場合、ドメイン DNS がどのようにセットアップされているかにかかわらず、そのドメインは WordPress 内でのマッピングに使用できません。

![[カスタム ドメインの管理] ダイアログ][wordpress-manage-domains]

ドメインをテキスト ボックスに入力すると、以前に作成した CNAME レコードが確認されます。DNS が完全に反映されていない場合は、赤いインジケーターが表示されます。成功した場合は、緑のチェック マークが表示されます。

ダイアログの下部に表示される IP アドレスをメモしておきます。ドメインのための A レコードをセットアップするときに必要になります。

## ドメイン A レコードのセットアップ

ここまでの手順が成功すると、DNS A レコードを通じてドメインを自分の Azure Web アプリに割り当てられるようになります。

ここで重要なのは、Azure Web Apps では CNAME と A レコードのどちらも許容されるものの、適切なドメイン マッピングを可能にするには A レコードを使う*必要*があるということです。Azure では、YOUR\_DOMAIN.azurewebsites.net で CNAME が作成されますが、CNAME は他の CNAME に転送することができません。

DNS マネージャーに戻り、前の手順でメモした IP アドレスを指すように A レコードをセットアップします。


## プラグインのインストールとセットアップ

現在、WordPress Multisite には、カスタム ドメインをマップするための方法が組み込まれていません。ただし、[WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] というプラグインを使用すると、この機能を追加することができます。自分のサイトの Network Admin 部分にログインして、**WordPress MU Domain Mapping** プラグインをインストールします。

プラグインをインストールおよびアクティブ化した後で、**[設定]** > **[Domain Mapping]** にアクセスし、プラグインを構成します。1 つ目のテキスト ボックス *[Server IP Address]* で、ドメインの A レコードのセットアップに使用した IP アドレスを入力します。必要に応じて *[Domain Options]* を設定し (通常は既定のままでよい)、**[Save]** をクリックします。

## ドメインのマップ

ドメインのマップ対象のサイトの**ダッシュボード**にアクセスします。**[ツール]**、**[Domain Mapping]** の順にクリックし、テキスト ボックスに新しいドメインを入力して、**[Add]** をクリックします。

既定では、新しいドメインは自動生成されたサイト ドメインに書き換えられます。すべてのトラフィックが新しいドメインに送信されるようにするには、保存する前に *[Primary domain for this blog]* チェック ボックスをオンにします。サイトに追加するドメインの数に制限はありませんが、プライマリにできるのは 1 つだけです。

## 繰り返し

Azure Web Apps では、Web アプリに追加できるドメインの数に制限はありません。他のドメインを追加するには、各ドメインに対して「**ドメインの確認**」および「**ドメイン A レコードのセットアップ**」セクションの手順を繰り返します。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、「[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 

<!---HONumber=AcomDC_0817_2016-->