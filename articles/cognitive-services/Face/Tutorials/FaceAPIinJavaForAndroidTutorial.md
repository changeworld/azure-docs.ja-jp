---
title: Face API Java for Android チュートリアル | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: このチュートリアルでは、Cognitive Services Face サービスを利用して画像の中にある顔を検出し、フレームに収める単純な Android アプリを作成します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: nolachar
ms.openlocfilehash: ad7b85b378db9e9687b5f8081bc9832e91e9ee5e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125638"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>チュートリアル: 画像の中にある顔を検出してフレームに収める Android アプリの作成

このチュートリアルでは、Face サービス Java クラス ライブラリを使用して画像の中にある人間の顔を検出する単純な Android アプリケーションを作成します。 このアプリケーションは、選択された画像から顔を検出し、それぞれ四角形のフレームに収めて表示します。 完全なサンプル コードは、[Android で画像の中にある顔を検出してフレームに収める方法](https://github.com/Azure-Samples/cognitive-services-face-android-sample)についての GitHub ページで入手できます。

![Android のスクリーンショット (赤色の四角形のフレームに顔を収めた写真)](../Images/android_getstarted2.1.PNG)

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> - Android アプリケーションを作成する
> - Face サービス クライアント ライブラリのインストール
> - クライアント ライブラリを使用して画像の中にある顔を検出する
> - 検出された顔の周囲にそれぞれフレームを描画する

## <a name="prerequisites"></a>前提条件

- サンプルを実行するにはサブスクリプション キーが必要です。 無料試用版のサブスクリプション キーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=face-api)」から取得できます。
- [Android Studio](https://developer.android.com/studio/) と SDK 22 以上 (Face クライアント ライブラリで必要)。
- Maven の [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) Face クライアント ライブラリ。 パッケージをダウンロードする必要はありません。 インストールの手順は、以降で説明しています。

## <a name="create-the-project"></a>プロジェクトを作成する

次の手順に従って Android アプリケーション プロジェクトを作成します。

1. Android Studio を起動します。 このチュートリアルでは、Android Studio 3.1 を使用します。
1. **[Start a new Android Studio project]\(新しい Android Studio プロジェクトを開始する\)** を選択します。
1. **[Create Android Project]\(Android プロジェクトの作成\)** 画面で、必要に応じて既定のフィールドに変更を加え、**[次へ]** をクリックします。
1. **[Target Android Devices]\(ターゲットの Android デバイス\)** 画面で、ドロップダウン リストから **API 22** 以降を選択し、**[Next]\(次へ\)** をクリックします。
1. **[Empty Activity]\(空のアクティビティ\)** を選択し、**[Next]\(次へ\)** をクリックします。
1. **[Backwards Compatibility]\(下位互換性\)** チェック ボックスをオフにして **[Finish]\(完了\)** をクリックします。

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>画像を選択して表示するための UI の作成

*activity_main.xml* を開いてレイアウト エディターを表示します。 **[Text]\(テキスト\)** タブを選択し、その内容を次のコードに置き換えます。

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

*MainActivity.java* を開いて、先頭の `package` ステートメントを除くすべての内容を以下のコードに置き換えます。

このコードは、新しいアクティビティを開始するイベント ハンドラーを `Button` に対して設定し、ユーザーが写真を選択できるようにするものです。 選択された写真は、`ImageView` に表示されます。

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

public class MainActivity extends Activity {
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
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

これでアプリから写真を参照し、下の画像のように、その写真をウィンドウに表示できるようになります。

![Android スクリーンショット (顔が写った写真)](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Face クライアント ライブラリの構成

Face API はクラウド API であり、HTTPS 要求を使って呼び出すことができます。 開発作業を省力化するために、こうした Web 要求は Face クライアント ライブラリによってカプセル化されています。このチュートリアルでは、Face クライアント ライブラリを使用します。

**[Project]\(プロジェクト\)** ウィンドウで、ドロップダウン リストから **[Android]** を選択します。 **[Gradle Scripts]\(Gradle スクリプト\)** を展開し、*[build.gradle (Module: app)]* を開きます。

次のスクリーンショットに示したように、Face クライアント ライブラリの依存関係 (`com.microsoft.projectoxford:face:1.4.3`) を追加し、**[Sync Now]\(今すぐ同期\)** をクリックします。

![Android Studio スクリーンショット (App build.gradle ファイル)](../Images/face-tut-java-gradle.png)

**MainActivity.java** を開いて、次の import ディレクティブを追加します。

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Face クライアント ライブラリ コードの追加

`MainActivity` クラスの `onCreate` メソッドの上に、次のコードを挿入します。

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

`<API endpoint>` は、実際のキーに割り当てられた API エンドポイントで置き換えてください。 無料試用版のサブスクリプション キーは、**westcentralus** リージョンで生成されます。 そのため無料試用版のサブスクリプション キーを使用する場合、次のようなステートメントになります。

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

`<Subscription Key>` は、実際のサブスクリプション キーで置き換えてください。 例: 

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

**[Project]\(プロジェクト\)** ウィンドウで **[app]\(アプリ\)**、**[manifests]\(マニフェスト\)** を順に展開し、*AndroidManifest.xml* を開きます。

`manifest` 要素の直接の子として、次の要素を挿入します。

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

プロジェクトをビルドし、エラーがないか確認します。 これで、Face サービスを呼び出す準備が整いました。

## <a name="upload-an-image-to-detect-faces"></a>画像をアップロードして顔を検出する

顔を検出する最も簡単な方法は、`FaceServiceClient.detect` メソッドを呼び出すことです。 このメソッドは、[Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API メソッドをラップし、`Face` の配列を返します。

返された `Face` にはそれぞれ、その場所を示す四角形と一連の任意の顔属性が含まれます。 この例で必要なのは顔の位置情報だけです。

エラーが発生した場合、根本的な理由が `AlertDialog` に表示されます。

次のメソッドを `MainActivity` クラスに挿入します。

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
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

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>画像の中にある顔をフレームに収める

以下のヘルパー メソッドを `MainActivity` クラスに挿入します。 検出された顔の周囲にはそれぞれ、このメソッドによって四角形が描画されます。

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
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

`detectAndFrame` メソッド内の、`TODO` コメントで示した `AsyncTask` のメソッドを完成させます。 成功時には、選択された画像が `ImageView` に表示され、それぞれの顔がフレームに収められます。

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
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

最後に `onActivityResult` メソッドで、`detectAndFrame` メソッド呼び出しのコメントを解除します。次のコードを参照してください。

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>アプリの実行

アプリケーションを実行し、顔が写っている画像を参照します。 Face サービスが応答するまで数秒お待ちください。 数秒後、下の画像のような結果が表示されます。

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>まとめ

このチュートリアルでは、Face サービスを使用するための基本的なプロセスを学習し、画像の中にある顔をフレームに収めて表示するアプリケーションを作成しました。

## <a name="next-steps"></a>次の手順

顔のランドマークの検出と使用について学習してください。

> [!div class="nextstepaction"]
> [画像内の顔を検出する方法](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

姿勢、性別、年齢、頭部、顔ひげ、メガネなど、顔とその属性を検出するために使用される Face API について考察します。

> [!div class="nextstepaction"]
> [Face API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)