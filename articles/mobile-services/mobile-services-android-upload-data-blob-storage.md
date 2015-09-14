<properties 
	pageTitle="Mobile Services を使用したイメージの BLOB ストレージへのアップロード (Android) | Mobile Services"
	description="Mobile Services を使用してイメージを Azure Storage にアップロードし、そのイメージに Android アプリからアクセスする方法を説明します。"
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="ricksal"/>

# Android デバイスから Azure Storage へのイメージのアップロード

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

このトピックでは、Android 向け Azure Mobile Services アプリで Azure Storage にイメージをアップロードできるようにする方法について説明します。

Mobile Services では、SQL データベースを使用してデータを保存します。ただし、Azure Storage にバイナリ ラージ オブジェクト (BLOB) データを格納する方が効率的です。このチュートリアルでは、Mobile Services クイック スタート アプリで Android のカメラを使って写真を撮り、その画像を Azure Storage にアップロードできるようにします。


## はじめにやるべきこと

このチュートリアルを開始する前に、[Mobile Services の使用]に関するクイック スタートを完了している必要があります。

このチュートリアルには、次のものも必要です。

+ [Azure ストレージ アカウント](../storage-create-storage-account.md)
+ カメラ付きの Android デバイス

## アプリの動作

写真画像をアップロードするプロセスは、複数の手順で構成されます。

- 最初に写真を撮影し、Azure Storage で使用される新しいメタデータ フィールドを含む TodoItem 行を SQL データベースに挿入します。
- 新しいモバイル サービスの SQL **insert** スクリプトで、Azure Storage に対して Shared Access Signature (SAS) を要求します。
- このスクリプトにより、SAS と BLOB の URI がクライアントに返されます。
- クライアントは、SAS と BLOB URI を使用して写真をアップロードします。

SAS とは

Azure Storage サービスにデータをアップロードするために必要な資格情報をクライアント アプリ内に保存するのは安全ではありません。代わりに、これらの資格情報をモバイル サービスに保存し、それらを使用して、新しいイメージをアップロードするためのアクセス許可を付与する SAS (Shared Access Signature) を生成します。SAS は有効期間が 5 分間の資格情報で、Mobile Services によってクライアント アプリに安全に返されます。アプリケーションは、この一時的な資格情報を使用してイメージをアップロードします。詳細については、[Shared Access Signature の第 1 部である SAS モデル](storage-dotnet-shared-access-signature-part-1.md)に関するページを参照してください。

>[AZURE.NOTE] [Here](https://github.com/Azure/mobile-services-samples/tree/master/UploadImages)から、このアプリの完成したクライアント ソース コードを入手できます。

## 管理ポータルで登録されている挿入スクリプトを更新する

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]


## イメージをキャプチャしてアップロードするようにクイック スタート アプリケーションを更新する

### Azure Storage Android クライアント ライブラリを参照する

1. ライブラリへの参照を追加するには、**app** の **build.gradle** ファイルで、`dependencies` セクションに次の行を追加します。

		compile 'com.microsoft.azure.android:azure-storage-android:0.6.0@aar'


2. `minSdkVersion` の値を 15 (カメラの API で必要な値) に変更します。

3. **[Sync Project with Gradle Files]** アイコンをクリックします。

### カメラとストレージのマニフェスト ファイルを更新する

1. このアプリには使用可能なカメラが用意されている必要があることを指定します。これには、**AndroidManifest.xml** に次の行を追加します。

	    <uses-feature android:name="android.hardware.camera"
	        android:required="true" />

2. このアプリでは外部ストレージへの書き込みアクセス許可が必要であることを指定します。これには、**AndroidManifest.xml** に次の行を追加します。

	    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

	Android の外部ストレージには必ずしも SD カードであるとは限りません。詳細については、[ファイルの保存](http://developer.android.com/training/basics/data-storage/files.html)に関するページを参照してください。

### 新しいユーザー インターフェイスのリソース ファイルを更新する

1. *values* ディレクトリにある **strings.xml** ファイルに次の行を追加して、新しいボタンのタイトルを追加します。

	    <string name="preview_button_text">Take Photo</string>
	    <string name="upload_button_text">Upload</string>

2. **res\\layout** フォルダー内の **Activity\_to\_do.xml** ファイルで、**Add** ボタンの既存のコードの前に次のコードを追加します。

         <Button
             android:id="@+id/buttonPreview"
             android:layout_width="120dip"
             android:layout_height="wrap_content"
             android:onClick="takePicture"
             android:text="@string/preview_button_text" />

3. **Add** ボタン要素を次のコードに置き換えます。

         <Button
             android:id="@+id/buttonUpload"
             android:layout_width="100dip"
             android:layout_height="wrap_content"
             android:onClick="uploadPhoto"
             android:text="@string/upload_button_text" />


### 写真をキャプチャするためのコードを追加する

1. **ToDoActivity.java** で、次のコードを追加して、一意の名前を持つ **File** オブジェクトを作成します。

		// Create a File object for storing the photo
	    private File createImageFile() throws IOException {
	        // Create an image file name
	        String timeStamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
	        String imageFileName = "JPEG_" + timeStamp + "_";
	        File storageDir = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES);
	        File image = File.createTempFile(
	                imageFileName,  /* prefix */
	                ".jpg",         /* suffix */
	                storageDir      /* directory */
	        );
	        return image;
	    }

