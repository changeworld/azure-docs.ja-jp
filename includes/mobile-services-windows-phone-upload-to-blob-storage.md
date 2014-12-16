
##<a name="add-select-images"></a>アプリケーションの更新イメージをキャプチャおよびアップロードするクイック スタート クライアント アプリケーションの更新

このセクションでは、「[モバイル サービスの使用]」チュートリアルで使用したプロジェクトを更新して、写真を撮影して Azure BLOB ストレージにアップロードできるようにします。このチュートリアルではイメージをキャプチャするために、`Microsoft.Phone.Tasks` 名前空間の [CameraCaptureTask] を使用しています。このクラスは Windows Phone デバイスのカメラ UI を起動し、写真を撮影して、自動的にそのイメージを Windows Phone デバイスのカメラ ロールに保存します。イメージをカメラ ロールに保存しない場合は、代わりに `Microsoft.Devices` 名前空間の [PhotoCamera] クラスを使用します。

1. Visual Studio のソリューション エクスプローラーで、プロジェクトの下の **[プロパティ]** を展開します。次に WMAppManifest.xml ファイルを開き、**[機能]** タブの **ID\_CAP\_ISV\_CAMERA** をクリックして、カメラを有効にします。ファイルを閉じて変更内容を保存します。

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png)

   	これにより、コンピューターに接続されたカメラをアプリケーションで使用できます。ユーザーは、アプリケーションを最初に実行したときに、カメラのアクセスを許可することを求められます。

2. MainPage.xaml ファイルを開き、**ContentPanel** という名前の **Grid** 要素を次のコードに置き換えます。

        <!--ContentPanel - place additional content here-->
        <Grid x:Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="Auto" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="2*" />
                <ColumnDefinition Width="2*" />
            </Grid.ColumnDefinitions>
            <TextBlock Grid.Row="0" Grid.ColumnSpan="2" Text="Enter some text below, click Capture Image to add a captured image. Then click Save to insert a new TodoItem item into your database" TextWrapping="Wrap" Margin="12"/>
            <TextBox Grid.Row="1" Grid.ColumnSpan="2" Name="TextInput" Text="" />
            <Button Name="ButtonCaptureImage" Grid.Row="2" Click="ButtonCaptureImage_Click">Capture Image</Button>
            <Button Grid.Row ="2" Grid.Column="1" Name="ButtonSave" Click="ButtonSave_Click">Save</Button>
            <TextBlock Grid.Row="3" Grid.ColumnSpan="2" Text="Click refresh below to load the unfinished TodoItems from your database. Use the checkbox to complete and update your TodoItems" TextWrapping="Wrap" Margin="12" />
            <Button Grid.Row="4" Grid.ColumnSpan="2" Name="ButtonRefresh" Click="ButtonRefresh_Click">Refresh</Button>
            <phone:LongListSelector Grid.Row="5" Grid.ColumnSpan="2" Name="ListItems">
                <phone:LongListSelector.ItemTemplate>
                    <DataTemplate>
                        <StackPanel Orientation="Vertical">
                            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" Checked="CheckBoxComplete_Checked" Content="{Binding Text}" Margin="10,5" VerticalAlignment="Center"/>
                            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" MaxHeight="150"/>
                        </StackPanel>
                    </DataTemplate>
                </phone:LongListSelector.ItemTemplate>
            </phone:LongListSelector>
        </Grid>


   	これにより、[CameraCaptureTask] を起動する新しいボタンが追加され、**ItemTemplate** にイメージが追加されます。さらに、そのバインド ソースとして、Blob ストレージ サービスにアップロードされたイメージの URI が設定されます。

3. MainPage.xaml.cs プロジェクト ファイルを開き、次の **using** ステートメントを追加します。
	
		using Microsoft.Phone.Tasks;
		using System.IO;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
    
4. MainPage.xaml.cs プロジェクト ファイルで、次のプロパティを追加して TodoItem クラスを更新します。

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

5. MainPage.xaml.cs プロジェクト ファイルで、MainPage クラスを更新します。次に示すコードを追加して、[CameraCaptureTask] を宣言し、キャプチャされたイメージを参照するストリーム オブジェクトを宣言します。

        // Using the CameraCaptureTask to allow the user to capture a todo item image //
        CameraCaptureTask cameraCaptureTask;
		
        // Using a stream reference to upload the image to blob storage.
        Stream imageStream = null;

