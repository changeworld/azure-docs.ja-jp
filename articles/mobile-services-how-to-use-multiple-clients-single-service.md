<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="1 つのモバイル サービスから複数のクライアントをサポートするための考慮事項" authors="krisragh" solutions="" manager="" editor="" />

# 1 つのモバイル サービスから複数のクライアントをサポートするための考慮事項
 
Azure モバイル サービスを使用してモバイル アプリケーションの開発をサポートする主な利点として、1 つのバックエンド サービスを使用して複数のクライアント プラットフォームのアプリケーションをサポートできることが挙げられます。モバイル サービスには、すべての主要なデバイス プラットフォーム用にネイティブ クライアント ライブラリが用意されています。詳細については、「[チュートリアルとリソース]」を参照してください。

モバイル サービスでは、1 つのバックエンド サービスを使用して複数のクライアント プラットフォームのネイティブ アプリケーションを簡単に移行できますが、いくつかの考慮事項があります。このトピックでは、すべてのクライアント プラットフォーム間でプッシュ通知が機能するように準備する方法についてガイダンスを示します。また、Windows ストアと Windows Phone の両方のアプリケーションで Microsoft アカウントを使用したクライアント主導のシングル サインオンを利用する場合に、発生する問題を回避する方法についても説明します。このトピックの最後では、Visual Studio プロジェクトでコードを再利用するためのいくつかのベスト プラクティスについて説明します。

## プッシュ通知 
このセクションでは、モバイル サービスから複数のプラットフォームのクライアント アプリケーションにプッシュ通知を送信するための 2 つのアプローチについて説明します。

### 通知ハブを活用する

Azure 通知ハブは Azure のサービスの 1 つであり、モバイル サービス (または任意のバックエンド) からすべての主要なデバイス プラットフォームのクライアント アプリケーションにプッシュ通知を送信するための、拡張性の高いソリューションでもあります。詳細については、「[Azure 通知ハブ]」を参照してください。

通知ハブによって提供される一貫性のある統一されたインフラストラクチャで、デバイスの登録を作成および管理したり、すべての主要なデバイス プラットフォーム上で動作するデバイスにプッシュ通知を送信したりできます。詳細については、「[通知ハブの使用]」を参照してください。通知ハブではプラットフォーム固有のテンプレートの登録がサポートされているため、1 回の API 呼び出しを使用して、登録したすべてのプラットフォーム上で動作するアプリケーションに通知を送信できます。詳細については、「[ユーザーへのクロスプラットフォーム通知の送信]」を参照してください。

通知ハブは、モバイル サービスからクライアント プラットフォームに通知を送信するための推奨されるソリューションです。

### 共通の登録テーブルとプラットフォーム識別子を使用する