5. Android のカメラ アプリを起動する次のコードを追加します。これで、写真を撮影できます。問題がなければ **[Save]** を押します。先ほど作成したファイルに写真が格納されます。

		// Run an Intent to start up the Android camera
	    static final int REQUEST_TAKE_PHOTO = 1;
	    public Uri mPhotoFileUri = null;
	    public File mPhotoFile = null;
		
	    public void takePicture(View view) {
	        Intent takePictureIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	        // Ensure that there's a camera activity to handle the intent
	        if (takePictureIntent.resolveActivity(getPackageManager()) != null) {
	            // Create the File where the photo should go
	            try {
	                mPhotoFile = createImageFile();
	            } catch (IOException ex) {
	                // Error occurred while creating the File
	                //
	            }
	            // Continue only if the File was successfully created
	            if (mPhotoFile != null) {
	                mPhotoFileUri = Uri.fromFile(mPhotoFile);
	                takePictureIntent.putExtra(MediaStore.EXTRA_OUTPUT, mPhotoFileUri);
	                startActivityForResult(takePictureIntent, REQUEST_TAKE_PHOTO);
	            }
	        }
	    }

### 写真のファイルを BLOB ストレージにアップロードするコードを追加する


1. 最初に、**ToDoItem.java** に次のコードを追加して、いくつかの新しいメタデータ フィールドを `ToDoItem` オブジェクトに追加します。

		/**
	     *  imageUri - points to location in storage where photo will go
	     */
	    @com.google.gson.annotations.SerializedName("imageUri")
	    private String mImageUri;
	
	    /**
	     * Returns the item ImageUri
	     */
	    public String getImageUri() {
	        return mImageUri;
	    }
	
	    /**
	     * Sets the item ImageUri
	     *
	     * @param ImageUri
	     *            Uri to set
	     */
	    public final void setImageUri(String ImageUri) {
	        mImageUri = ImageUri;
	    }
	
	    /**
	     * ContainerName - like a directory, holds blobs
	     */
	    @com.google.gson.annotations.SerializedName("containerName")
	    private String mContainerName;
	
	    /**
	     * Returns the item ContainerName
	     */
	    public String getContainerName() {
	        return mContainerName;
	    }
	
	    /**
	     * Sets the item ContainerName
	     *
	     * @param ContainerName
	     *            Uri to set
	     */
	    public final void setContainerName(String ContainerName) {
	        mContainerName = ContainerName;
	    }
	
	    /**
	     *  ResourceName
	     */
	    @com.google.gson.annotations.SerializedName("resourceName")
	    private String mResourceName;
	
	    /**
	     * Returns the item ResourceName
	     */
	    public String getResourceName() {
	        return mResourceName;
	    }
	
	    /**
	     * Sets the item ResourceName
	     *
	     * @param ResourceName
	     *            Uri to set
	     */
	    public final void setResourceName(String ResourceName) {
	        mResourceName = ResourceName;
	    }
	
	    /**
	     *  SasQueryString - permission to write to storage
	     */
	    @com.google.gson.annotations.SerializedName("sasQueryString")
	    private String mSasQueryString;
	
	    /**
	     * Returns the item SasQueryString
	     */
	    public String getSasQueryString() {
	        return mSasQueryString;
	    }
	
	    /**
	     * Sets the item SasQueryString
	     *
	     * @param SasQueryString
	     *            Uri to set
	     */
	    public final void setSasQueryString(String SasQueryString) {
	        mSasQueryString = SasQueryString;
	    }

2. パラメーターを持たないコンストラクターを次のコードに置き換えます。

	    /**
	     * ToDoItem constructor
	     */
	    public ToDoItem() {
	        mContainerName = "";
	        mResourceName = "";
	        mImageUri = "";
	        mSasQueryString = "";
	    }

3. 複数のパラメーターを持つコンストラクターを次のコードに置き換えます。

	    /**
	     * Initializes a new ToDoItem
	     *
	     * @param text
	     *            The item text
	     * @param id
	     *            The item id
	     */
	    public ToDoItem(String text,
	                    String id,
	                    String containerName,
	                    String resourceName,
	                    String imageUri,
	                    String sasQueryString) {
	        this.setText(text);
	        this.setId(id);
	        this.setContainerName(containerName);
	        this.setResourceName(resourceName);
	        this.setImageUri(imageUri);
	        this.setSasQueryString(sasQueryString);
	    }


