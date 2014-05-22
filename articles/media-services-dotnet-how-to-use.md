<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="メディア サービスを使用する方法" authors="" solutions="" manager="" editor="" />


#メディア サービスを使用する方法

このガイドでは、Azure メディア サービスを使用したプログラミングの基礎について説明します。このガイドはいくつかの記事のセットで構成され、メディア サービスの技術的概要、メディア サービスに使用する Azure アカウントの構成手順、開発のセットアップ ガイドのほか、標準的なプログラミング タスクの実行方法を紹介したトピックなどが含まれています。示されているシナリオには、アセットのアップロード、アセットの暗号化、アセットのエンコード、アセットの配信が含まれています。サンプルは C# で記述され、Media Services SDK for .NET を利用しています。Azure メディア サービスの詳細については、「[次のステップ][]」を参照してください。

メディア サービスのプログラムは、OData ベースの REST API を使用して作成することもできます。.NET 言語など各種のプログラミング言語からメディア サービスに対する REST API 呼び出しを行うアプリケーションを構築できます。メディア サービス REST API によるプログラミングについての一連のドキュメントについては、[Azure メディア サービス REST API を使ったアプリケーション構築に関するページ](http://go.microsoft.com/fwlink/?linkid=252967)を参照してください。

Media Services REST API または Media Services SDK でプログラミングを開始するにはまず、「[メディア サービスに使用する Azure アカウントの設定][]」の説明に従い、メディア サービスに使用する Azure アカウントを有効にする必要があります。

最新のメディア サービス SDK ドキュメントについては、[こちら](http://msdn.microsoft.com/ja-jp/library/hh973613.aspx)を参照してください。

##<a name="what-are"></a><span class="short header">メディア サービスとは</span>
Azure メディア サービスは、Microsoft Media Platform とサードパーティのメディア コンポーネントのそれぞれの良さを Azure に統合する拡張可能なメディア プラットフォームです。メディア サービスは、クラウドの中でメディアパイプラインとして機能します。業界パートナーは、メディア サービスを利用して、コンポーネント テクノロジを拡張したり、置き換えたりすることができます。ISV やメディア プロバイダーは、メディア サービスを利用して、エンド ツー エンドのメディア ソリューションを構築できます。ここでは、メディア サービスの全般的なアーキテクチャと一般的な開発シナリオの概要を説明します。

以下の図は、メディア サービスの基本アーキテクチャを示したものです。

![メディア サービスのアーキテクチャ](./media/media-services-dotnet-how-to-use/wams-01.png)

###メディア サービスの機能サポート
最新リリースのメディア サービスには、クラウドにおけるメディア アプリケーションの開発用途として、次の機能セットが用意されています。

- **取り込み**。たとえば、Azure ストレージに配置するアセットを事前にアップロードしたり暗号化したりすることによって、システムにアセットを取り込む操作です。メディア サービスにより、パートナー コンポーネントとの統合が可能となり、高速の UDP (User Datagram Protocol) アップロード ソリューションが実現されます。
- **エンコード**。エンコード操作には、メディア アセットのエンコードや変換が伴います。エンコード タスクは、Azure メディア エンコーダーを使用し、クラウドで実行することができます。次のエンコード機能があります。
   - Azure メディア エンコーダーの使用と一連の標準コーデックおよび形式への対応 (業界をリードする IIS スムーズ ストリーミング、MP4、Apple HTTP ライブ ストリーミングへの変換など)。
   - 入力と出力をトータルに制御することでライブラリ全体または個々のファイルを変換。
   - ファイル タイプ、形式、コーデックを豊富にサポート ([メディア サービスでサポートされるファイル タイプに関するページ][] を参照)。
   - 形式の変換。メディア サービスでは、ISO MP4 (.mp4) を Smooth Streaming File Format (PIFF 1.3) (.ismv、.isma) に変換できます。Smooth Streaming File Format (PIFF) を Apple HTTP ライブ ストリーミング (.msu8、.ts) に変換することもできます。
- **保護**。コンテンツの保護とは、ライブ ストリーミングやオンデマンド コンテンツを暗号化することによって、送信、保存、配信のセキュリティを保護することです。メディア サービスは、特定の DRM テクノロジにとらわれないコンテンツ保護ソリューションを提供します。現在サポートされている DRM テクノロジは、Microsoft PlayReady および MPEG Common Encryption です。その他の DRM テクノロジも今後サポートされる予定です。
- **ストリーム**。コンテンツのストリーミングとは、コンテンツをライブでまたはオンデマンドでクライアントに送信したり、特定のメディア ファイルをクラウドからダウンロードしたりすることです。メディア サービスは、特定の形式にとらわれないコンテンツ ストリーミングを提供します。メディア サービスでは、スムーズ ストリーミング、Apple HTTP ライブ ストリーミング、および MP4 形式が、ストリーミングの配信元としてサポートされます。その他の形式も今後サポートされる予定です。サードパーティ CDN を使用してストリーミング コンテンツをシームレスに配信することもでき、必要に応じて、配信規模を何百万というユーザーにまで拡張できます。

###メディア サービス開発シナリオ
メディア サービスでサポートされる代表的なメディア開発シナリオを次の表に示します。
<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
  <thead>
    <tr>
       <th>シナリオ</th>
       <th>説明</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>エンド ツー エンド ワークフローの構築</td>
        <td>クラウドにより、包括的なメディア配信ワークフローを構築できます。メディアのアップロードからコンテンツ配信まで、メディアサービスは、特定のアプリケーション ワークフローに合わせて組み合わせられるコンポーネントを提供します。現在は、アップロード、ストレージ、エンコード、形式変換、コンテンツ保護、オンデマンド ストリーミング配信などの機能が含まれます。</td>
    </tr>
    <tr>
        <td>ハイブリッド ワークフローの構築</td>
        <td>メディア サービスは、既存のツールやプロセスと統合できます。たとえば、コンテンツをオンサイトでエンコードして、複数の形式にトランスコードするためにメディア サービスにアップロードし、Azure CDN やサードパーティ CDN を通じて配信します。メディア サービスを標準 REST API 経由で個別に呼び出し、外部アプリケーションやサービスと統合できます。</td>
    </tr>
    <tr>
        <td>メディア プレーヤーのクラウド サポート</td>
        <td>多様なデバイス (iOS、Android、Windows デバイスなど) およびプラットフォームを対象にメディアを作成、管理、配信できます。</td>
    </tr>
  </tbody>
</table>

<h3><a name="media-client"></a>メディア サービス クライアント開発</h3>
SDK とプレーヤー フレームワークを使用してメディア クライアント アプリケーションを構築することによって、メディア サービス ソリューションの裾野を広げることができます。メディア サービス アプリケーションを構築する開発者は、幅広いデバイスとプラットフォームを対象に魅力的なエクスペリエンスをユーザーに提供することができます。SDK およびプレーヤー フレームワークは、ターゲット デバイスに応じて Microsoft やサードパーティ パートナーから提供されています。

以下に示したのは、クライアント SDK およびプレーヤー フレームワークの一覧です。これらの SDK とプレーヤー フレームワーク、サポート可能な機能の詳細については、メディア サービス フォーラムの[メディア サービス クライアント開発に関する記事][]を参照してください。今後予定されている SDK とプレーヤー フレームワークについても記載されています。

####Mac および PC クライアントのサポート
Microsoft Silverlight または Adobe Open Source Media Framework を使用することにより、PC および Mac を対象にしてストリーミング エクスペリエンスを提供できます。

-	[Silverlight 用 Smooth Streaming Client](http://www.microsoft.com/ja-jp/download/details.aspx?id=29940)
-	[Microsoft Media Platform: Silverlight 用プレーヤー フレームワーク](http://smf.codeplex.com/documentation)
-	[OSMF 2.0 用スムーズ ストリーミング プラグイン](http://go.microsoft.com/fwlink/?LinkId=275022)。このプラグインを使用する方法については、[Adobe Open Source Media Framework 用スムーズ ストリーミング プラグインの使用方法に関するページ](http://go.microsoft.com/fwlink/?LinkId=275034)を参照してください。

####Windows 8 アプリケーション
Windows 8 用には、サポートされている開発言語やコンストラクト (HTML、Javascript、XAML、C#、C+ など) を使用して Windows ストア アプリケーションを作成できます。

-	[Windows 8 用 Smooth Streaming Client SDK](http://go.microsoft.com/fwlink/?LinkID=246146)。この SDK を使用して Windows ストア アプリケーションを作成する方法の詳細については、[スムーズ ストリーミング用の Windows ストア アプリケーションを作成する方法に関するページ](http://go.microsoft.com/fwlink/?LinkId=271647)を参照してください。HTML5 でスムーズ ストリーミング プレイヤーを作成する方法については、「[Walkthrough: Building Your First HTML5 Smooth Streaming Player (チュートリアル: 初めての HTML5 スムーズ ストリーミング プレイヤーを作成する)](http://msdn.microsoft.com/ja-jp/library/jj573656(v=vs.90).aspx)」を参照してください。

-	[Microsoft Media Platform: Windows 8 Windows ストア アプリケーション用プレーヤー フレームワーク](http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home)

####Xbox
Xbox では、スムーズ ストリーミング コンテンツを使用する Xbox LIVE アプリケーションがサポートされています。Xbox LIVE アプリケーション開発キット (ADK) には、次のコンポーネントが含まれています。

-	Xbox LIVE ADK 用 Smooth Streaming Client
-	Microsoft Media Platform: Xbox LIVE ADK 用プレーヤー フレームワーク

####組み込みデバイスまたは専用デバイス
カスタム アプリケーション開発フレームワークおよびカスタム メディア パイプラインを備えたデバイス (スマート テレビ、セットトップ ボックス、ブルーレイ プレーヤー、OTT TV ボックス、モバイル デバイスなど)。Microsoft では、次に示すライセンス取得可能な移植キットを提供しています。これらを使用すると、プラットフォームに対応したスムーズ ストリーミング再生機能の移植が可能になります。

-	[Smooth Streaming Client Porting Kit](http://www.microsoft.com/ja-jp/mediaplatform/sspk.aspx)
-	[Microsoft PlayReady Device Porting Kit](http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx)

####Windows Phone
Microsoft では、Windows Phone 用の優れたビデオ アプリケーションを作成するために使用できる SDK を提供しています。

-	[Silverlight 用 Smooth Streaming Client](http://www.microsoft.com/ja-jp/download/details.aspx?id=29940)
-	[Microsoft Media Platform: Silverlight 用プレーヤー フレームワーク](http://smf.codeplex.com/documentation)

####iOS デバイス
iPhone、iPod、iPad などの iOS デバイス用に Microsoft で用意されている SDK (Smooth Streaming SDK for iOS Devices with PlayReady) を使用すると、これらのプラットフォームを対象にして、高品質のビデオ コンテンツを提供するアプリケーションを作成できます。この SDK を使用するには、ライセンスの取得が必要です。詳細については、[Microsoft に電子メールでお問い合わせください](mailto:askdrm@microsoft.com)。iOS 開発の詳細については、「[iOS Developer Center (iOS デベロッパー センター)](https://developer.apple.com/devcenter/ios/index.action)」を参照してください。

####Android デバイス
複数の Microsoft パートナーにより、Android デバイスでのスムーズ ストリーミング再生機能を追加するための Android プラットフォーム用 SDK が提供されています。パートナーの詳細については、[Microsoft に電子メールでお問い合わせください](mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices)。

##次のステップ
これで、メディア サービスの概要を学習できました。次は、[メディア サービス用のコンピューターのセットアップ](http://go.microsoft.com/fwlink/?LinkId=301751)に関するトピックに進みます。


  [メディア サービスとは]: #what-are
  [メディア サービス クライアント開発に関する記事]: #media-client
  [メディア サービスに使用する Azure アカウントの設定]: #setup-account
  [メディア サービス開発のための設定]: #setup-dev
  [方法: メディア サービスにプログラムから接続する]: #connect
  [方法: 暗号化されたアセットを作成してストレージにアップロードする]: #create-asset
  [方法: メディア プロセッサ インスタンスを取得する]: #get-mediaproc
  [方法: ジョブの進行状況をチェックする]: #check-job-progress
  [方法: アセットをエンコードする]: #encode-asset
  [方法: PlayReady Protection でアセットを保護する]: #playready
  [方法: ストレージ内のアセットを管理する]: #manage-asset
  [方法: ダウンロードによってアセットを配信する]: #download-asset
  [方法: ストリーミング コンテンツを配信する]: #stream-asset
  [方法: Apple HLS ストリーミング コンテンツを配信する]: #stream-HLS
  [方法: Azure CDN を有効にする]: #enable-cdn
  [次のステップ]: #next-steps

  [Azure メディア サービス REST API を使用したアプリケーション構築に関するページ]: http://go.microsoft.com/fwlink/?linkid=252967
  [Open Data Protocol]: http://odata.org/
  [WCF Data Services 5.0 for OData v3]: http://www.microsoft.com/download/en/details.aspx?id=29306
  [Azure Marketplace]: https://datamarket.azure.com/
  [メディア サービス クライアント開発に関する記事]: http://social.msdn.microsoft.com/Forums/ja-jp/MediaServices/thread/e9092ec6-2dfc-44cb-adce-1dc935309d2a
  [メディア サービス プレビュー: サポートされている機能]: http://social.msdn.microsoft.com/Forums/ja-jp/MediaServices/thread/eb946433-16f2-4eac-834d-4057335233e0
  [メディア サービスの今後のリリース: 機能のサポート予定]: http://social.msdn.microsoft.com/Forums/ja-jp/MediaServices/thread/431ef036-0939-4784-a939-0ecb31151ded
  [メディア サービス プレビュー アカウントのセットアップ]: http://go.microsoft.com/fwlink/?linkid=247287
  [Azure Media Services SDK for .NET]: http://go.microsoft.com/fwlink/?LinkID=256500
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Windows 8 への Azure SDK のインストール]: http://www.windowsazure.com/ja-jp/develop/net/other-resources/windows-azure-on-windows-8/
  [Azure メディア サービスのドキュメントに関するページ]: http://go.microsoft.com/fwlink/?linkid=245437
  [Azure CDN の概要]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff919705.aspx
  [メディア サービス フォーラム]: http://social.msdn.microsoft.com/Forums/ja-jp/MediaServices/threads
  [Media Services SDK for .NET の概要に関するページ]: http://go.microsoft.com/fwlink/?linkid=252966
  [Media Services SDK for .NET を使ったアプリケーション構築に関するページ]: http://go.microsoft.com/fwlink/?linkid=247821
  [Azure 管理ポータル]: https://manage.windowsazure.com/
  [メディア サービス アカウントの作成方法]: http://go.microsoft.com/fwlink/?linkid=256662
  [メディア サービスでサポートされるファイル タイプに関するページ]: http://msdn.microsoft.com/ja-jp/library/hh973634



