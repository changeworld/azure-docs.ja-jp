<properties 
	pageTitle="Azure App Service での Web アプリの使用 - 第 2 部" 
	description="App Service で Web アプリに運用上の重要な機能を数回のクリックで追加します。" 
	services="app-service\web"
	documentationCenter=""
	authors="cephalin" 
	manager="wpickett" 
	editor="" 
/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="03/14/2016" 
	ms.author="cephalin"
/>

# Azure App Service の使用 - 第 2 部

「[Azure App Service の使用](app-service-web-get-started.md)」では、Azure App Service に Web アプリをデプロイしました。すべての要素がセットアップされているため、更新を短時間で簡単に実行できます。この記事では、デプロイしたアプリにいくつかの重要な機能をわずかな操作で追加して、スムーズに機能させます。この操作を通じて、App Service が Web サイト ホストよりもはるかに高機能であることがおわかりいただけると思います。App Service によってエンタープライズクラスの機能がアプリに組み込まれるため、開発者はセキュリティ、スケーラビリティ、パフォーマンス、管理といった実環境の要求を満たすことではなく、アプリでユーザーの満足度を高めることに創造的エネルギーを集中することができます。

数回のクリックで次の内容を行う方法を学習します。

- ユーザーへの認証の強制
- アプリの自動スケール
- アプリのパフォーマンスに関するアラートの取得

前の記事でどのサンプル アプリをデプロイした場合でも、以下の手順を実行できます。

## ユーザーの認証

アプリへの認証の追加がどれほど簡単に実現できるかを見てみましょう。

1. アプリのブレードを開いて、**[設定]**、**[認証/承認]** の順にクリックします。 ![Authenticate - settings blade](./media/app-service-web-get-started/aad-login-settings.png)
    
2. **[オン]** をクリックして認証を有効にします。 ![Authenticate - turn on](./media/app-service-web-get-started/aad-login-auth-on.png)
    
4. **[認証プロバイダー]** で、**[Azure Active Directory]** をクリックします。 ![Authenticate - select Azure AD](./media/app-service-web-get-started/aad-login-config.png)

5. **[Azure Active Directory 設定]** ブレードで **[Express]**、**[OK]** の順にクリックします。新しい Azure AD アプリケーションが既定の設定で既定のディレクトリに作成されます。 ![Authenticate - express configuration](./media/app-service-web-get-started/aad-login-express.png)

6. **[Save]** をクリックします。![Authenticate - save configuration](./media/app-service-web-get-started/aad-login-save.png)

    変更が正常に完了すると、通知ベルが緑色に変わり、わかりやすいメッセージが表示されます。

7. アプリのメイン ブレードに戻り、**[URL]** リンク (またはメニュー バーの **[参照]**) をクリックします。このリンクは HTTP アドレスになっています。![Authenticate - browse to URL](./media/app-service-web-get-started/aad-login-browse-click.png) ただし、URL をクリックして新しいタブでアプリが開くと、数回のリダイレクトを経て、最終的には HTTPS アドレスでアプリが開かれます。結果として、Azure サブスクリプションのある Microsoft アカウントにログイン済みの状態になり、そのアカウントを使用してアプリに自動的にログインされます。![Authenticate - logged in](./media/app-service-web-get-started/aad-login-browse-http-postclick.png) そのため、(まだログインしていないことを確認するために) 別のブラウザーを開いて、同じアプリの URL に移動すると、ログイン画面が表示されます。 ![Authenticate - login page](./media/app-service-web-get-started/aad-login-browse.png) Azure Active Directory で何も実行していない場合は、既定のディレクトリに Azure AD ユーザーが含まれていない可能性があります。その場合、既定のディレクトリに含まれているのは、Azure サブスクリプションのある Microsoft アカウントのみになります。前述のとおり、同一のブラウザーでアプリに自動的にログインされる場合があるのは、このためです。その同じ Microsoft アカウントを使用して、このログイン ページにもログインできます。

これで、サイトへのすべてのトラフィックが認証されます。

お気付きかもしれませんが、**[認証/承認]** ブレードでは、他にも次のような多数の操作を行うことができます。

- ソーシャル ログインを有効にする
- さまざまなログイン オプションを有効にする
- ユーザーが最初にアプリに移動したときの既定の動作を変更する

App Service には一般的な認証のニーズの一部に対応した設定不要のソリューションが用意されているため、自分で認証ロジックを提供する必要がありません。詳細については、「[...](/services/app-service/)」を参照してください。

## アプリのスケールアップとスケールアウト

次に、アプリのスケーリングについて見てみましょう。App Service アプリをスケールする方法は 2 つあります。

- [スケールアップ](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): App Service アプリをスケールアップするには、アプリが属する App Service プランの価格レベルを変更します。スケールアップすると、CPU、メモリ、およびディスク領域が増えるほか、専用の VM インスタンス、自動スケール、99.95% の SLA、カスタム ドメイン、カスタム SSL 証明書、デプロイ スロット、バックアップと復元などの追加機能が利用できるようになります。レベルが上がるほど、App Service アプリで利用できる機能も増えます。  
- [スケールアウト](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): App Service アプリをスケールアウトするには、対象のアプリ (または同じ App Service プラン内の他のアプリ) が実行される VM インスタンスの数を変更します。Standard レベル以上では、パフォーマンス メトリックに基づく VM インスタンスの自動スケールを有効にすることができます。 

前置きはこのくらいにして、アプリの自動スケールを設定しましょう。

1. 最初に、スケールアップして自動スケールを有効にしましょう。アプリのブレードで、**[設定]**、**[スケール アップ (App Service のプラン)]** の順にクリックします。 ![Scale up - settings blade](./media/app-service-web-get-started/scale-up-settings.png)