4. **ToDoActivity.java** ファイルで、**ToDoActivity.java** 内の **addItem** メソッドを、イメージをアップロードする次のコードに置き換えます。

	    /**
	     * Add a new item
	     *
	     * @param view
	     *            The view that originated the call
	     */
	    public void uploadPhoto(View view) {
	        if (mClient == null) {
	            return;
	        }
	
	        // Create a new item
	        final ToDoItem item = new ToDoItem();
	
	        item.setText(mTextNewToDo.getText().toString());
	        item.setComplete(false);
	        item.setContainerName("todoitemimages");
	
	        // Use a unigue GUID to avoid collisions.
	        UUID uuid = UUID.randomUUID();
	        String uuidInString = uuid.toString();
	        item.setResourceName(uuidInString);
	
	        // Send the item to be inserted. When blob properties are set this
	        // generates an SAS in the response.
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
		                    final ToDoItem entity = addItemInTable(item);
		
		                    // If we have a returned SAS, then upload the blob.
		                    if (entity.getSasQueryString() != null) {
		
	                       // Get the URI generated that contains the SAS
	                        // and extract the storage credentials.
	                        StorageCredentials cred = 
								new StorageCredentialsSharedAccessSignature(entity.getSasQueryString());
	                        URI imageUri = new URI(entity.getImageUri());
	
	                        // Upload the new image as a BLOB from a stream.
	                        CloudBlockBlob blobFromSASCredential =
	                                new CloudBlockBlob(imageUri, cred);
	
	                        blobFromSASCredential.uploadFromFile(mPhotoFileUri.getPath());
  	                    }
	
	                    runOnUiThread(new Runnable() {
	                        @Override
	                        public void run() {
	                            if(!entity.isComplete()){
	                                mAdapter.add(entity);
	                            }
	                        }
	                    });
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	
	        runAsyncTask(task);
	
	        mTextNewToDo.setText("");
	    }
	

このコードは、新しい TodoItem を挿入する要求をモバイル サービスに送信します。応答には、ローカル ストレージから Azure Storage の BLOB にイメージをアップロードするために使用される SAS が含まれます。


## イメージのアップロードをテストする 

1. Android Studio で、**[Run]** を押します。ダイアログ ボックスで、使用するデバイスを選択します。

2. アプリの UI が表示されたら、**[Add a ToDo item]** というラベルが付いたテキスト ボックスにテキストを入力します。

3. **[Take Photo]** を押します。カメラ アプリが起動したら、写真を撮影します。チェック マークを押して写真を受け入れます。

4. **[Upload]** を押します。通常どおり、ToDoItem が一覧に追加されていることを確認します。

5. Microsoft Azure ポータルで、ストレージ アカウントに移動します。**[コンテナー]** タブを押し、一覧内のコンテナーの名前を押します。

6. アップロードされている BLOB ファイルの一覧が表示されます。いずれかのファイルを選択し、**[ダウンロード]** を押します。

7. アップロードした画像がブラウザー ウィンドウに表示されます。


## <a name="next-steps"> </a>次のステップ

モバイル サービスを Blob サービスと統合することによりイメージを安全にアップロードできました。他のバックエンド サービスおよび統合のトピックを参照してください。

+ [SendGrid を使用した Mobile Services からの電子メールの送信]
 
  SendGrid 電子メール サービスを使用して、モバイル サービスに電子メール機能を追加する方法について説明します。このトピックでは、サーバー側スクリプトを追加し、SendGrid を使用して電子メールを送信する方法を示します。

+ [Mobile Services でのバックエンド ジョブの計画]

  Mobile Services のジョブ スケジューラ機能を使用して、定義したスケジュールに従って実行されるサーバー スクリプト コードを定義する方法について説明します。

+ [Mobile Services のサーバー スクリプト リファレンス]

  サーバー側のタスクを実行するサーバー スクリプトの使用および他の Azure コンポーネントおよび外部リソースとの統合に関するトピックを参照してください。
 
+ [Mobile Services .NET の使用方法の概念リファレンス]

  .NET で Mobile Services を使用する方法について説明します
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[SendGrid を使用した Mobile Services からの電子メールの送信]: store-sendgrid-mobile-services-send-email-scripts.md
[Mobile Services でのバックエンド ジョブの計画]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Mobile Services のサーバー スクリプト リファレンス]: mobile-services-how-to-use-server-scripts.md
[Mobile Services の使用]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET の使用方法の概念リファレンス]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=September15_HO1-->