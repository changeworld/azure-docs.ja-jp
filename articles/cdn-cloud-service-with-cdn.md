<properties 
	pageTitle="クラウド サービスと Azure CDN との統合" 
	description="統合 Azure CDN エンドポイントからコンテンツを提供するクラウド サービスをデプロイする方法について説明するチュートリアル" 
	services="cdn, cloud-services" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor="tysonn"/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="cephalin"/>

<a name="intro"></a>
# クラウド サービスと Azure CDN との統合 #

クラウド サービスを Azure CDN に統合すると、クラウド サービスの `~/CDN` パスからコンテンツを配信できます。この方法には、次の利点があります。

- クラウド サービスのプロジェクト ディレクトリへの画像、スクリプト、およびスタイルシートのデプロイと更新を簡単に行うことができる。
- クラウド サービスの NuGet パッケージ (たとえば、jQuery バージョン、Bootstrap バージョン) を簡単にアップグレードできる。 
- Web アプリケーションと CDN によって配信されるコンテンツをすべて同じ Visual Studio インターフェイスから管理できる。
- Web アプリケーションと CDN によって配信されるコンテンツのデプロイメント ワークフローを一元化できる。
- ASP.NET のバンドルと縮小を Azure CDN と統合できる。

## 学習内容 ##

このチュートリアルで学習する内容は次のとおりです。

