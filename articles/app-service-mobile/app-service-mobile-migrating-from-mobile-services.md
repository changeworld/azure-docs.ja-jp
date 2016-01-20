<properties
	pageTitle="Mobile Services から App Service モバイル アプリへの移行"
	description="Mobile Services アプリケーションを App Service モバイル アプリに簡単に移行する方法について説明します。"
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="adrianhall"/>

# <a name="article-top"></a>既存の Azure Mobile Service を Azure App Service に移行する

[Azure App Service は一般公開されており]、Azure Mobile Service サイトを簡単にインプレース移行し、Azure App Service の全機能を最大限に活用できます。このドキュメントでは、Azure Mobile Service から Azure App Service にサイトを移行するときに必要な作業について説明します。

## <a name="what-does-migration-do"></a>移行がサイトにもたらすもの

Azure Mobile Service を移行すれば、コードを変更することなくモバイル サービスを [Azure App Service] アプリに変えることができます。Notification Hubs、SQL データ接続、認証設定、スケジュールされたジョブ、ドメイン名は変更されません。Azure Mobile Service を利用しているモバイル クライアントは通常どおりの動作を続けます。サービスは Azure App Service に移行されると再起動します。

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

## <a name="why-migrate"></a>サイトを移行する理由

Microsoft では、次のような Azure App Service の機能を最大限に活用できるように Azure Mobile Service の移行を推奨しています。

  *  [WebJobs] や [custom domain names] などの新しいホスト機能。
  *  [ハイブリッド接続]に加えて、[VNet] を使用したオンプレミス リソースへの接続
  *  New Relic または [Application Insights] による監視とトラブルシューティング。
  *  [ステージング スロット]、ロールバック、本稼動テストなど、組み込み DevOps ツール。
  *  [自動スケール]、負荷分散、[パフォーマンス監視]。

Azure App Service の利点の詳細については、[Mobile Services と App Service の比較]に関するトピックを参照してください。

## <a name="why-not-migrate"></a>サイトを移行しない理由

次のような場合、Azure Mobile Services を今すぐ移行するべきではありません。

  *  現在、忙しい時期にあり、現時点ではサイトを再起動する余裕がない。
  *  移行プロセスをテストする前に本稼動サイトを変更することを望まない。
  *  Free または Basic 価格レベルのサイトを複数所有しているとき、すべてのサイトを同時に移行することを望まない。

忙しい時期であれば、保守管理の予定期間中に移行を計画してください。移行プロセスの一環としてサイトが再起動します。一時的にサイトが利用できなくなる可能性があります。

