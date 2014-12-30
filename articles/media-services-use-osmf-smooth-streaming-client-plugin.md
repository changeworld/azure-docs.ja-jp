<properties urlDisplayName="Smooth Streaming Plugin" pageTitle="Open Source Media Framework 用スムーズ ストリーミング プラグイン" metaKeywords="" description="Learn how to use the Azure Media Services Smooth Streaming plugin for the Adobe Open Source Media Framework." metaCanonical="" services="media-services" documentationCenter="" title="How to Use the Microsoft Smooth Streaming Plugin for the Adobe Open Source Media Framework" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />



# Adobe Open Source Media Framework 用 Microsoft スムーズ ストリーミング プラグインを使用する方法 #

##概要 ##
Open Source Media Framework 2.0 用 Microsoft スムーズ ストリーミング プラグイン (OSMF 用 SS) は、OSMF の既定の機能を拡張し、新規または既存の OSMF プレーヤーに Microsoft スムーズ ストリーミング コンテンツ再生機能を追加します。また、このプラグインは、Strobe Media Playback (SMP) にもスムーズ ストリーミング再生機能を追加します。

OSMF 用 SS には、次に示す 2 つのバージョンのプラグインが含まれています。

- OSMF 用 Static スムーズ ストリーミング プラグイン (.swc)

- OSMF 用 Dynamic スムーズ ストリーミング プラグイン (.swf)

