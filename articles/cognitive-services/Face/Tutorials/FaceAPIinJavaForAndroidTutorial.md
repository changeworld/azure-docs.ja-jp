---
title: Face API Java for Android チュートリアル | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Cognitive Services Face API を利用して画像の中にある人間の顔を検出し、フレームに収める単純な Android アプリを作成します。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377813"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Java for Android での Face API の使用開始チュートリアル

このチュートリアルでは、Face API を呼び出し、画像の中にある人間の顔を検出する単純な Android アプリケーションを開発する方法について学習します。 このアプリケーションでは、結果的に、検出した顔がフレームに収められます。

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> 準備

このチュートリアルを使用するには、次の前提条件が必要となります。

- Android Studio と SDK をインストールしていること
- Android デバイス (テストする場合に必要)

## <a name="step1"></a>手順 1: Face API の利用を申し込み、サブスクリプション キーを入手する

Face API を使用する前に、Microsoft Cognitive Services ポータルで新規登録し、Face API の利用を申し込む必要があります。 [サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページを参照してください。 プライマリ キーとセカンダリ キーの両方をこのチュートリアルで利用できます。

## <a name="step2"></a>手順 2: アプリケーション フレームワークを作成する

この手順では、Android アプリケーション プロジェクトを作成し、画像を選択して表示するための基本 UI を実装します。 次の手順に従ってください。 

1. Android Studio を起動します。
2. [ファイル] メニューの **[新しいプロジェクト]** をクリックします。
3. アプリケーションに "**MyFirstApp**" という名前を付け、[次へ] をクリックします。 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. 必要に応じてターゲット プラットフォームを選択し、[次へ] をクリックします。 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. **[Basic Activity]\(基本アクティビティ\)** を選択し、[次へ] をクリックします。
6. アクティビティに次のように名前を付け、[完了] をクリックします。 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. **activity_main.xml** を開きます。このアクティビティのレイアウト エディターが表示されるはずです。
8. テキスト ソース ファイルを表示し、アクティビティ レイアウトを次のように編集します。

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. **MainActivity.java** を開き、ファイルの先頭に次のインポート ディレクティブを挿入します。

    ```java
    import java.io.*; 
    import android.app.*; 
    import android.content.*; 
    import android.net.*; 
    import android.os.*; 
    import android.view.*; 
    import android.graphics.*; 
    import android.widget.*; 
    import android.provider.*;
    ```
      
    次に、クラスを次のように変更します。  
    
    ```java
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;
         
    @Override
    protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_main);
           Button button1 = (Button)findViewById(R.id.button1);
           button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

これでアプリはギャラリーから写真を参照し、下の画像のようにウィンドウに表示できます。

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>手順 3: Face API クライアント ライブラリを構成する

Face API は HTTPS 要求を利用して呼び出せるクラウド API です。 .NET プラットフォーム アプリケーションで Face API をもっと簡単に利用するために、Web 要求をカプセル化する目的でクライアント ライブラリも提供されます。 この例では、そのクライアント ライブラリを利用し、作業を簡単にしています。 

次の指示に従い、クライアント ライブラリを構成します。 

1. 例に表示されている [プロジェクト] パネルからプロジェクトの最上位 **build.gradle** ファイルを見つけます。 プロジェクト ツリーには **build.gradle** ファイルが他にもいくつかあります。最初に最上位 **build.gradle** ファイルを開く必要があります。
2. **mavenCentral()** をプロジェクトのリポジトリに追加します。 Android Studio の既定のリポジトリである jcenter() を使用することもできます。jcenter() が mavenCentral() の上位集合であるためです。  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. 'アプリ' プロジェクトの **build.gradle** ファイルを開きます。
4. Maven Central Repository に保存されているクライアント ライブラリの依存関係を追加します。

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. 'アプリ' プロジェクトで **MainActivity.java** を開き、次のインポート ディレクティブを挿入します。 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   次に、クラスに次のコードを挿入します。

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   上記の最初のパラメーターを、手順 1 でキーに割り当てた API エンドポイントで置き換えます。 例: 
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   2 番目のパラメーターを、手順 1 で取得したサブスクリプション キーで置き換えます。
   
6. 'アプリ' プロジェクトでファイル **AndroidManifest.xml** を開きます。 **manifest** 要素の子として次の要素を挿入します。  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. これで、アプリケーションから Face API を呼び出す準備ができました。 

## <a name="step4"></a>手順 4: 顔を検出する画像をアップロードする

顔を検出する最も簡単な方法は、画像ファイルを直接アップロードし、[Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API を呼び出すことです。 クライアント ライブラリを使用するとき、**FaceServiceClient** クラスの非同期メソッド **DetectAsync** を利用することでこれを行うことができます。 返された顔にはそれぞれ、その場所を示す四角形と一連の任意の顔属性が含まれます。 この例では、顔の場所のみを取得する必要があります。 ここで、顔検出のために **MainActivity** クラスにメソッドを挿入する必要があります。 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
                        return null;
                    }
                }
                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };
        detectTask.execute(inputStream);
    }
```

## <a name="step5"></a>手順 5: 画像の中の顔をマークする

この最後の手順では、上記の手順をすべて組み合わせ、画像の中で検出された顔にフレームを付けます。 最初に、**MainActivity.java** を開き、四角形を描くためのヘルパー メソッドを挿入します。 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
        if (faces != null) {
            for (Face face : faces) {
                FaceRectangle faceRectangle = face.faceRectangle;
                canvas.drawRect(
                        faceRectangle.left,
                        faceRectangle.top,
                        faceRectangle.left + faceRectangle.width,
                        faceRectangle.top + faceRectangle.height,
                        paint);
            }
        }
        return bitmap;
    }
```

次に、顔をフレームに収め、ステータスを報告する目的で、**detectAndFrame** メソッドの TODO 部分を完了します。

```java
    @Override
    protected void onPreExecute() {
        detectionProgressDialog.show();
    }
    @Override
    protected void onProgressUpdate(String... progress) {
        detectionProgressDialog.setMessage(progress[0]);
    }
    @Override
    protected void onPostExecute(Face[] result) {
        detectionProgressDialog.dismiss();
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
最後に、下の画像のように、**onActivityResult** メソッドからの **detectAndFrame** メソッドの呼び出しを追加します。 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

このアプリケーションを実行し、顔が含まれる画像を参照します。 クラウド API が応答するまで数秒お待ちください。 数秒後、下の画像のような結果が表示されます。 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> まとめ

このチュートリアルでは、Face API を使用するための基本的なプロセスを学習し、画像内の顔マークを表示するアプリケーションを作成しました。 Face API の詳細については、ハウツー記事と [API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)を参照してください。 

## <a name="related"></a> 関連チュートリアル

- [C# での Face API の使用開始チュートリアル](FaceAPIinCSharpTutorial.md)
- [Python での Face API の使用開始チュートリアル](FaceAPIinPythonTutorial.md)
