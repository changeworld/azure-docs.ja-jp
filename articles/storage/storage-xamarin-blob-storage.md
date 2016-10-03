<properties
	pageTitle="Xamarin から BLOB ストレージを使用する方法 | Microsoft Azure"
	description="Azure Storage Client Library for Xamarin を利用すれば、開発者は iOS、Android、Windows Store アプリをネイティブ ユーザー インターフェイスで作成できます。このチュートリアルでは、Xamarin を利用し、Azure BLOB ストレージを使用するアプリケーションを作成する方法を紹介します。"
	services="storage"
	documentationCenter="xamarin"
	authors="micurd"
	manager="jahogg"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="micurd;tamram"/>

# Xamarin から BLOB ストレージを使用する方法

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Overview

Xamarin を利用すれば、開発者は共有 C# コードベースを利用して iOS、Android、Windows Store アプリをネイティブ ユーザー インターフェイスで作成できます。このチュートリアルでは、Xamarin アプリケーションで Azure BLOB ストレージを使用する方法を紹介します。コードの説明に入る前に Azure Storage について学習するには、「[Microsoft Azure Storage の概要](storage-introduction.md)」を参照してください。

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## 新しい Xamarin アプリケーションの作成

最初に、Android、iOS、および Windows を対象とするアプリケーションを作成します。このアプリケーションは、コンテナーを作成し、そのコンテナーに BLOB をアップロードします。ここでは Windows で Visual Studio を使用しますが、Mac OS 上で Xamarin Studio を使ってアプリケーションを作成するときも、同じ操作を適用することができます。

次の手順でアプリケーションを作成します。

