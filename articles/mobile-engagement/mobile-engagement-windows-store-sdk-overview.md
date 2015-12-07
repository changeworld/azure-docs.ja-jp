<properties 
	pageTitle="Windows ユニバーサル SDK の概要" 
	description="Azure Mobile Engagement 向け Windows ユニバーサル SDK の概要" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Azure Mobile Engagement 向け Windows ユニバーサル SDK の概要

ここから Azure Mobile Engagement を Windows ユニバーサル アプリに統合する方法についての説明を開始します。まず試してみる場合は、[15 分間チュートリアル](mobile-engagement-windows-store-dotnet-get-started.md)を完了してください。

[SDK コンテンツ](mobile-engagement-windows-store-sdk-content.md)について表示するにはここをクリックします。

##統合手順

1. ここから開始: [Windows ユニバーサル アプリに Mobile Engagement を統合する方法](mobile-engagement-windows-store-integrate-engagement.md)

2. 通知: [リーチ (通知) を Windows ユニバーサル アプリに統合する方法](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. タグ付けプランの実装: [Windows ユニバーサル アプリで高度な Mobile Engagement タグ付け API を使用する方法](mobile-engagement-windows-store-use-engagement-api.md)

##リリース ノート

###3\.2.0 (11/20/2015)

-   Windows 10 Universal Windows Platform アプリケーションのサポートを追加しました。
-   プッシュ チャネル共有機能を追加して、チャネルの競合を修正しました (現在では、Azure Notification Hubs と互換性があります)。
-   初期化直後にデバイス ID を要求すると発生するクラッシュを修正しました。
-   コンソールのログを改善しました。
-   未処理の例外を解析するときに発生するクラッシュを修正しました。

以前のバージョンについては、「[完全リリース ノート](mobile-engagement-windows-store-release-notes.md)」をご覧ください。

##アップグレードの手順

既にアプリケーションに以前のバージョンのモバイル エンゲージメントを統合してある場合は、SDK をアップグレードするときに、次の点を考慮する必要があります。

SDK のいくつかのバージョンがない場合は、次の手順に従う必要があります。完全な[アップグレードの手順](mobile-engagement-windows-store-upgrade-procedure.md)をご覧ください。たとえば、0.10.1 から 0.11.0 に移行する場合、まず「0.9.0から 0.10.1」への手順を実行してから「0.10.1 から 0.11.0」への手順を実行する必要があります。

###3\.1.0 から 3.2.0 に移行

#### リソース
この手順では、カスタマイズされたリソースが問題になります。SDK 提供のリソース (html、画像、オーバーレイ) をカスタマイズしている場合、アップグレード前にそのリソースをバックアップし、アップグレードしたリソースにカスタマイズを再適用する必要があります。

#### Web ビュー統合 を使用しないでください。
今回のバージョンで、さまざまなデバイス フォーム ファクターに合わせた改善が導入されました。Web ビューの統合が次のようになっていることを確認してください。

XAML の page ():

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

関連付けられた .cs ファイル:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
			
			  #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

			  /* When page is left ensure to detach SizeChanged handler. */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
			    base.OnNavigatedFrom(e);
			  }
			  
			  /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
			  double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
			  double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
			  double width =  Window.Current.Bounds.Width;
			  double height =  Window.Current.Bounds.Height;
    #endif
			
			  /// <summary>
			  /// Set your webview elements to the correct size.
			  /// </summary>
			  /// <param name="width">The width of your current display.</param>
			  /// <param name="height">The height of your current display.</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
			  /// </summary>
			  /// <param name="sender">Original event trigger.</param>
			  /// <param name="e">Window Size Changed Event arguments.</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP			  
			  /// <summary>
			  /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
			  /// </summary>
			  /// <param name="sender">The related application view.</param>
			  /// <param name="e">Related event arguments.</param>
			  private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
			  {
			    double width = sender.VisibleBounds.Width;
			    double height = sender.VisibleBounds.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
    #endif
			  #endregion
			}
    }

### 古いバージョンからのアップグレード

[アップグレード手順](mobile-engagement-windows-store-upgrade-procedure/)をご覧ください

<!---HONumber=AcomDC_1125_2015-->