6. MainPage.xaml.cs プロジェクト ファイルで、MainPage クラスを更新します。CameraCaptureTask を作成し、Completed イベントのイベント ハンドラーを追加するために、コンストラクターに次のコードを追加します。

        // Constructor
        public MainPage()
        {
            InitializeComponent();
			
            cameraCaptureTask = new CameraCaptureTask();
            cameraCaptureTask.Completed += cameraCaptureTask_Completed;
        }
		
        void cameraCaptureTask_Completed(object sender, PhotoResult e)
        {
            imageStream = e.ChosenPhoto;
        }

7. MainPage.xaml.cs プロジェクト ファイルで、MainPage クラスを更新します。次のコードを追加すると、カメラ UI が表示され、ユーザーが **[Capture Image]** ボタンをクリックしたときにイメージをキャプチャできるようになります。

        private void ButtonCaptureImage_Click(object sender, RoutedEventArgs e)
        {
            cameraCaptureTask.Show();
        }


8. MainPage.xaml.cs プロジェクト ファイルで、MainPage クラスを更新します。既存の `InsertTodoItem` メソッドを次のコードに置き換えます。
 
        private async void InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;            
			
            if (imageStream != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";
                todoItem.ResourceName = Guid.NewGuid().ToString() + ".jpg";
            }                       
			
            // Send the item to be inserted. When blob properties are set this
            // generates an SAS in the response.
            await todoTable.InsertAsync(todoItem);  
			
            // If we have a returned SAS, then upload the blob.
            if (!string.IsNullOrEmpty(todoItem.SasQueryString))
            {
                // Get the URI generated that contains the SAS 
                // and extract the storage credentials.
                StorageCredentials cred = new StorageCredentials(todoItem.SasQueryString);
                var imageUri = new Uri(todoItem.ImageUri);
				
                // Instantiate a Blob store container based on the info in the returned item.
                CloudBlobContainer container = new CloudBlobContainer(
                    new Uri(string.Format("https://{0}/{1}",
                        imageUri.Host, todoItem.ContainerName)), cred);                
				
                // Upload the new image as a BLOB from the stream.
                CloudBlockBlob blobFromSASCredential =
                    container.GetBlockBlobReference(todoItem.ResourceName);
                await blobFromSASCredential.UploadFromStreamAsync(imageStream);
				
				// When you request an SAS at the container-level instead of the blob-level,
				// you are able to upload multiple streams using the same container credentials.

                imageStream = null;
            }              
			
            // Add the new item to the collection.
            items.Add(todoItem);
            TextInput.Text = "";
        }


	このコードは、新しい TodoItem を挿入する要求をモバイル サービスに送信します (イメージ ファイル名を含む)。応答には、SAS (その後、Blob ストア内のイメージの挿入に使用される) と、データ バインド用のイメージの URI が含まれます。

最後の手順では、アプリケーションをテストし、アップロードが成功するかどうかを検証します。
		
##<a name="test"></a>アプリケーションのイメージのアップロードをテストする

1. Visual Studio で F5 キーを押すと、エミュレーターまたは対象となる実際のデバイスでアプリケーションをテストできます。

2. テキスト ボックスにテキストを入力し、**[Capture Image]** をクリックします。

   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

  	これにより、カメラ キャプチャ UI が表示されます。 

3.  イメージをクリックするか、電話機のスナップショット ボタンを押すと、写真が撮影されます。
  
   	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-wp8.png)

4. **[accept]** をクリックするとそのイメージが採用され、カメラ UI が終了します。

    ![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png)

5. **[Save]** をクリックし、新しい項目を挿入してイメージをアップロードします。

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-save-wp8.png)

6. 新しい項目がアップロード イメージと共にリスト ビューに表示されます。

	![](./media/mobile-services-windows-phone-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

   >[WACOM.NOTE]新しい項目の <code>imageUri</code> プロパティが <strong>Image</strong> コントロールにバインドされると、イメージは Blob ストレージ サービスから自動的にダウンロードされます。


[モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-windows-phone-get-started
[CameraCaptureTask]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/microsoft.phone.tasks.cameracapturetask(v=vs.105).aspx
[PhotoCamera]: http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/microsoft.devices.photocamera(v=vs.105).aspx