1. [Xamarin for Visual Studio](https://www.xamarin.com/download) をダウンロードしてインストールします (まだ、インストールしていない場合)。
2. Visual Studio を開き、空のアプリケーションを作成します (**[ファイル] > [新規] > [プロジェクト] > [クロス プラットフォーム] > [Blank App(Native Shared) (空のアプリケーション (Native Shared))]**)。
3. [ソリューション エクスプローラー] ウィンドウでソリューションを右クリックし、**[ソリューションの NuGet パッケージの管理]** を選択します。**WindowsAzure.Storage** を検索し、最新の安定バージョンを、ソリューション内のすべてのプロジェクトにインストールします。
4. プロジェクトをビルドして実行します。

これで、ボタンをクリックすることでカウンターをインクリメントできるアプリケーションが作成されます。

> [AZURE.NOTE] Xamarin 用 Azure Storage クライアント ライブラリは、Xamarin.Forms アプリケーションにも使用できます。

## コンテナーの作成および BLOB のアップロード

次に、コンテナーを作成して、そのコンテナーに BLOB をアップロードするためのコードをいくつか、共有クラス `MyClass.cs` に追加します。`MyClass.cs` は次のようになります。

	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Blob;
	using System.Threading.Tasks;

	namespace XamarinApp
	{
		public class MyClass
		{
			public MyClass ()
			{
			}

		    public static async Task createContainerAndUpload()
		    {
		        // Retrieve storage account from connection string.
		        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

		        // Create the blob client.
		        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

		        // Retrieve reference to a previously created container.
		        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

				// Create the container if it doesn't already exist.
            	await container.CreateIfNotExistsAsync();

		        // Retrieve reference to a blob named "myblob".
		        CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

		        // Create the "myblob" blob with the text "Hello, world!"
		        await blockBlob.UploadTextAsync("Hello, world!");
		    }
		}
	}

"Your\_account\_name\_here" と "your\_account\_key\_here" は、実際のアカウント名とアカウント キーに置き換えます。この共有クラスは、iOS、Android、および Windows Phone アプリケーションで使用できます。`MyClass.createContainerAndUpload()` を各プロジェクトに追加するだけです。次に例を示します。

### XamarinApp.Droid > MainActivity.cs

	using Android.App;
	using Android.Widget;
	using Android.OS;

	namespace XamarinApp.Droid
	{
		[Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
		public class MainActivity : Activity
		{
			int count = 1;

			protected override async void OnCreate (Bundle bundle)
			{
				base.OnCreate (bundle);

				// Set our view from the "main" layout resource
				SetContentView (Resource.Layout.Main);

				// Get our button from the layout resource,
				// and attach an event to it
				Button button = FindViewById<Button> (Resource.Id.myButton);

				button.Click += delegate {
					button.Text = string.Format ("{0} clicks!", count++);
				};

	      	  await MyClass.createContainerAndUpload();
			}
		}
	}

### XamarinApp.iOS > ViewController.cs

	using System;
	using UIKit;

	namespace XamarinApp.iOS
	{
		public partial class ViewController : UIViewController
		{
			int count = 1;

			public ViewController (IntPtr handle) : base (handle)
			{
			}

			public override async void ViewDidLoad ()
			{
				base.ViewDidLoad ();
				// Perform any additional setup after loading the view, typically from a nib.
				Button.AccessibilityIdentifier = "myButton";
				Button.TouchUpInside += delegate {
					var title = string.Format ("{0} clicks!", count++);
					Button.SetTitle (title, UIControlState.Normal);
				};

	            await MyClass.createContainerAndUpload();
	    	}

			public override void DidReceiveMemoryWarning ()
			{
				base.DidReceiveMemoryWarning ();
				// Release any cached data, images, etc that aren't in use.
			}
		}
	}

### XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

	using Windows.UI.Xaml.Controls;
	using Windows.UI.Xaml.Navigation;

	// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

	namespace XamarinApp.WinPhone
	{
	    /// <summary>
	    /// An empty page that can be used on its own or navigated to within a Frame.
	    /// </summary>
	    public sealed partial class MainPage : Page
	    {
	        int count = 1;

	        public MainPage()
	        {
	            this.InitializeComponent();

	            this.NavigationCacheMode = NavigationCacheMode.Required;
	        }

	        /// <summary>
	        /// Invoked when this page is about to be displayed in a Frame.
	        /// </summary>
	        /// <param name="e">Event data that describes how this page was reached.
	        /// This parameter is typically used to configure the page.</param>
	        protected override async void OnNavigatedTo(NavigationEventArgs e)
	        {
	            // TODO: Prepare page for display here.

	            // TODO: If your application contains multiple pages, ensure that you are
	            // handling the hardware Back button by registering for the
	            // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
	            // If you are using the NavigationHelper provided by some templates,
	            // this event is handled for you.
	            Button.Click += delegate {
	                var title = string.Format("{0} clicks!", count++);
	                Button.Content = title;
	            };

	            await MyClass.createContainerAndUpload();
	        }
	    }
	}


## アプリケーションの実行

これで、このアプリケーションを、Android または Windows Phone エミュレーターで実行できます。iOS エミュレーターで、このアプリケーションを実行することもできますが、それには Mac が必要です。これを行う具体的な手順については、[Mac への Visual Studio の接続](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)に関するドキュメントをお読みください

アプリケーションを実行すると、コンテナー `mycontainer` がストレージ アカウントに作成されます。これには、テキスト `Hello, world!` が示された BLOB `myblob` が含まれています。これを確認するには、[Microsoft Azure ストレージ エクスプ ローラー](http://storageexplorer.com/)を使用します。

## 次のステップ

ここでは、Azure Storage を使用する Xamarin に、クロス プラットフォーム アプリケーションを作成する方法について説明しました。このチュートリアルは、特に Blob Storage の 1 つのシナリオに焦点を合わせています。ただし、Blob Storage だけでなく、Table Storage、File Storage、および Queue Storage を使用すると、さらに多くの作業を行うことができます。詳細については、次の記事を確認してください。
- [.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md)
- [.NET を使用して Azure Table Storage を使用する](storage-dotnet-how-to-use-tables.md)
- [.NET を使用して Azure Queue Storage を使用する](storage-dotnet-how-to-use-queues.md)
- [Windows で Azure File Storage を使用する](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

<!---HONumber=AcomDC_0921_2016-->