-	[Azure CDN エンドポイントをクラウド サービスと統合して、Azure CDN から Web ページの静的コンテンツを配信する](#deploy)
-	[クラウド サービスの静的コンテンツのキャッシュ設定を構成する](#caching)
-	[Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)
-	[Visual Studio のスクリプトのデバッグ エクスペリエンスを維持しながらバンドルされたコンテンツおよび縮小されたコンテンツを配信する](#bundling)
-	[Azure CDN がオフラインのときのスクリプトおよび CSS のフォールバックを構成する](#fallback) 

## 学習内容 ##

既定の ASP.NET MVC テンプレートを使用してクラウド サービス Web ロールをデプロイし、統合された Azure CDN からコンテンツ (たとえば、画像、コントローラー アクションの結果、既定の JavaScript ファイルおよび CSS ファイル) を配信するコードを追加します。さらに、CDN がオフラインになった場合に提供されるバンドルのフォールバック メカニズムを構成するコードを作成します。

## 前提条件 ##

このチュートリアルの前提条件は次のとおりです。

-	アクティブな [Microsoft Azure アカウント](/account/)
-	Visual Studio 2013 と [Azure SDK](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)

> [AZURE.NOTE] このチュートリアルを完了するには、Azure アカウントが必要です。
> + 無料で [Azure アカウントを開く](/pricing/free-trial/)ことができます - Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。
> + [MSDN サブスクライバーの特典を有効にする](/pricing/member-offers/msdn-benefits-details/)こともできます - MSDN サブスクリプションにより、有料の Azure サービスを利用できるクレジットが毎月与えられます。

<a name="deploy"></a>
## クラウド サービスを統合 CDN エンドポイントと共にデプロイする ##

このセクションでは、Visual Studio 2013 の既定の ASP.NET MVC アプリケーション テンプレートをクラウド サービス Web ロールにデプロイした後、これを新しい CDN エンドポイントと統合します。次の手順に従ってください。

1. Visual Studio 2013 で、メニュー バーから **[ファイル]、[新規]、[プロジェクト]、[クラウド]、[Azure クラウド サービス]** の順に選択して新しい Azure クラウド サービスを作成します。名前を付けて、**[OK]** をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. **[ASP.NET Web ロール]** を選択し、**[>]** をクリックします。[OK] をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. **[MVC]** を選択し、**[OK]** をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. この Web ロールを Azure クラウド サービスに発行します。クラウド サービス プロジェクトを右クリックし、**[発行]** を選択します。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Microsoft Azure にまだサインインしていない場合は、**[サインイン]** をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. サインイン ページで、Azure アカウントをアクティブ化する際に使用した Microsoft アカウントでサインインします。
7. サインインしたら、**[次へ]** をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. この段階でまだクラウド サービス アカウントまたはストレージ アカウントを作成していない場合は、Visual Studio の指示に従ってこれらのアカウントを作成します。**[クラウド サービスとアカウントの作成]** ダイアログで、目的のサービスの名前を入力します。**[作成]** をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. 発行の設定ページで、構成を確認し、**[発行]** をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

	>[AZURE.NOTE] クラウド サービスの発行には時間がかかります。[すべてのロールの Web のデプロイを有効にする] オプションを使用すると、Web ロールに対する高速な (ただし一時的な) 更新を提供することでクラウド サービスのデバッグが高速化されます。このオプションの詳細については、「[Azure Toolｓ を使用したクラウド サービスの発行](http://msdn.microsoft.com/library/ff683672.aspx)」を参照してください。

	**[Microsoft Azure のアクティビティ ログ]** に発行状態が **[完了]** と表示されたら、このクラウド サービスと統合される CDN エンドポイントを作成します。 

1. CDN エンドポイントを作成するには、[Azure の管理ポータル](http://manage.windowsazure.com/)にログインします。 
2. **[新規]**、**[App Services]**、**[CDN]**、**[簡易作成]** の順にクリックします。**http://*&lt;サービス名>*.cloudapp.net/cdn/** を選択し、**[作成]** をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-10-createcdn.png)

	>[AZURE.NOTE] CDN エンドポイントが作成されると、その URL と統合先のオリジン ドメインが Azure ポータルに表示されます。ただし、新しい CDN エンドポイントの構成がすべての CDN ノードの場所に完全に反映されるまで少し時間がかかる場合があります。 

	CDN エンドポイントは、クラウド サービスのパス **cdn/** に関連付けられます。これに対応するには、**WebRole1** プロジェクトに **cdn** フォルダーを作成するか、URL の書き換えを使用してこのパスのすべての着信リンクを削除します。このチュートリアルでは、後者の方法を使用します。

3. Azure ポータルに戻り、**[CDN]** タブで、作成した CDN エンドポイントの名前をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-11-disablequerya.png)

3. **[クエリ文字列の有効化]** をクリックし、CDN キャッシュ内のクエリ文字列を有効にします。これを有効にすると、同一のリンクが異なるクエリ文字列でアクセスされた場合は別のエントリとしてキャッシュされます。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-12-disablequeryb.png)

	>[AZURE.NOTE] チュートリアルのこのセクションでのクエリ文字列の有効化は必須ではありませんが、ここでの変更がすべての CDN ノードに反映されるまで時間がかかるため、できる限り早めに有効にしておくと便利です。また、クエリ文字列非対応コンテンツで CDN キャッシュが停滞するのを防ぐためでもあります (CDN コンテンツの更新については後で説明します)。

3. CDN エンドポイントに対して ping を実行し、CDN エンドポイントが CDN ノードに反映されていることを確認します。場合によっては ping に対する応答が得られるようになるまで最大で 1 時間待つ必要があります。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-13-testcdn.png)

2. Visual Studio 2013 に戻り、**WebRole1** プロジェクトで **Web.config** を開き、次のコードを `<system.webServer>` タグに追加します。  
	<pre class="prettyprint">
	&lt;system.webServer&gt;
	  <mark>&lt;rewrite&gt;
	    &lt;rules&gt;
	      &lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
	        &lt;match url=&quot;^cdn/(.*)$&quot;/&gt;
	        &lt;action type=&quot;Rewrite&quot; url=&quot;{R:1}&quot;/&gt;
	      &lt;/rule&gt;
	    &lt;/rules&gt;
	  &lt;/rewrite&gt;</mark>
      ...
	&lt;/system.webServer&gt;
	</pre>

4. もう一度クラウド サービスを発行します。クラウド サービス プロジェクトを右クリックし、**[発行]** を選択します。

	![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

1. 発行状態が **[完了]** と表示されたら、ブラウザー ウィンドウを開き、**http://*&lt;CDN 名>*.vo.msecnd.net/Content/bootstrap.css** に移動します。この設定では、次の URL を使用します。

		http://az632148.vo.msecnd.net/Content/bootstrap.css

	この URL は、CDN エンドポイントの次のオリジン URL に対応します。

		http://cephalinservice.cloudapp.net/cdn/Content/bootstrap.css

	Web アプリで URL が書き換えられることにより、実際には次のファイルが CDN キャッシュに格納されます。

		http://cephalinservice.cloudapp.net/Content/bootstrap.css

	**http://*&lt;CDN 名>*.vo.msecnd.net/Content/bootstrap.css** に移動すると、発行された Web アプリケーションから bootstrap.css をダウンロードするように求められます。 

	![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

同様に、CDN エンドポイントから直接、**http://*&lt;サービス名>*.cloudapp.net/** にあるパブリック アクセスが可能なすべての URL にアクセスできます。次に例を示します。

-	/Script パスの .js ファイル
-	/Content パスの任意のコンテンツ ファイル
-	任意のコントローラー/アクション 
-	CDN エンドポイントでクエリ文字列が有効になっている場合、クエリ文字列を含む任意の URL

実際には、上記の構成の場合、**http://*&lt;CDN 名>*.vo.msecnd.net/** のクラウド サービス全体をホストできます。**http://az632148.vo.msecnd.net/** に移動すると、Home/Index からのアクション結果が得られます。

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

ただし、これが、Azure CDN を介してクラウド サービス全体を提供するのが常に適切なやり方 (または一般的に適切なやり方) であることにはなりません。いくつかの注意点があります。

-	Azure CDN ではプライベートなコンテンツを配信できないため、この方法ではサイト全体をパブリックにする必要があります。
-	予定されたメンテナンスであろうとユーザー エラーであろうと、CDN エンドポイントが何かの理由でオフラインになると、顧客をオリジン URL **http://*&lt;サービス名>*.cloudapp.net/** にリダイレクトできる場合を除き、クラウド サービス全体がオフラインになります。 
-	カスタムの Cache-Control 設定 (「[クラウド サービスの静的ファイルのキャッシュ オプションを構成する](#caching)」を参照してください) を使用した場合でも、CDN エンドポイントによって高度に動的なコンテンツのパフォーマンスが向上するわけではありません。上に示すように CDN エンドポイントからホーム ページを読み込もうとした場合、非常に単純な既定のホーム ページを初めて読み込むときに 5 秒以上かかります。このページに毎分更新する必要がある動的コンテンツが含まれていたとしたら、クライアント エクスペリエンスはどうなるでしょうか。動的コンテンツを CDN エンドポイントから配信するにはキャッシュの有効期限が短く設定されている必要があります。これは、CDN エンドポイントで頻繁にキャッシュ ミスが発生することになります。その結果、クラウド サービスのパフォーマンスが低下し、CDN の目的が果たせなくなります。

また、クラウド サービスで Azure CDN から配信するコンテンツをケースバイケースで決定する方法もあります。CDN エンドポイントから個々のコンテンツ ファイルにアクセスする方法については既に説明しました。CDN エンドポイントを介して特定のコントローラー アクションを配信する方法については、「[Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)」で説明します。

より制限の厳しい URL の書き換え規則を指定すると、CDN エンドポイントを介してアクセスできるコンテンツを制限できます。たとえば、URL の書き換えを *\Scripts* フォルダーに制限するには、上記の書き換え規則を次のように変更します。   
<pre class="prettyprint">
&lt;rule name=&quot;RewriteIncomingCdnRequest&quot; stopProcessing=&quot;true&quot;&gt;
  &lt;match url=&quot;^cdn/<mark>Scripts/</mark>(.*)$&quot;/&gt;
  &lt;action type=&quot;Rewrite&quot; url=&quot;<mark>Scripts/</mark>{R:1}&quot;/&gt;
&lt;/rule&gt;
</pre>

<a name="caching"></a>
## クラウド サービスの静的ファイルのキャッシュ オプションを構成する ##

Azure CDN 統合をクラウド サービスに組み込むと、CDN エンドポイントで静的コンテンツをどのようにキャッシュするかを指定できます。このためには、Web ロール プロジェクト (例: WebRole1) から  *Web.config* を開き、`<staticContent>` 要素を `<system.webServer>` に追加します。次の XML では、3 日間で有効期限が切れるキャッシュを追加しています。  
<pre class="prettyprint">
&lt;system.webServer&gt;
  <mark>&lt;staticContent&gt;
    &lt;clientCache cacheControlMode=&quot;UseMaxAge&quot; cacheControlMaxAge=&quot;3.00:00:00&quot;/&gt;
  &lt;/staticContent&gt;</mark>
  ...
&lt;/system.webServer&gt;
</pre>

この操作を実行すると、クラウド サービス内のすべての静的ファイルは、CDN キャッシュ内で同じ規則に従います。キャッシュ設定をより細かく制御するには、 *Web.config* ファイルをフォルダーに追加し、そこに設定を追加します。たとえば、 *Web.config* ファイルを *\Content* フォルダーに追加して、その内容を次の XML で置き換えます。

	<?xml version="1.0"?>
	<configuration>
	  <system.webServer>
	    <staticContent>
	      <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
	    </staticContent>
	  </system.webServer>
	</configuration>

この設定は、*\Content* フォルダーのすべての静的ファイルを 15 日間キャッシュすることを指定しています。

`<clientCache>` 要素を構成する方法の詳細については、[クライアント キャッシュ &lt;clientCache>](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache) に関するページを参照してください。

「[Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)」では、CDN キャッシュ内のコントローラー アクションの結果に対してキャッシュ設定を構成する方法についても説明します。

<a name="controller"></a>
## Azure CDN を介してコントローラー アクションからコンテンツを配信する ##

クラウド サービス Web ロールを Azure CDN と統合すると、Azure CDN を介してコントローラー アクションからコンテンツを簡単に配信できます。(上に示した) Azure CDN を介して直接クラウド サービスを提供する方法のほか、[Maarten Balliauw 氏](https://twitter.com/maartenballiauw)は、[Azure CDN による Web の遅延時間の短縮](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)のビデオでおもしろい MemeGenerator コントローラーを使用した方法を紹介しています。ここではその方法を再現します。

クラウド サービスで次のような Chuck Norris の若いときの画像 ([Alan Light](http://www.flickr.com/photos/alan-light/218493788/) による撮影) に基づいてミームを生成するとします。

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

ここで使用する `Index` アクションでは、画像内にこのようなジョークを指定できます。顧客がこのアクションに投稿するとミームが生成されます。題材が Chuck Norris のため、このページは全世界で受け入れられると予想されます。これは、Azure CDN で半動的コンテンツを配信するよい例です。 

上記の手順を実行して、このコントローラー アクションを設定します。

1. *\Controllers* フォルダーに、 *MemeGeneratorController.cs* という名前の新しい .cs ファイルを作成し、その内容を次のコードで置き換えます。強調表示されている箇所は CDN 名で置き換えてください。  
	<pre class="prettyprint">
	using System;
	using System.Collections.Generic;
	using System.Diagnostics;
	using System.Drawing;
	using System.IO;
	using System.Net;
	using System.Web.Hosting;
	using System.Web.Mvc;
	using System.Web.UI;
	
	namespace WebRole1.Controllers
	{
	    public class MemeGeneratorController : Controller
	    {
	        static readonly Dictionary&lt;string, Tuple&lt;string ,string&gt;&gt; Memes = new Dictionary&lt;string, Tuple&lt;string, string&gt;&gt;();

	        public ActionResult Index()
	        {
	            return View();
	        }
	
	        [HttpPost, ActionName(&quot;Index&quot;)]
        	public ActionResult Index_Post(string top, string bottom)
	        {
	            var identifier = Guid.NewGuid().ToString();
	            if (!Memes.ContainsKey(identifier))
	            {
	                Memes.Add(identifier, new Tuple&lt;string, string&gt;(top, bottom));
	            }
	
	            return Content(&quot;&lt;a href=\&quot;&quot; + Url.Action(&quot;Show&quot;, new {id = identifier}) + &quot;\&quot;&gt;here's your meme&lt;/a&gt;&quot;);
	        }


	        [OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Show(string id)
	        {
	            Tuple&lt;string, string&gt; data = null;
	            if (!Memes.TryGetValue(id, out data))
	            {
	                return new HttpStatusCodeResult(HttpStatusCode.NotFound);
	            }
	
	            if (Debugger.IsAttached) // Preserve the debug experience
	            {
	                return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
	            }
	            else // Get content from Azure CDN
	            {
	                return Redirect(string.Format(&quot;http://<mark>&lt;yourCdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
	            }
	        }

	        [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
	        public ActionResult Generate(string top, string bottom)
	        {
	            string imageFilePath = HostingEnvironment.MapPath(&quot;<mark>~/Content/chuck.bmp</mark>&quot;);
	            Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
	
	            using (Graphics graphics = Graphics.FromImage(bitmap))
	            {
	                SizeF size = new SizeF();
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
	                }
	                using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
	                {
	                    graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
	                }
	            }
	
	            MemoryStream ms = new MemoryStream();
	            bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
	            return File(ms.ToArray(), &quot;image/png&quot;);
	        }
	
	        private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
	        {
	            // Compute actual size, shrink if needed
	            while (true)
	            {
	                size = g.MeasureString(text, font);
	
	                // It fits, back out
	                if (size.Height &lt; i.Height &amp;&amp;
	                     size.Width &lt; i.Width) { return font; }
	
	                // Try a smaller font (90% of old size)
	                Font oldFont = font;
	                font = new Font(font.Name, (float)(font.Size * .9), font.Style);
	                oldFont.Dispose();
	            }
	        }
	    }
	}
	</pre>

2. 既定の `Index()` アクション内で右クリックし、**[ビューの追加]** を選択します。

	![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  次の既定の設定をそのまま受け入れ、**[追加]** をクリックします。

	![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. 新しい *Views\MemeGenerator\Index.cshtml* を開き、その内容を、ジョークを送信するための次の単純な HTML で置き換えます。

		<h2>Meme Generator</h2>
		
		<form action="" method="post">
		    <input type="text" name="top" placeholder="Enter top text here" />
		    <br />
		    <input type="text" name="bottom" placeholder="Enter bottom text here" />
		    <br />
		    <input class="btn" type="submit" value="Generate meme" />
		</form>

5. もう一度クラウド サービスを発行して、ブラウザーで **http://*&lt;サービス名>*.cloudapp.net/MemeGenerator/Index** に移動します。 

フォームの値を `/MemeGenerator/Index`に送信すると、`Index_Post`  アクション メソッドにより、`Show`  アクション メソッドへのリンクとそれぞれの入力識別子が返されます。リンクをクリックすると、次のコードに到達します。  
<pre class="prettyprint">
[OutputCache(VaryByParam = &quot;*&quot;, Duration = 1, Location = OutputCacheLocation.Downstream)]
public ActionResult Show(string id)
{
    Tuple&lt;string, string&gt; data = null;
    if (!Memes.TryGetValue(id, out data))
    {
        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
    }

    if (Debugger.IsAttached) // Preserve the debug experience
    {
        return Redirect(string.Format(&quot;/MemeGenerator/Generate?top={0}&bottom={1}&quot;, data.Item1, data.Item2));
    }
    else // Get content from Azure CDN
    {
        return Redirect(string.Format(&quot;http://<mark>&lt;cdnName&gt;</mark>.vo.msecnd.net/MemeGenerator/Generate?top={0}&amp;bottom={1}&quot;, data.Item1, data.Item2));
    }
}
</pre>

ローカル デバッガーが接続されている場合は、ローカル リダイレクトにより通常のデバッグ エクスペリエンスが得られます。クラウド サービスで実行されている場合は、次の場所にリダイレクトされます。

	http://<yourCDNName>.vo.msecnd.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

この URL は、CDN エンドポイントの次のオリジン URL に対応します。

	http://<youCloudServiceName>.cloudapp.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

URL の書き換え規則が適用された後、CDN エンドポイントにキャッシュされる実際のファイルは次のファイルになります。

	http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

次に、`Generate`  メソッドの `OutputCacheAttribute` 属性を使用して、Azure CDN に有効な、アクション結果をキャッシュする方法を指定できます。次のコードでは、キャッシュの有効期限が 1 時間 (3,600 秒) に設定されています。

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

同様に、任意のキャッシュ オプションを使用して、Azure CDN を介してクラウド サービスの任意のコントローラー アクションからコンテンツを配信できます。

次のセクションでは、バンドルされたスクリプト、縮小されたスクリプト、および CSS を Azure CDN 経由で提供する方法について説明します。 

<a name="bundling"></a>
## ASP.NET のバンドルと縮小を Azure CDN と統合できる。 ##

スクリプトや CSS スタイルシートはあまり頻繁に変更されないため、Azure CDN キャッシュの有力な候補になります。Azure CDN を介して Web ロール全体を提供するやり方は、バンドルや縮小を Azure CDN と統合する方法として最も簡単です。ただし、このやり方を希望しない方のために、次のような ASP.NET のバンドルと縮小の開発者エクスペリエンスを維持しながらこれを実現する方法を紹介します。

-	優れたデバッグ モード エクスペリエンス
-	合理的なデプロイ
-	スクリプトや CSS のバージョンをアップグレードするためのクライアントの即時の更新
-	CDN エンドポイントのに障害に対するフォールバック メカニズム
-	最小限のコードの変更

[Azure CDN エンドポイントを Azure Web サイトと統合し、Azure CDN から Web ページに静的なコンテンツを配信する](#deploy) セクションで作成した **WebRole1** プロジェクトで、*App_Start\BundleConfig.cs* を開き、`bundles.Add()` メソッドの呼び出しを見てみます。

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
		...
    }

最初の `bundles.Add()` ステートメントにより、仮想ディレクトリ `~/bundles/jquery` にスクリプトのバンドルが追加されます。次に、*Views\Shared_Layout.cshtml*  を開いて、スクリプト バンドル タグがどのようにレンダリングされているか確認します。次の Razor コードが見つかります。

    @Scripts.Render("~/bundles/jquery")

Azure Web ロールでこの Razor コードが実行されると、次のようなスクリプト バンドルのための `<script>` タグがレンダリングされます。 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

ただし、`F5` キーを押してこのコードを Visual Studio 内で実行した場合は、バンドル内の各スクリプト ファイルが個別にレンダリングされます (上記のケースでは、バンドルに含まれているスクリプト ファイルは 1 つだけです)。

    <script src="/Scripts/jquery-1.10.2.js"></script>

これにより、JavaScript コードを開発環境でデバッグできる一方で、運用環境での同時クライアント接続数 (バンドル) を削減し、ファイルのダウンロード パフォーマンス (縮小) を高めることができます。これは、Azure CDN 統合によって得られる優れた機能です。加えて、レンダリングされたバンドルには自動生成されたバージョン文字列が既に含まれます。そこで、この機能を複製することで、NuGet を介して jQuery バージョンを更新するたびにできる限り迅速にクライアント側にその更新が反映されるようにすることができます。

ASP.NET のバンドルおよび縮小を CDN エンドポイントと統合するには、次の手順に従います。

1.  *App_Start\BundleConfig.cs* に戻り、CDN アドレスを指定する別の [Bundle コンストラクター](http://msdn.microsoft.com/library/jj646464.aspx)を使用するように `bundles.Add()` メソッドを変更します。そのためには、 `RegisterBundles` メソッドの定義を次のコードで置き換えます。  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    <mark>bundles.UseCdn = true;
	    var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://&lt;yourCDNName&gt;.vo.msecnd.net/{0}?v=&quot; + version;</mark>
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jquery&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;<mark>, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)</mark>).Include(
	                &quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you're
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;<mark>, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)</mark>).Include(
	                &quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;<mark>, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)</mark>).Include(
	                &quot;~/Scripts/bootstrap.js&quot;,
	                &quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;<mark>, string.Format(cdnUrl, &quot;Content/css&quot;)</mark>).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}
	</pre>

	`<yourCDNName>` は Azure CDN の名前で置き換えてください。

	このコードでは、 `bundles.UseCdn = true` を設定し、巧妙に作成された CDN URL を各バンドルに追加しています。たとえば、コードの最初のコンストラクター

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

	は、次のコードと同じです。 

		new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.vo.msecnd.net/bundles/jquery?v=<W.X.Y.Z>"))

	このコンストラクターは、ASP.NET のバンドルおよび縮小機能に対して、ローカルにデバッグされたときに個々のスクリプト ファイルをレンダリングする一方で、指定された CDN アドレスを使用して対象のスクリプトにアクセスすることを指定しています。ここで、この巧妙に作成された CDN URL の 2 つの重要な特性に注意してください。
	
	-	この CDN URL のオリジンは、実際にはクラウド サービス内のスクリプト バンドルの仮想ディレクトリである `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>` です。
	-	CDN コンストラクターを使用しているため、バンドルのための CDN スクリプト タグには、レンダリングされた URL 内の自動生成されたバージョン文字列が含まれません。Azure CDN でキャッシュ ミスを強制的に発生させるには、スクリプト バンドルを変更するたびに一意のバージョン文字列を手動で生成する必要があります。また、この一意のバージョン文字列は、バンドルがデプロイされた後の Azure CDN でのキャッシュ ヒットを最大化するために、デプロイの有効期間を通じて一定に保たれる必要があります。
	-	クエリ文字列 v=<W.X.Y.Z> は、Web ロール プロジェクトの *Properties\AssemblyInfo.cs* から取得されます。Azure に発行するたびにアセンブリ バージョンを増分する操作を含むデプロイ ワークフローを導入できます。または、ワイルドカード文字 * を使って単にプロジェクトの *Properties\AssemblyInfo.cs* を変更して、ビルドするたびに自動的にバージョン文字列を増分するよう設定することもできます。次に例を示します。
	
				[assembly: AssemblyVersion("1.0.0.*")]
	
		デプロイの有効期間を通じて一意の文字列を生成する操作を合理化するための他の戦略も使用できます。

3. クラウド サービスを再発行し、ホーム ページにアクセスします。
 
4. ページの HTML コードを表示します。クラウド サービスに変更を再発行するたびに一意のバージョン文字列を付けて CDN URL がレンダリングされているのを確認できます。次に例を示します。  
	<pre class="prettyprint">
	...

    &lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25449&quot; rel=&quot;stylesheet&quot;/&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25449&quot;&gt;&lt;/script&gt;

	...</pre>

5. Visual Studio で、`F5` キーを押してクラウド サービスをデバッグします。 

6. ページの HTML コードを表示します。Visual Studio での一貫したデバッグ エクスペリエンスが得られるように個別にレンダリングされた各スクリプト ファイルを確認できます。  
	<pre class="prettyprint">
	...
	
	    &lt;link href=&quot;/Content/bootstrap.css&quot; rel=&quot;stylesheet&quot;/&gt;
	&lt;link href=&quot;/Content/site.css&quot; rel=&quot;stylesheet&quot;/&gt;
	
	    &lt;script src=&quot;/Scripts/modernizr-2.6.2.js&quot;&gt;&lt;/script&gt;
	
	...
	
	    &lt;script src=&quot;/Scripts/jquery-1.10.2.js&quot;&gt;&lt;/script&gt;
	
	    &lt;script src=&quot;/Scripts/bootstrap.js&quot;&gt;&lt;/script&gt;
	&lt;script src=&quot;/Scripts/respond.js&quot;&gt;&lt;/script&gt;
	
	...    
	</pre>

<a name="fallback"></a>
## CDN URL のフォールバック メカニズム ##

何かの理由で Azure CDN エンドポイントに障害が発生した場合に備えて、JavaScript または Bootstrap を読み込むためのフォールバック オプションとしてオリジン Web サーバーにアクセスできるようにしておくと便利です。CDN が利用できないために Web サイトの画像が失われることは深刻な事態ですが、スクリプトやスタイルシートで提供される重要なページ機能が失われることは、さらに深刻な事態です。

[Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) クラスには、CDN 障害に対するフォールバック メカニズムを構成するための [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) プロパティがあります。このプロパティを使用するには、次の手順に従います。

1. Web ロール プロジェクトで、それぞれの [Bundle コンストラクター](http://msdn.microsoft.com/library/jj646464.aspx)に CDN URL を追加した *App_Start\BundleConfig.cs* を開き、次の強調表示された変更を加えて、既定のバンドルにフォールバック メカニズムを追加します。  
	<pre class="prettyprint">
	public static void RegisterBundles(BundleCollection bundles)
	{
	    var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
	        .GetName().Version.ToString();
	    var cdnUrl = &quot;http://cdnurl.vo.msecnd.net/.../{0}?&quot; + version;
	    bundles.UseCdn = true;
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;, string.Format(cdnUrl, &quot;bundles/jquery&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.jquery&quot; }</mark>
	                .Include(&quot;~/Scripts/jquery-{version}.js&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;, string.Format(cdnUrl, &quot;bundles/jqueryval&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;$.validator&quot; }</mark>
	            	.Include(&quot;~/Scripts/jquery.validate*&quot;));
	
	    // Use the development version of Modernizr to develop with and learn from. Then, when you're
	    // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
	    bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;, string.Format(cdnUrl, &quot;bundles/modernizer&quot;)) 
					<mark>{ CdnFallbackExpression = &quot;window.Modernizr&quot; }</mark>
					.Include(&quot;~/Scripts/modernizr-*&quot;));
	
	    bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;, string.Format(cdnUrl, &quot;bundles/bootstrap&quot;)) 	
					<mark>{ CdnFallbackExpression = &quot;$.fn.modal&quot; }</mark>
	        		.Include(
		              		&quot;~/Scripts/bootstrap.js&quot;,
		              		&quot;~/Scripts/respond.js&quot;));
	
	    bundles.Add(new StyleBundle(&quot;~/Content/css&quot;, string.Format(cdnUrl, &quot;Content/css&quot;)).Include(
	                &quot;~/Content/bootstrap.css&quot;,
	                &quot;~/Content/site.css&quot;));
	}</pre>

	 `CdnFallbackExpression` が null でない場合は、HTML にスクリプトが挿入されます。このスクリプトは、バンドルが正常に読み込まれたかどうかをテストして、正常に読み込まれていない場合はオリジン Web サーバーのバンドルに直接アクセスします。このプロパティは、それぞれの CDN バンドルが適切に読み込まれたかどうかをテストする JavaScript 式に設定する必要があります。各バンドルをテストするために必要な式は、コンテンツによって異なります。上記の既定のバンドルに対して、次のように定義されています。
	
		-	`window.jquery` は jquery-{version}.js に定義されています。
		-	`$.validator` は jquery.validate.js に定義されています。
		-	`window.Modernizr` は modernizer-{version}.js に定義されています。
		-	`$.fn.modal` は bootstrap.js に定義されています。
	
	`~/Cointent/css` バンドルには CdnFallbackExpression を設定していません。これは、[System.Web.Optimization のバグ](https://aspnetoptimization.codeplex.com/workitem/104)が存在するためで、これはフォールバック CSS の `<script>` タグが挿入され、期待される `<link>` タグが挿入されないというバグです。
	
	ただし、[Ember Consulting Group](https://github.com/EmberConsultingGroup) から優れた[スタイル バンドルのフォールバック](https://github.com/EmberConsultingGroup/StyleBundleFallback)が提供されています。 

2. この対処法を使用するには、Web ロール プロジェクトの *App_Start* フォルダーに *StyleBundleExtensions.cs* という名前の新しい .cs ファイルを作成し、その内容を [GitHub に公開されているコード](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)で置き換えます。 

4. *App_Start\StyleFundleExtensions.cs* で、名前空間の名前を Web ロールの名前 (例: **WebRole1**) に変更します。 

3.  `App_Start\BundleConfig.cs` に戻り、最後の `bundles.Add` ステートメントを、次の強調表示されたコードで置き換えます。  
	<pre class="prettyprint">
	bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
	    <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
	    .Include(
	          "~/Content/bootstrap.css",
	          "~/Content/site.css"));
	</pre>

	この新しいメソッドでは、同じアイデアを使用するスクリプトを HTML に挿入します。つまり、DOM を調べて CSS バンドルに定義されているクラス名、規則名、および規則値が一致するかどうかを確認し、一致しない場合はオリジン Web サーバーにフォールバックします。

4. クラウド サービスを発行し、ホーム ページにアクセスします。 
5. ページの HTML コードを表示します。次のようなスクリプトが挿入されていることを確認できます。    
	<pre class="prettyprint">...
	
		&lt;link href=&quot;http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474&quot; rel=&quot;stylesheet&quot;/&gt;
	<mark>&lt;script&gt;(function() {
	                var loadFallback,
	                    len = document.styleSheets.length;
	                for (var i = 0; i &lt; len; i++) {
	                    var sheet = document.styleSheets[i];
	                    if (sheet.href.indexOf('http://az632148.vo.msecnd.net/Content/css?v=1.0.0.25474') !== -1) {
	                        var meta = document.createElement('meta');
	                        meta.className = 'sr-only';
	                        document.head.appendChild(meta);
	                        var value = window.getComputedStyle(meta).getPropertyValue('width');
	                        document.head.removeChild(meta);
	                        if (value !== '1px') {
	                            document.write('&lt;link href=&quot;/Content/css&quot; rel=&quot;stylesheet&quot; type=&quot;text/css&quot; /&gt;');
	                        }
	                    }
	                }
	                return true;
	            }())||document.write('&lt;script src=&quot;/Content/css&quot;&gt;&lt;/script&gt;');&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/modernizer?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.Modernizr)||document.write('&lt;script src=&quot;/bundles/modernizr&quot;&gt;&lt;/script&gt;');&lt;/script&gt;</mark>
	
	...	
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/jquery?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;(window.jquery)||document.write('&lt;script src=&quot;/bundles/jquery&quot;&gt;&lt;/script&gt;');&lt;/script&gt;</mark>
	
	    &lt;script src=&quot;http://az632148.vo.msecnd.net/bundles/bootstrap?v=1.0.0.25474&quot;&gt;&lt;/script&gt;
	<mark>&lt;script&gt;($.fn.modal)||document.write('&lt;script src=&quot;/bundles/bootstrap&quot;&gt;&lt;/script&gt;');&lt;/script&gt;</mark>
	
	...
	</pre>

	CSS バンドルに対して挿入されたスクリプトには、次の行に `CdnFallbackExpression` プロパティのエラーの残りがまだ含まれている点に注意してください。

        }())||document.write('<script src="/Content/css"></script>');</script>

	ただし、(すぐ上の行の) || 式の最初の部分は常に true を返すため、document.write() 関数が実行されることはありません。

# 詳細 #
- [Azure Content Delivery Network (CDN) の概要](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Web アプリケーションで Azure CDN からコンテンツを配信する](cdn-serve-content-from-cdn-in-your-web-application.md)
- [Azure CDN と Azure Websites の統合](cdn-websites-with-cdn.md)
- [ASP.NET のバンドルおよび縮小](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)
- [Azure 用 CDN の使用](cdn-how-to-use.md)

<!--HONumber=49-->