この一覧のほとんどの項目には回避策があります。詳細については、「[開始する前に](#before-you-begin)」セクションを参照してください。

## <a name="before-you-begin"></a>開始する前に

サイトを移行する前に次の手順を行ってください。

  *  [モバイル サービス スクリプトをバックアップし]、SQL データベースをバックアップする
  *  (省略可能) モバイル サービス層を Standard に格上げする

本稼動サイトを移行する前に移行プロセスをテストする場合、本稼動の Azure Mobile Service を複製し (データ ソースのコピーを含む完全な複製)、新しい URL に対して移行をテストします。また、移行されたサイトを適切にテストするには、テスト サイトを指すテスト クライアント実装が必要になります。

### <a name="opt-raise-service-tier"></a>(省略可能) モバイル サービス層を Standard に格上げする

ホスティング プランを共有するすべての Mobile Services サイトは同時に移行されます。Free または Basic 価格レベルの Mobile Services は、価格レベルと [Azure リージョン]が同じ他のサービスとホスティング プランを共有します。モバイル サービスが Standard 価格レベルで動作している場合、それは独自のホスティング プランに配置されています。Free または Basic 価格レベルのサイトを個別に移行する場合、モバイル サービスの価格レベルを一時的に Standard にアップグレードします。これはモバイル サービスの [スケール] メニューで設定できます。

  1.  [Azure クラシック ポータル]にログオンします。
  2.  モバイル サービスを選択します。
  3.  **[スケール]** タブを選択します。
  4.  **[モバイル サービス層]** で **[STANDARD]** 層をクリックします。ページの下部にある **[保存]** アイコンをクリックします。

移行後、価格レベルを適切な設定に必ず戻してください。

## <a name="migrating-site"></a>サイトを移行する

サイトを移行するには:

  1.  [Azure クラシック ポータル]にログオンします。
  2.  モバイル サービスを選択します。
  3.  **[App Service に移行する]** ボタンをクリックします。

    ![[移行] ボタン][0]

  4.  [App Service に移行する] ダイアログを読みます。
  5.  ボックスにモバイル サービスの名前を入力します。たとえば、ドメイン名が「contoso.azure-mobile.net」の場合、ボックスに「_contoso_」と入力します。
  6.  チェック マーク ボタンをクリックします。

Free または Basic 価格レベルのモバイル サービスを移行すると、その価格レベルのすべてのモバイル サービスが同時に移行されます。移行の間、Standard に[モバイルサービスを格上げする](#opt-raise-service-tier)ことでこれを回避できます。

利用状況モニターで移行の状態を監視できます。サイトは Azure クラシック ポータルに「 *移行中* 」として一覧表示されます。

  ![移行アクティビティ モニター][1]

移行するモバイル サービスごとに移行には 3 分から 15 分かかります。サイトは移行の間も利用できますが、移行プロセスの終了時に再起動されます。再起動プロセスの間、サイトは数秒間利用できなくなります。

## <a name="finalizing-migration"></a>移行の最終処理

移行プロセスの終わりとして、モバイル クライアントからサイトをテストしてください。モバイル クライアントを変更せず、あらゆる共通クライアント アクションを実行できることを確認します。さらに、価格レベルの変更など、移行のために行った変更を必要に応じて戻せることを確認してください。

### <a name="update-app-service-tier"></a>適切な App Service 価格レベルを選択する

Azure App Service に移行した後は、価格設定がより自由になります。

  1.  [Azure ポータル]にログインします。
  2.  **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
  3.  既定で [設定] ブレードが開きます。開かない場合は、**[設定]** をクリックします。
  4.  [設定] メニューの **[App Service プラン]** をクリックします。
  5.  **[価格レベル]** タイルをクリックします。
  6.  要件に合うタイルをクリックし、**[選択]** をクリックします。利用できる価格レベルを表示するには、**[すべて表示]** をクリックする必要があります。

出発点としては以下が推奨されます。

| モバイル サービス価格レベル | App Service 価格レベル |
| :-------------------------- | :----------------------- |
| 無料 | F1 Free |
| 基本 | B1 Basic |
| 標準 | S1 Standard |

自分のアプリケーションに適した価格レベルを自由に選択できます。App Service の価格に関する詳細については、「[App Service 価格]」を参照してください。

> [AZURE.TIP] App Service Standard 層では、[ステージング スロット]、自動バックアップ、自動スケールなど、さまざまな機能にアクセスできます。この機会に新しい機能をお試しください。

### <a name="review-migration-scheduler-jobs"></a>移行された Scheduler Jobs の確認

Scheduler Jobs は移行後約 30 分経過するまで表示されません。スケジュールされたジョブはすべて引き続きバック グラウンドで実行されます。スケジュールされたジョブを表示するには:

  1.  [Azure ポータル]にログインします。
  2.  **[参照]** を選択し、_[フィルター]_ ボックスに「**Schedule**」と入力し、**[Scheduler コレクション]** を選択します。

移行後に利用できる無料スケジューラ ジョブの数は限られています。使用状況と [Azure Scheduler プラン]を確認してください。

### <a name="configure-cors"></a>必要に応じて CORS を構成する

クロス オリジン リソース共有は、Web サイトが異なるドメインの Web API にアクセスする手法です。Azure Mobile Services に Web サイトが関連付けられている場合、移行の一環として CORS を構成する必要があります。Azure Mobile Services へのアクセスをモバイル デバイスに限定していた場合、特別な場合を除き、CORS を構成する必要はありません。

移行した CORS 設定は **MS\_CrossDomainWhitelist** アプリ設定として利用できます。App Service の CORS 機能をサイトに移行するには:

  1.  [Azure ポータル]にログインします。
  2.  **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
  3.  既定で [設定] ブレードが開きます。開かない場合は、**[設定]** をクリックします。
  4.  API メニューの **[CORS]** をクリックします。
  5.  許可されたオリジンをボックスに 1 つずつ入力し、Enter を押します。
  6.  許可されたオリジンの一覧が正しければ、[保存] ボタンをクリックします。

この作業は任意ですが、管理しやすくなります。

> [AZURE.TIP] Azure App Service を使用する利点の 1 つは、同じサイトで Web サイトとモバイル サービスを実行できることです。詳細については、「[次のステップ](#next-steps)」セクションを参照してください。

### <a name="download-publish-profile"></a>新しい発行プロファイルのダウンロード

Azure App Service に移行すると、サイトの発行プロファイルが変更されます。Visual Studio 内からサイトを発行する場合、新しい発行プロファイルが必要になります。新しい発行プロファイルをダウンロードするには、次の手順に従います。

  1.  [Azure ポータル]にログインします。
  2.  **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
  3.  **[発行プロファイルの取得]** をクリックします。

PublishSettings ファイルがコンピューターにダウンロードされます。通常、ファイル名は _サイト名_.PublishSettings です。これで、発行設定を既存のプロジェクトにインポートできます。

  1.  Visual Studio を開き、Azure Mobile Service プロジェクトを開きます。
  2.  **ソリューション エクスプローラー**でプロジェクトを右クリックし、**[発行...]** をクリックします。
  3.  **[インポート]** をクリックします。
  4.  **[参照]** をクリックし、ダウンロードした発行設定ファイルを選択します。**[OK]** をクリックします。
  5.  **[接続の検証]** をクリックして、発行設定が機能していることを確認します。
  6.  **[発行]** をクリックして、サイトを発行します。


## <a name="working-with-your-site"></a>サイトの移行後の操作

まず、[Azure ポータル]の移行後で、新しい App Service を操作します。以下は、[Azure クラシック ポータル]で実行していた特定の操作とそれに対応する App Service 操作に関する注記です。

### <a name="publishing-your-site"></a>移行したサイトをダウンロードし、公開する

サイトは git または ftp 経由で利用可能であり、WebDeploy、TFS、Mercurial、GitHub、FTP など、さまざまなメカニズムで再公開できます。デプロイメント資格情報はサイトの残りの部分で移行されます。デプロイメント資格情報を設定しなかった場合、または資格情報を覚えていない場合はリセットできます。

  1. [Azure ポータル]にログインします。
  2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
  3. 既定で [設定] ブレードが開きます。開かない場合は、**[設定]** をクリックします。
  4. [公開] メニューの **[デプロイメント資格情報]** をクリックします。
  5. ボックスに新しいデプロイメント資格情報を入力し、[保存] ボタンをクリックします。

これらの資格情報を利用し、git でサイトの複製を作成したり、GitHub、TFS、Mercurial から自動化デプロイを設定したりできます。詳細については、「[Azure App Service のデプロイに関するドキュメント]」を参照してください。

### <a name="appsettings"></a>アプリケーションの設定

移行したモバイル サービスのほとんどの設定は [アプリケーション設定] から利用できます。アプリ設定の一覧は [Azure ポータル]から取得できます。アプリケーションの設定を表示または変更するには:

  1. [Azure ポータル]にログインします。
  2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
  3. 既定で [設定] ブレードが開きます。開かない場合は、**[設定]** をクリックします。
  4. [全般] メニューで、**[アプリケーション設定]** をクリックします。
  5. [アプリケーション設定] セクションまでスクロールし、アプリ設定を見つけます。
  6. アプリ設定の値をクリックし、値を編集します。**[保存]** をクリックして値を保存します。

複数のアプリ設定を同時に更新できます。

> [AZURE.TIP] 同じ値を含むアプリケーション設定が 2 つあります。たとえば、_ApplicationKey_ と _MS\_ApplicationKey_ の 2 つがあります。**MS\_** という接頭辞の付いたアプリ設定だけを変更します。ただし、両方のアプリ設定を同時に更新するのはよい考えです。

### <a name="authentication"></a>認証

すべての認証設定は、移行したサイトでアプリケーション設定として利用できます。認証設定を更新するには、適切なアプリケーション設定を変更する必要があります。次の表は、ご利用の認証プロバイダーに適したアプリケーションの設定をまとめたものです。

| プロバイダー | クライアント ID | クライアント シークレット | その他の設定 |
| :---------------- | :------------------------ | :--------------------------- | :------------------------- |
| Microsoft アカウント | **MS\_MicrosoftClientID** | **MS\_MicrosoftClientSecret** | **MS\_MicrosoftPackageSID** |
| Facebook | **MS\_FacebookAppID** | **MS\_FacebookAppSecret** | |
| Twitter | **MS\_TwitterConsumerKey** | **MS\_TwitterConsumerSecret** | |
| Google | **MS\_GoogleClientID** | **MS\_GoogleClientSecret** | |
| Azure AD | **MS\_AadClientID** | | **MS\_AadTenants** |

注: **MS\_AadTenants** はテナント ドメインのコンマ区切りリストとして格納されています (Mobile Services ポータルの [許可されているテナント] フィールド)。

> [AZURE.WARNING] **[設定] メニューの認証メカニズムは使用しないでください。**
>
> Azure App Service の _[認証/承認]_ 設定メニューに別個の「コードなし」認証/承認システムがあり、[設定] メニューに (廃止) _[モバイル認証]_ オプションがあります。これらのオプションと移行した Azure モバイル サービスとの間には互換性がありません。サイトをアップグレードし、Azure App Service 認証を最大限に活用できます。

### <a name="easytables"></a>データ

Mobile Services の _[データ]_ タブは Azure ポータルでは _[テーブルの簡単操作]_ に取って代わられました。テーブルの簡単操作にアクセスするには:

  1. [Azure ポータル]にログインします。
  2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
  3. 既定で [設定] ブレードが開きます。開かない場合は、**[設定]** をクリックします。
  4. [モバイル] メニューの **[テーブルの簡単操作]** をクリックします。

**[追加]** ボタンをクリックして新しいテーブルを追加したり、テーブル名をクリックして既存のテーブルにアクセスしたりできます。このブレードからは次のようなさまざまな操作を実行できます。

  * テーブルのアクセス許可を変更する
  * 操作スクリプトを編集する
  * テーブル スキーマを管理する
  * テーブルを削除する
  * テーブルの内容を消去する
  * テーブルの特定の行を削除する

### <a name="easyapis"></a>API

Mobile Services の _[API]_ タブは Azure ポータルでは _[API の簡単操作]_ に取って代わられました。API の簡単操作にアクセスするには:

  1. [Azure ポータル]にログインします。
  2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
  3. 既定で [設定] ブレードが開きます。開かない場合は、**[設定]** をクリックします。
  4. [モバイル] メニューの **[API の簡単操作]** をクリックします。

移行した API はブレードに一覧表示されます。このブレードから新しい API を追加することもできます。特定の API を管理するには、API をクリックします。新しいブレードから、アクセス許可を調整したり、API のスクリプトを編集したりできます。

### <a name="on-demand-jobs"></a>スケジューラ ジョブ

スケジューラ ジョブはすべて、[スケジューラ ジョブ コレクション] セクションから利用できます。スケジューラ ジョブにアクセスするには、次の手順に従います。

  1. [Azure ポータル]にログインします。
  2. **[参照]** を選択し、_[フィルター]_ ボックスに「**Schedule**」と入力し、**[Scheduler コレクション]** を選択します。
  3. サイトのジョブ コレクションを選択します。ジョブ コレクションには、_サイト名_-Jobs という名前が付けられています。
  4. **[設定]** をクリックします。
  5. [管理] の **[スケジューラ ジョブ]** をクリックします。

スケジュールされたジョブは、移行前に指定した頻度で表示されます。オンデマンド ジョブは無効になります。オンデマンド ジョブを実行するには、次の手順に従います。

  1. 実行するジョブを選択します。
  2. 必要に応じて、**[有効にする]** をクリックしてジョブを有効にします。
  3. **[設定]**、**[スケジュール]** の順にクリックします。
  4. 定期的なアイテムとして **[1 回]** を選択し、**[保存]** をクリックします。

オンデマンド ジョブは `App_Data/config/scripts/scheduler post-migration` にあります。すべてのオンデマンド ジョブを [WebJobs] に変更することが推奨されます。新しいスケジューラ ジョブは、[WebJobs] として作成してください。

### <a name="notification-hubs"></a>Notification Hubs

Mobile Services では、プッシュ通信に Notification Hubs が使用されます。次のアプリケーション設定を使用して、移行後、モバイル サービスに通知ハブをリンクします。

| アプリケーション設定 | 説明 |
| :------------------------------------- | :--------------------------------------- |
| **MS\_PushEntityNamespace** | 通知ハブの名前空間 |
| **MS\_NotificationHubName** | 通知ハブの名前 |
| **MS\_NotificationHubConnectionString** | 通知ハブの接続文字列 |
| **MS\_NamespaceName** | MS\_PushEntityNamespace のエイリアス |

通知ハブは [Azure ポータル]経由で管理されます。Notification Hub 名を書き留めます (アプリケーション設定で見つかります)。

  1. [Azure ポータル]にログインします。
  2. **[参照]** > \[Notification Hubs] の順に選択します。
  3. モバイル サービスに関連付けられている通知ハブの名前をクリックします。

> [AZURE.NOTE] 「Mixed」タイプの場合、通知ハブは表示されません。「Mixed」タイプの通知ハブでは、Notification Hubs と以前の Service Bus 機能の両方が利用されます。[Mixed 名前空間を変換する]必要があります。変換が完了すると、通知ハブが [Azure ポータル]に表示されます。

詳細については、[Notification Hubs] ドキュメントを確認してください。

> [AZURE.TIP] "[Azure ポータル]"の Notification Hubs 管理機能はまだプレビュー段階です。 [Azure クラシック ポータル]で引き続きすべての Notification Hubs を管理できます。

### <a name="app-settings"></a>その他のアプリ設定

次の追加アプリ設定はモバイル サービスから移行され、*[設定]* > *[アプリ設定]* にあります。

| アプリケーション設定 | 説明 |
| :------------------------------- | :-------------------------------------- |
| **MS\_MobileServiceName** | アプリの名前 |
| **MS\_MobileServiceDomainSuffix** | ドメインの接頭辞 (例: azure-mobile.net) |
| **MS\_ApplicationKey** | アプリケーション キー |
| **MS\_MasterKey** | アプリ マスター キー |

アプリケーション キーとマスター キーは、元のモバイル サービスのアプリケーション キーと同じになる必要があります。具体的に言うと、アプリケーション キーはモバイル クライアントのモバイル API の利用を検証するために送信されます。

### <a name="cliequivalents"></a>対応するコマンド ライン

_azure モバイル_ コマンドを利用して Azure Mobile Services サイトを管理することができなくなります。多くの関数が _azure サイト_ コマンドに取って代わられました。下の表を利用し、共通コマンドに対応するものを探してください。

| _Azure Mobile_ コマンド | 対応する _Azure サイト_ コマンド |
| :----------------------------------------- | :----------------------------------------- |
| mobile locations | site location list |
| mobile list | site list |
| mobile show _name_ | site show _name_ |
| mobile restart _name_ | site restart _name_ |
| mobile redeploy _name_ | site deployment redeploy _commitId_ _name_ |
| mobile key set _name_ _type_ _value_ | site appsetting delete _key_ _name_ <br/> site appsetting add _key_=\_value\_ _name_ |
| mobile config list _name_ | site appsetting list _name_ |
| mobile config get _name_ _key_ | site appsetting show _key_ _name_ |
| mobile config set _name_ _key_ | site appsetting delete _key_ _name_ <br/> site appsetting add _key_=\_value\_ _name_ |
| mobile domain list _name_ | site domain list _name_ |
| mobile domain add _name_ _domain_ | site domain add _domain_ _name_ |
| mobile domain delete _name_ | site domain delete _domain_ _name_ |
| mobile scale show _name_ | site show _name_ |
| mobile scale change _name_ | site scale mode _mode_ _name_ <br /> site scale instances _instances_ _name_ |
| mobile appsetting list _name_ | site appsetting list _name_ |
| mobile appsetting add _name_ _key_ _value_ | site appsetting add _key_=\_value\_ _name_ |
| mobile appsetting delete _name_ _key_ | site appsetting delete _key_ _name_ |
| mobile appsetting show _name_ _key_ | site appsetting delete _key_ _name_ |

適切なアプリケーション設定を更新し、認証またはプッシュ通信を更新します。ファイルを編集し、ftp または git を使用してサイトを公開します。

### <a name="diagnostics"></a>診断とログ

通常、診断ログは Azure App Service で無効になっています。診断ログを有効にするには:

  1. [Azure ポータル]にログインします。
  2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
  3. 既定で [設定] ブレードが開きます。開かない場合は、**[設定]** をクリックします。
  4. [機能] メニューで **[診断ログ]** を選択します。
  5. **[アプリケーション ログ (ファイル システム)]**、**[詳細なエラー メッセージ]**、**[失敗した要求トレース]** のログの **[オン]** をクリックします。
  6. Web サーバーのログの **[ファイル システム]** をクリックします。
  7. **[保存]** をクリックします。

ログを表示するには:

  1. [Azure ポータル]にログインします。
  2. **[すべてのリソース]** または **[App Services]** を選択し、移行したモバイル サービスの名前をクリックします。
  3. **[ツール]** ボタンをクリックします。
  4. [監視] メニューの **[ログ ストリーム]** を選択します。

生成されたログはウィンドウにストリーム配信されます。ログをダウンロードし、後でデプロイ資格情報を利用して分析できます。詳細については、[ログ]に関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

アプリケーションが App Service に移行され、活用できる機能がさらに増えました。

  * デプロイメント [ステージング スロット]では、変更をサイトに公開し、A/B テストを実行できます。
  * [WebJobs] は、オンデマンドでスケジュールしたジョブの代わりを提供します。
  * GitHub、TFS、Mercurial にサイトをリンクし、サイトを[連続的にデプロイ]できます。
  * [Application Insights] を利用し、サイトを監視できます。
  * 同じコードから Web サイトと Mobile API にサービスを提供します。

### <a name="upgrading-your-site"></a>Azure Mobile Apps SDK に Mobile Services サイトをアップグレードする

  * Node.js ベースのサーバー プロジェクトの場合、新しい [Mobile Apps Node.js SDK] は多くの新機能を提供します。たとえば、ローカルで開発やデバッグを行い、0.10 より後のバージョンの Node.js を使用し、Express.js ミドルウェアでカスタマイズを行うことができます。

  * .NET ベースのサーバー プロジェクトの場合、新しい [Mobile Apps SDK NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)は、NuGet の依存関係に関してより高い柔軟性を備え、新しい App Service 認証機能をサポートし、MVC を含むすべての ASP.NET プロジェクトを作成します。アップグレードの詳細については、「[既存の .NET Mobile Service の App Service へのアップグレード](app-service-mobile-net-upgrading-from-mobile-services.md)」を参照してください。

<!-- Images -->
[0]: ./media/app-service-mobile-migrating-from-mobile-services/migrate-to-app-service-button.PNG
[1]: ./media/app-service-mobile-migrating-from-mobile-services/migration-activity-monitor.png
[2]: ./media/app-service-mobile-migrating-from-mobile-services/triggering-job-with-postman.png

<!-- Links -->
[App Service 価格]: https://azure.microsoft.com/ja-JP/pricing/details/app-service/
[Application Insights]: ../application-insights/app-insights-overview.md
[自動スケール]: ../app-service-web/web-sites-scale.md
[Azure App Service]: ../app-service/app-service-value-prop-what-is.md
[Azure App Service のデプロイに関するドキュメント]: ../app-service-web/web-sites-deploy.md
[Azure クラシック ポータル]: https://manage.windowsazure.com
[Azure ポータル]: https://portal.azure.com
[Azure リージョン]: https://azure.microsoft.com/ja-JP/regions/
[Azure Scheduler プラン]: ../scheduler/scheduler-plans-billing.md
[連続的にデプロイ]: ../app-service-web/web-sites-publish-source-control.md
[Mixed 名前空間を変換する]: https://azure.microsoft.com/ja-JP/blog/updates-from-notification-hubs-independent-nuget-installation-model-pmt-and-more/
[curl]: http://curl.haxx.se/
[custom domain names]: ../app-service-web/web-sites-custom-domain-name.md
[Fiddler]: http://www.telerik.com/fiddler
[Azure App Service は一般公開されており]: /blog/announcing-general-availability-of-app-service-mobile-apps/
[ハイブリッド接続]: ../app-service-web/web-sites-hybrid-connection-get-started.md
[ログ]: ../app-service-web/web-sites-enable-diagnostic-log.md
[Mobile Apps Node.js SDK]: https://github.com/azure/azure-mobile-apps-node
[Mobile Services と App Service の比較]: app-service-mobile-value-prop-migration-from-mobile-services.md
[Notification Hubs]: ../notification-hubs/notification-hubs-overview.md
[パフォーマンス監視]: ../app-service-web/web-sites-monitor.md
[Postman]: http://www.getpostman.com/
[モバイル サービス スクリプトをバックアップし]: ../mobile-services/mobile-services-disaster-recovery.md
[ステージング スロット]: ../app-service-web/web-sites-staged-publishing.md
[VNet]: ../app-service-web/web-sites-integrate-with-vnet.md
[WebJobs]: ../app-service-web/websites-webjobs-resources.md

<!---HONumber=AcomDC_0107_2016-->
