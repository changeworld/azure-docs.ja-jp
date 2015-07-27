##Windows ストア アプリ用のストレージ クライアントのインストール

SAS を使用して Blob ストレージにイメージをアップロードできるようにするには、最初に、Windows ストア アプリ用のストレージ クライアント ライブラリをインストールする NuGet パッケージを追加する必要があります。

1. Visual Studio の**ソリューション エクスプローラー**で、プロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。

2. 左側のウィンドウで、[**オンライン**] カテゴリを選択し、`WindowsAzure.Storage` を探します。**Azure Storage** パッケージで [**インストール**] をクリックし、使用許諾契約に同意します。

  	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

  	これにより、Azure のストレージ サービス用のクライアント ライブラリがプロジェクトに追加されます。

次に、イメージをキャプチャおよびアップロードするクイック スタート アプリケーションを更新します。

##アプリケーションの更新イメージをキャプチャおよびアップロードするクイック スタート クライアント アプリケーションの更新

1. Visual Studio で、Windows アプリ プロジェクト用の Package.appxmanifest ファイルを開き、**[機能]** タブで **[Web カメラ]** 機能と **[マイク]** 機能を有効にします。

   	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-app-manifest-camera.png)
 
   	これにより、コンピューターに接続されたカメラをアプリケーションで使用できます。ユーザーは、アプリケーションを最初に実行したときに、カメラのアクセスを許可することを求められます。

2. Windows Phone アプリ プロジェクトについて上記の手順を繰り返します。
 
3. Windows アプリ プロジェクトで、MainPage.xaml ファイルを開き、最初の **QuickStartTask** 要素の直後にある **StackPanel** 要素を次のコードに置き換えます。

		<StackPanel Orientation="Horizontal" Margin="72,0,0,0">
            <TextBox Name="TextInput" Margin="5" MaxHeight="40" MinWidth="300"></TextBox>
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" Click="ButtonCapture_Click" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Name="captureGrid" Margin="62,0,0,0" Visibility="Collapsed" >
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement x:Name="previewElement" Grid.Row="0" Tapped="previewElement_Tapped" />
            <Image Name="imagePreview" Grid.Row="0"  />
            <StackPanel Name="captureButtons" Orientation="Horizontal" Grid.Row="1">
                <TextBlock Name="TextCapture" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" Click="ButtonCapture_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>

2. **DataTemplate** 内の **StackPanel** 要素を次のコードに置き換えます。

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                        Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                        Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}"
                    MaxHeight="250"/>
        </StackPanel> 

   	これにより、イメージが **ItemTemplate** に追加され、バインド ソースが、Blob ストレージ サービスのアップロードされたイメージの URI として設定されます。

3. Windows Phone アプリ プロジェクトで、MainPage.xaml ファイルを開き、**ButtonSave** 要素を次のコードに置き換えます。

        <StackPanel Grid.Row ="1" Grid.Column="1"  Orientation="Horizontal">
            <AppBarButton Label="Photo" Icon="Camera" Name="ButtonCapture" 
                          Click="ButtonCapture_Click" Height="78" Width="62" />
            <AppBarButton Label="Upload" Icon="Upload" Name="ButtonSave" 
                          Click="ButtonSave_Click"/>
        </StackPanel>
        <Grid Grid.Row="2" Name="captureGrid" Grid.RowSpan="3" Grid.ColumnSpan="2" 
              Canvas.ZIndex="99" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}" 
              Visibility="Collapsed">
            <Grid.RowDefinitions>
                <RowDefinition Height="*" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <CaptureElement Grid.Row="0" x:Name="previewElement" Tapped="previewElement_Tapped" />                    
            <Image Grid.Row="0" Name="imagePreview" Visibility="Collapsed" />
            <StackPanel Grid.Row="1" Name="captureButtons" 
                        Orientation="Horizontal" Visibility="Collapsed">
                <TextBlock Name="TextCapture" VerticalAlignment="Bottom" />
                <AppBarButton Label="Retake" Icon="Redo" Name="ButtonRetake" 
                              Click="ButtonRetake_Click" />
                <AppBarButton Label="Cancel" Icon="Cancel" Name="ButtonCancel" 
                              Click="ButtonCancel_Click" />
            </StackPanel>
        </Grid>