通知ハブを使用しない場合は、共通のデバイス登録メカニズムを定義することで、モバイル サービスから複数のクライアントへのプッシュ通知をサポートできます。1 つのテーブルを使用して、サポートされているプラットフォームのプッシュ通知サービスで使用されるデバイス固有の情報を格納します。チュートリアル「**プッシュ通知の使用**」([Windows ストア C#][Get started with push Windows dotnet]/[Windows ストア JavaScript][Get started with push Windows js]/[Windows Phone][Get started with push Windows Phone]/[iOS][Get started with push iOS]/[Android][Get started with push Android]) では、**Registrations** テーブルを使用し、_handle_ という名前の列にチャネル URI (Windows)、デバイス トークン (iOS)、または ハンドル (Android) を格納しています。

<div class="dev-callout"><b>注</b>
	<p>Visual Studio 2013 のプッシュ通知の追加ウィザードで、プッシュ通知を Windows ストア アプリケーションに追加すると、<strong>Channel</strong> という名前のテーブルが自動的に作成されて、チャネル URI の格納に使用されます。チュートリアル「<strong>Visual Studio 2012 でのプッシュ通知の使用</strong>」(<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012">Windows ストア C#</a>/<a href="/ja-jp/develop/mobile/tutorials/get-started-with-push-js-vs2012">Windows ストア JavaScript</a>) では、<strong>Registrations</strong> テーブルを使用してプッシュ通知を有効にする方法を示しています。</p>
</div>

この 1 つの登録テーブルで複数のクライアントをサポートするには、そのテーブルに _platform_ 列を追加します。このフィールドには、登録時に行が挿入されてクライアントのプラットフォームが設定されます。たとえば、次の **Registrations** クラス定義は、Windows ストア アプリケーションまたは Windows Phone アプリケーションのものであり、クライアントの _ChannelUri_ フィールドを Registrations テーブルの _handle_ 列に対応付けています。
		
		public class Registrations
		{
			[JsonProperty(PropertyName = "platform")]			
			public string Platform { 
				get
				{
					return "windowsstore";
					// return "windowsphone";
				}
				set {}
			}
			
		    public string Id { get; set; }
		
			[JsonProperty(PropertyName = "handle")]
			public string ChannelUri { get; set; }
		}

この Windows デバイスでは、_Platform_ フィールドから常に `windowsstore` (または `windowsphone` ) が返されます。動的スキーマが有効になっていると (既定)、モバイル サービスによって Registrations テーブルにプラットフォームの列が追加されます。詳細については、「[動的スキーマ]」を参照してください。

通知を送信するサーバー側スクリプトでは、プラットフォームのフィールドを使用して、[push オブジェクト]で呼び出すプラットフォーム固有の関数を決定します。次のスクリプトは、チュートリアル「**プッシュ通知の使用**」([Windows ストア C#][Get started with push Windows dotnet]/[Windows ストア JavaScript][Get started with push Windows js]/[Windows Phone][Get started with push Windows Phone]/[iOS][Get started with push iOS]/[Android][Get started with push Android]) からのサーバー スクリプトであり、複数のクライアント プラットフォームを有効にするように変更を加えています。

		function insert(item, user, request) {
		    request.execute({
		        success: function() {
		            request.respond();
		            sendNotifications();
		        }
		    });
		
		    function sendNotifications() {
		        var registrationsTable = tables.getTable('Registrations');
		        registrationsTable.read({
		            success: function(registrations) {
		                registrations.forEach(function(registration) {
		                    if (registration.platform === 'winstore') {
		                        push.wns.sendToastText04(registration.handle, {
		                            text1: item.text
		                        }, {
		                            success: pushCompleted
		                        });
		                    } else if (registration.platform === 'winphone') {
		                        push.mpns.sendFlipTile(registration.handle, {
		                            title: item.text
		                        }, {
		                            success: pushCompleted
		                        });
		                    } else if (registration.platform === 'ios') {
		                        push.apns.send(registration.handle, {
		                            alert: "Toast: " + item.text,
		                            payload: {
		                                inAppMessage: item.text
		                            }
		                        });
		                    } else if (registration.platform === 'android') {
		                        push.gcm.send(registration.handle, item.text, {
		                            success: pushCompleted
		                        });
		                    } else {
		                        console.error("Unknown push notification platform");
		                    }
		                });
		            }
		        });
		    }
		
		    function pushCompleted(pushResponse) {
		        console.log("Sent push:", pushResponse);
		    }
		}



## Windows アプリケーションの登録

Windows ストアと Windows Phone の両方のアプリケーションで Microsoft アカウントを使用したシングル サインオン クライアント認証を利用するには、まず Windows ストア ダッシュボードで Windows ストア アプリケーションを登録する必要があります。Windows Phone 用に Live Connect 登録を作成した後は、Windows ストア用に登録を作成できないためです。この方法の詳細については、「**Live Connect シングル サインオンによる Windows ストア アプリケーションの認証**」([Windows ストア][SSO Windows ストア]/[Windows Phone][SSO Windows Phone]) を参照してください。

## Visual Studio プロジェクトでコードを再利用するためのベスト プラクティス

ポータブル クラス ライブラリを使用すると、Windows ストアや Windows Phone など、複数のプラットフォームで動作するマネージ アセンブリを記述してビルドできます。多数のプロジェクトで共有するコードを含むクラスを作成し、各種プロジェクトからそれらのクラスを参照することができます。

.NET Framework ポータブル クラス ライブラリを使用して、Model-View-View Model (MVVM) パターンを実装し、複数のプラットフォームでアセンブリを共有することができます。Visual Studio 2012 のポータブル クラス ライブラリ プロジェクトでモデル クラスとビュー モデル クラスを実装し、各種プラットフォーム用にカスタマイズされたビューを作成することができます。プラットフォーム間の共通モデル コードでは、(例として) Azure のモバイル サービスなどのソースからプラットフォームに依存しない方法でデータを取得できます。MSDN ライブラリでは、<a href="http://msdn.microsoft.com/ja-jp/library/gg597391(v=vs.110)">概要と例</a>、<a href="http://msdn.microsoft.com/ja-jp/library/gg597392(v=vs.110)">API の相違点</a>の説明、<a href="http://msdn.microsoft.com/ja-jp/library/hh563947(v=vs.110)">ポータブル クラス ライブラリを使用した MVVM パターンの実装</a>の例、その他の<a href="http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/jj714086(v=vs.105).aspx">規範的なガイダンス</a>、およびポータブル クラス ライブラリ プロジェクトでの<a href="http://msdn.microsoft.com/ja-jp/library/hh871422(v=vs.110)">リソースの管理</a>に関する情報が提供されています。

<!-- URLs -->

[Azure 通知ハブ]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows ストア]: /ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /ja-jp/develop/mobile/tutorials/single-sign-on-wp8/
[チュートリアルとリソース]: /ja-jp/develop/mobile/resources/
[通知ハブの使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
[ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[プッシュの使用 Windows dotnet]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[プッシュの使用 Windows js]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js-vs2012/
[プッシュの使用 Windows Phone]: /ja-jp/develop/mobile/tutorials/get-started-with-push-wp8/
[プッシュの使用 iOS]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios/
[プッシュの使用 Android]: /ja-jp/develop/mobile/tutorials/get-started-with-push-android/
[動的スキーマ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj193175.aspx
[push オブジェクト]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554217.aspx