このドキュメントでは、OSMF および OSMF プラグインに関する一般的な実務知識を持つ読者を想定しています。OSMF の詳細については、[OSMF の公式サイト](http://osmf.org/)にあるドキュメントを参照してください。

###OSMF 2.0 用スムーズ ストリーミング プラグイン

このプラグインは、オンデマンド スムーズ ストリーミング コンテンツの読み込みおよび再生を次の機能でサポートしています。

- オンデマンド スムーズ ストリーミング再生 (再生、一時停止、シーク、停止)
- ライブ スムーズ ストリーミング再生 (再生)
- ライブ DVR 機能 (一時停止、シーク、DVR 再生、Go-to-Live)
- ビデオ コーデック (H.264) のサポート
- オーディオ コーデック (AAC) のサポート
- OSMF ビルトイン API による複数オーディオ言語切り替え
- OSMF ビルトイン API による再生時の最高画質選択
- OSMF キャプション プラグインによるサイドカー クローズド キャプション
- Adobe&reg; Flash&reg; Player 10.2 以上
- このバージョンでは OSMF 2.0 のみをサポート

次の機能はサポートされません。

- VC-1 および WMA コーデック
- コンテンツ保護 (PlayReady)
- テキスト トラックとスパース トラック
- Trickplay (スロー モーション、早送り、巻き戻し)

既知の問題の一覧を次に示します。

- 48 KHz オーディオ トラックが含まれるスムーズ ストリーミング コンテンツの再生に問題があります。Flash ランタイムには、48 KHz オーディオ コンテンツのレンダリングに関する問題があります。この問題のため、48 Khz 設定でエンコードされたスムーズ ストリーミング コンテンツが正しく動作しないことがあります。詳細については、[「Using Flash Player (Flash Player の使用)](http://forums.adobe.com/message/4483498#4483498) 」および「[Adobe Flash Player 11.3 - Bug 3210964 (Adobe Flash Player 11.3 - バグ 3210964)](https://bugbase.adobe.com/index.cfm?event=bug&id=3210964) 」を参照してください。
- 単一ページで複数のスムーズ ストリーミング コンテンツを再生すると、問題が発生することがあります。これは OSMF に関する既知の問題です。
- Stage Video を再生すると、問題が発生することがあります。一部のコンピューターではビデオをまったく再生できないこともあります。回避策として、ハードウェア アクセラレータまたは Stage Video を無効にすることができます。

## プラグインの読み込み
OSMF プラグインは、静的 (コンパイル時) または動的 (実行時) に読み込むことができます。OSMF 用スムーズ ストリーミング プラグインのダウンロードには、静的バージョンと動的バージョンの両方が含まれています。

- 静的読み込み:静的に読み込むには、静的ライブラリ (SWC) ファイルが必要です。静的プラグインは参照としてプロジェクトに追加され、コンパイル時に最終的な出力ファイル内部でマージされます。

- 動的読み込み:動的に読み込むには、プリコンパイル済みファイル (SWF) ファイルが必要です。動的プラグインはランタイムに読み込まれ、プロジェクト出力(コンパイル済み出力) には含まれません。動的プラグインは HTTP および FILE プロトコルを使用して読み込むことができます。

静的読み込みと動的読み込みの詳細については、[OSMF 公式サイトのプラグインに関するページ](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)を参照してください。

###OSMF 用 SS の静的読み込み
次のコード スニペットは、OSMF 用 SS プラグインを静的に読み込み、OSMF MediaFactory クラスを使用して基本的なビデオを再生する方法を示しています。OSMF 用 SS をコードに含める前に、プロジェクト参照に静的プラグイン "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" が含まれていることを確認してください。

<pre><code>
package 
{
	
	import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	
	
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		

		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;

			initMediaPlayer();

		}
	
		private function initMediaPlayer():void
		{
		
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
			_mediaPlayerSprite.scaleMode = ScaleMode.NONE;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			
			pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
			_mediaFactory.loadPlugin( pluginResource ); 
		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.
			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
		loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>


###OSMF 用 SS の動的読み込み

次のコード スニペットは、OSMF 用 SS プラグインを動的に読み込み、OSMF MediaFactory クラスを使用して基本的なビデオを再生する方法を示しています。OSMF 用 SS をコードに含める前に、動的プラグイン "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" をプロジェクト フォルダーにコピー (FILE プロトコルを使用して読み込む場合) するか、Web サーバーにコピー (HTTP プロトコルを使用して読み込む場合) してください。プロジェクト参照に "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" を含める必要はありません。

<pre><code>
package 
{
	
	import flash.display.*;
	import org.osmf.media.*;
	import org.osmf.containers.MediaContainer;
	import org.osmf.events.MediaErrorEvent;
	import org.osmf.events.MediaFactoryEvent;
	import org.osmf.events.MediaPlayerStateChangeEvent;
	import org.osmf.layout.*;
	import flash.events.Event;
	import flash.system.Capabilities;

	
	//Sets the size of the SWF
	
	[SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
	public class TestPlayer extends Sprite
	{        
		public var _container:MediaContainer;
		public var _mediaFactory:DefaultMediaFactory;
		private var _mediaPlayerSprite:MediaPlayerSprite;
		
		
		public function TestPlayer( )
		{
			stage.quality = StageQuality.HIGH;
			initMediaPlayer();
		}
		
		private function initMediaPlayer():void
		{
			
			// Create the container (sprite) for managing display and layout
			_mediaPlayerSprite = new MediaPlayerSprite();    
			_mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
			_mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

			//Adds the container to the stage
			addChild(_mediaPlayerSprite);
			
			// Create a mediafactory instance
			_mediaFactory = new DefaultMediaFactory();
			
			// Add the listeners for PLUGIN_LOADING
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
			_mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
			
			// Load the plugin class 
			loadAdaptiveStreamingPlugin( );  
			
		}
		
		private function loadAdaptiveStreamingPlugin( ):void
		{
			var pluginResource:MediaResourceBase;
			var adaptiveStreamingPluginUrl:String;

			// Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

			adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
			pluginResource = new URLResource(adaptiveStreamingPluginUrl);
			_mediaFactory.loadPlugin( pluginResource ); 

		}
		
		private function onPluginLoaded( event:MediaFactoryEvent ):void
		{
			// The plugin is loaded successfully.

			// Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

	loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
		}
		
		private function onPluginLoadFailed( event:MediaFactoryEvent ):void
		{
			// The plugin is failed to load ...
		}
		
		
		private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
		{
			var state:String;
			
			state =  event.state;
			
			switch (state)
			{
				case MediaPlayerState.LOADING: 
					
					// A new source is started to load.
					
					break;
				
				case  MediaPlayerState.READY :   
					// Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
					
					break;
				
				case MediaPlayerState.BUFFERING :
					
					break;
				
				case  MediaPlayerState.PAUSED :
					break;      
				// other states ...          
			}
		}
		
		private function onPlayerFailed(event:MediaErrorEvent) : void
		{
			// Media Player is failed .           
		}
		
		private function loadMediaSource(sourceURL : String):void 
		{
			// Take an URL of SmoothStreamingSource's manifest and add it to the page.
			
			var resource:URLResource= new URLResource( sourceURL );
			
			var element:MediaElement = _mediaFactory.createMediaElement( resource );
			_mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
			_mediaPlayerSprite.width = stage.stageWidth;
			_mediaPlayerSprite.height = stage.stageHeight;
			// Add the media element
			_mediaPlayerSprite.media = element;
		}     
		
	}
}
</code></pre>

##Strobe Media Playback と OSMF 用 SS 動的プラグイン
OSMF 用スムーズ ストリーミング動的プラグインには、[Strobe Media Playback (SMP)](http://osmf.org/strobe_mediaplayback.html) との互換性があります。OSMF 用 SS プラグインを使用すると、スムーズ ストリーミング コンテンツ再生機能を SMP に追加することができます。これには、"MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" を Web サーバーにコピーし、次に示す手順を使用して HTTP 読み込みを行ってください。

1.	[Strobe Media Playback セットアップ ページ](http://osmf.org/dev/2.0gm/setup.html)に移動します。 
2.	[src] をスムーズ ストリーミング ソース (「http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest」など) に設定します。 
3.	必要な構成変更を行い、[Preview and Update] をクリックします。
 
	**注**: コンテンツ Web サーバーには有効な crossdomain.xml が必要です。 
4.	コードをコピーし、好みのテキスト エディターで作成した HTML ページに、次の例のようにコードを貼り付けます。



		<html>
		<body>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
		</embed>
		</object>
		</body>
		</html>



5. OSMF 用スムーズ ストリーミング プラグインを埋め込みコードに追加して保存します。

		<html>
		<object width="920" height="640"> 
		<param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
		<param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
		<param name="allowFullScreen" value="true"></param>
		<param name="allowscriptaccess" value="always"></param>
		<param name="wmode" value="direct"></param>
		<embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
    		type="application/x-shockwave-flash" 
    		allowscriptaccess="always" 
    		allowfullscreen="true" 
    		wmode="direct" 
    		width="920" 
    		height="640" 
    		flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
		</embed>
		</object>
		</html>


6. 	HTML ページを保存して、Web サーバーに発行します。Flash&reg; Player 対応の好みのインターネット ブラウザー (Internet Explorer、Chrome、Firefox など) を使用して、発行済みの Web ページに移動します。
7. 	Adobe&reg; Flash&reg; Player でスムーズ ストリーミング コンテンツをお楽しみください。

全般的な OSMF 開発の詳細については、[OSMF 公式サイトの開発に関するページ](http://osmf.org/resources.html)を参照してください。

<!--HONumber=35_1-->