2. **DataTemplate** 内の **StackPanel** 要素を次のコードに置き換えます。

        <StackPanel Orientation="Vertical">
            <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}" 
                      Checked="CheckBoxComplete_Checked" Content="{Binding Text}" 
                      Margin="10,5" VerticalAlignment="Center"/>
            <Image Name="ImageUpload" Source="{Binding ImageUri, Mode=OneWay}" 
                   MaxHeight="150"/>
        </StackPanel>

4. 共有 DataModel フォルダーで、TodoItem.cs プロジェクト ファイルを開き、TodoItem クラスに次のプロパティを追加します。

        [JsonProperty(PropertyName = "containerName")]
        public string ContainerName { get; set; }
		
        [JsonProperty(PropertyName = "resourceName")]
        public string ResourceName { get; set; }
		
        [JsonProperty(PropertyName = "sasQueryString")]
        public string SasQueryString { get; set; }
		
        [JsonProperty(PropertyName = "imageUri")]
        public string ImageUri { get; set; } 

3. 共有 MainPage.cs プロジェクト ファイルを開き、次の **using** ステートメントを追加します。
	
		using Windows.Media.Capture;
		using Windows.Media.MediaProperties;
		using Windows.Storage;
		using Windows.UI.Xaml.Input;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;
		using Windows.UI.Xaml.Media.Imaging;

5. MainPage クラスに、次のコードを追加します。

        // Use a StorageFile to hold the captured image for upload.
        StorageFile media = null;
        MediaCapture cameraCapture;
        bool IsCaptureInProgress;

        private async Task CaptureImage()
        {
            // Capture a new photo or video from the device.
            cameraCapture = new MediaCapture();
            cameraCapture.Failed += cameraCapture_Failed;

            // Initialize the camera for capture.
            await cameraCapture.InitializeAsync();

            #if WINDOWS_PHONE_APP
            cameraCapture.SetPreviewRotation(VideoRotation.Clockwise90Degrees);
            cameraCapture.SetRecordRotation(VideoRotation.Clockwise90Degrees);
            #endif

            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Visibility = Windows.UI.Xaml.Visibility.Visible;
            previewElement.Source = cameraCapture;
            await cameraCapture.StartPreviewAsync();
        }

        private async void previewElement_Tapped(object sender, TappedRoutedEventArgs e)
        {
            // Block multiple taps.
            if (!IsCaptureInProgress)
            {                
                IsCaptureInProgress = true;

                // Create the temporary storage file.
                media = await ApplicationData.Current.LocalFolder
                    .CreateFileAsync("capture_file.jpg", CreationCollisionOption.ReplaceExisting);

                // Take the picture and store it locally as a JPEG.
                await cameraCapture.CapturePhotoToStorageFileAsync(
                    ImageEncodingProperties.CreateJpeg(), media);

                captureButtons.Visibility = Visibility.Visible;

				// Use the stored image as the preview source.
                BitmapImage tempBitmap = new BitmapImage(new Uri(media.Path));
                imagePreview.Source = tempBitmap;
                imagePreview.Visibility = Visibility.Visible;
                previewElement.Visibility = Visibility.Collapsed;
                IsCaptureInProgress = false;
            }
        }

        private async void cameraCapture_Failed(MediaCapture sender, MediaCaptureFailedEventArgs errorEventArgs)
        {
            // It's safest to return this back onto the UI thread to show the message dialog.
            MessageDialog dialog = new MessageDialog(errorEventArgs.Message);
            await this.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, 
                async () => { await dialog.ShowAsync(); });            
        }

        private async void ButtonCapture_Click(object sender, RoutedEventArgs e)
        {
            // Prevent multiple initializations.
            ButtonCapture.IsEnabled = false;

            await CaptureImage();
        }

        private async void ButtonRetake_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            // Reset the capture and then start again.
            await ResetCaptureAsync();
            await CaptureImage();
        }

        private async void ButtonCancel_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            await ResetCaptureAsync();
        }

        private async Task ResetCaptureAsync()
        {
            captureGrid.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            imagePreview.Visibility = Visibility.Collapsed;
            captureButtons.Visibility = Visibility.Collapsed;
            previewElement.Source = null;
            ButtonCapture.IsEnabled = true;

            // Make sure we stop the preview and release resources.
            await cameraCapture.StopPreviewAsync();
            cameraCapture.Dispose();
            media = null;
        }

  	このコードにより、イメージをキャプチャするための UI が表示され、イメージがストレージ ファイルに保存されます。