2. スクロールして、自動スケールをサポートするレベルのうち最も下位のレベルである (下図の丸で囲んだところ) **[S1 Standard]** レベルを選択し、**[選択]** をクリックします。このレベルでは無料試用版のクレジットが消費されるので注意してください。 ![Scale up - choose tier](./media/app-service-web-get-started/scale-up-select.png)

    これでスケールアップは完了です。
    
3. 次に、自動スケールを構成します。アプリのブレードで、**[設定]**、**[スケール アウト (App Service のプラン)]** の順にクリックします。 ![Scale out - settings blade](./media/app-service-web-get-started/scale-out-settings.png)

4. **[スケールの基準]** を **[CPU の割合]** に変更します。ドロップダウン リストの下にあるスライダーをそれぞれ変更します。次に、**[インスタンス]** の範囲を **1** ～ **2** に、**[対象範囲]** を **40** ～ **80** に、それぞれボックスに入力するかスライダーを動かして、定義します。 ![Scale out - configure autoscaling](./media/app-service-web-get-started/scale-out-configure.png)
    
    アプリは、この構成に基づいて、CPU 使用率が 80% を超えると (最大 2 インスタンスまで) 自動的にスケールアウトされ、CPU 使用率が 40% を下回ると (最低 1 インスタンスまで) スケールインされます。
    
5. メニュー バーで **[保存]** をクリックします。

これで、アプリは自動スケールされます。

お気付きかもしれませんが、**[スケールの設定]** ブレードでは、他にも次のような多数の操作を行うことができます。

- インスタンスを特定の数に手動でスケールする
- メモリの割合やディスク キューなどの追加のパフォーマンス メトリックに基づいてスケールする
- パフォーマンス ルールがトリガーされたときのスケーリングの動作をカスタマイズする
- スケジュールに基づいて自動スケールする
- 将来のイベントに対して自動スケールの動作を設定する

アプリのスケールアップの詳細については、「[Azure App Service における価格レベルのスケーリング](../app-service/app-service-scale.md)」を参照してください。スケールアウトの詳細については、「[手動または自動によるインスタンス数のスケール変更](../azure-portal/insights-how-to-scale.md)」を参照してください。

## アプリのアラートを受信する

アプリが自動的にスケールアウトされるようになると、最大インスタンス数 (**Standard** レベルの場合は 10 インスタンス) に達し、CPU 使用率が目的の割合 (80%) を超えた場合はどうなるのでしょうか。 たとえば、この状況を通知するアラートを設定し、アプリをさらにスケールアップできます。この状況に対してアラートをわずかな操作で設定しましょう。

1. アプリのブレードで、**[ツール]**、**[アラート]** の順にクリックします。 ![Alerts - settings blade](./media/app-service-web-get-started/alert-settings.png)

2. **[アラートの追加]** をクリックします。次に、**[リソース]** ボックスで、**(serverfarm)** で終わるリソースを選択します。これは、ご利用の App Service プランです。 ![Alerts - add alert for App Service plan](./media/app-service-web-get-started/alert-add.png)

3. **[名前]** を `CPU Maxed` に、**[メトリック]** を **[CPU の割合]** に、**[しきい値]** を `90` に指定し、**[メールの所有者、投稿者、閲覧者]** を選択して、**[OK]** をクリックします。 ![Alerts - configure alert](./media/app-service-web-get-started/alert-configure.png)
    
    Azure でアラートの作成が完了すると、**[アラート]** ブレードにアラートが表示されます。 ![Alerts - finished view](./media/app-service-web-get-started/alert-done.png)

これで、アラートが取得されるようになりました。

このアラート設定では CPU 使用率が 5 分ごとにチェックされます。この数値が 90% を超えると、電子メール アラートが届きます。このアラートは、承認されたすべてのユーザーにも送信されます。アラートを受信する権限のあるすべてのユーザーを表示するには、アプリのブレードに戻り、**[アクセス]** ボタンをクリックします。 ![See who gets alerts](./media/app-service-web-get-started/alert-rbac.png)

**サブスクリプション管理者**が既にアプリの**所有者**であることが表示されます。Azure サブスクリプション (試用版サブスクリプションなど) のアカウント管理者もこれに含まれます。Azure のロールベースのアクセス制御の詳細については、「[Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)」を参照してください。

## 次のステップ

アラートを構成する際、**[ツール]** ブレードにツールの豊富なセットが用意されており、問題のトラブルシューティング、パフォーマンスの監視、脆弱箇所のテスト、リソースの管理、VM コンソールとのやりとり、有用な拡張機能の追加を実行できます。これらのツールをそれぞれクリックして、シンプルでありながら強力なツールを実際に確認することをお勧めします。

また、デプロイしたアプリのさらなる活用方法もご確認ください。一覧の一部を次に示します。

- [Azure App Service でのカスタム ドメイン名の購入と構成](custom-dns-web-site-buydomains-web-app.md)
- [Azure App Service の Web アプリのステージング環境を設定する](web-sites-staged-publishing.md)
- [Azure App Service での GIT による継続的なデプロイ](web-sites-publish-source-control.md)
- [Azure App Service での Web アプリのバックアップ](web-sites-backup.md)
- [Azure App Service の Web アプリの診断ログの有効化](web-sites-enable-diagnostic-log.md)
- [Azure App Service のハイブリッド接続を使用してオンプレミスのリソースにアクセスする](web-sites-hybrid-connection-get-started.md)
- [App Service の特長](../app-service/app-service-how-works-readme.md) 

<!---HONumber=AcomDC_0316_2016-->