6. 既存の `InsertTodoItem` メソッドを次のコードに置き換えます。
 
        private async Task InsertTodoItem(TodoItem todoItem)
        {
            string errorString = string.Empty;

            if (media != null)
            {
                // Set blob properties of TodoItem.
                todoItem.ContainerName = "todoitemimages";

                // Use a unigue GUID to avoid collisions.
                todoItem.ResourceName = Guid.NewGuid().ToString();
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

                // Get the new image as a stream.
                using (var inputStream = await media.OpenReadAsync())
                {
                    // Upload the new image as a BLOB from the stream.
                    CloudBlockBlob blobFromSASCredential =
                        container.GetBlockBlobReference(todoItem.ResourceName);
                    await blobFromSASCredential.UploadFromStreamAsync(inputStream);
                }

                // When you request an SAS at the container-level instead of the blob-level,
                // you are able to upload multiple streams using the same container credentials.

                await ResetCaptureAsync();
            }

            // Add the new item to the collection.
            items.Add(todoItem);
        }

	このコードは、新しい TodoItem を挿入する要求をモバイル サービスに送信します。応答には、ローカル ストレージから Azure Blob ストレージにイメージをアップロードするために使用される SAS が含まれています。アップロードされたイメージの URL は、データ バインドで使用されます。

最後の手順では、アプリケーションの両方のバージョンをテストし、両方のデバイスからのアップロードが成功するかどうかを検証します。
		
##<a name="test"></a>アプリケーションのイメージのアップロードをテストする

1. Visual Studio で、Windows プロジェクトが既定のプロジェクトとして設定されていることを確認してから、F5 キーを押してアプリケーションを実行します。

2. **[Insert a TodoItem]** のテキスト ボックスにテキストを入力し、**[Photo]** をクリックします。

3. プレビューをクリックまたはタップして写真を撮ってから、**[Upload]** をクリックして新しい項目を挿入し、イメージをアップロードします。

	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-appbar2.png)

4. 新しい項目がアップロード イメージと共にリスト ビューに表示されます。

	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-quickstart-blob-ie.png)

   	>[AZURE.NOTE]新しい項目の *imageUri* プロパティが **Image** コントロールにバインドされると、イメージは BLOB ストレージから自動的にダウンロードされます。

5. アプリケーションを停止し、Windows Phone プロジェクト バージョンのアプリケーションを再起動します。

	前にアップロードされた画像が表示されます。

6. 以前と同様、テキスト ボックスに何らかのテキストを入力してから、**[Photo]** をクリックします。

   	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-wp8.png)

3. プレビューをタップして写真を撮ってから、**[Upload]** をクリックして新しい項目を挿入し、イメージをアップロードします。

	![](./media/mobile-services-windows-universal-dotnet-upload-to-blob-storage/mobile-upload-blob-app-view-final-wp8.png)

これで、イメージのアップロードのチュートリアルは完了です。

<!---HONumber=July15_HO3-->