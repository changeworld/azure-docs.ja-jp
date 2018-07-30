---
title: Face API C# チュートリアル | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: このチュートリアルでは、Cognitive Services Face サービスを利用して画像の中にある顔を検出し、フレームに収める単純な Windows アプリを作成します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: nolachar
ms.openlocfilehash: e4f2192c40f0b650b31ed59642dee89e42eca703
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125942"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image"></a>チュートリアル: 画像の中にある顔を検出してフレームに収める WPF アプリの作成

このチュートリアルでは、.NET クライアント ライブラリを通じて Face サービスを使用する Windows Presentation Framework (WPF) アプリケーションを作成します。 このアプリは、画像内の顔を検出し、各顔のまわりにフレームを描画し、ステータス バーに顔の説明を表示します。 完全なサンプル コードは、[Windows で画像の中にある顔を検出してフレームに収める方法](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample)についての GitHub ページで入手できます。

![顔を検出して四角形のフレームに収めた画像のスクリーンショット](../Images/getting-started-cs-detected.png)

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> - WPF アプリケーションを作成する
> - Face サービス クライアント ライブラリのインストール
> - クライアント ライブラリを使用して画像の中にある顔を検出する
> - 検出された顔の周囲にそれぞれフレームを描画する
> - 顔の説明をステータス バーに表示する

## <a name="prerequisites"></a>前提条件

- サンプルを実行するにはサブスクリプション キーが必要です。 無料試用版のサブスクリプション キーは「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/?api=face-api)」から取得できます。
- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション。 Visual Studio 2017 の場合、.NET デスクトップ アプリケーション開発ワークロードが必要です。 このチュートリアルでは、Visual Studio 2017 Community Edition を使用します。
- [Microsoft.Azure.CognitiveServices.Vision.Face 2.0.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.0.0-preview) クライアント ライブラリの NuGet パッケージ。 パッケージをダウンロードする必要はありません。 インストールの手順は、以降で説明しています。

## <a name="create-the-visual-studio-solution"></a>Visual Studio ソリューションを作成する

次の手順に従って、Windows WPF アプリケーション プロジェクトを作成します。

1. Visual Studio を開き、**[ファイル]** メニューから **[新規作成]**、**[プロジェクト]** の順にクリックします。
   - Visual Studio 2017 で、**[インストール済み]**、**[他の言語]** の順に展開します。 **[Visual C#]**、**[WPF アプリ (.NET Framework)]** の順に選択します。
   - Visual Studio 2015 で、**[インストール済み]**、**[テンプレート]** の順に展開します。 **[Visual C#]**、**[WPF アプリケーション]** の順に選択します。
1. アプリケーションの名前を **FaceTutorial** に設定し、**[OK]** をクリックします。

## <a name="install-the-face-service-client-library"></a>Face サービス クライアント ライブラリのインストール

次の手順に従ってクライアント ライブラリをインストールします。

1. **[ツール]** メニューで、**[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順に選択します。
1. **[パッケージ マネージャー コンソール]** に次のコードを貼り付けて、**Enter** キーを押します。

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.0.0-preview`

## <a name="add-the-initial-code"></a>初期コードの追加

### <a name="mainwindowxaml"></a>MainWindow.xaml

*MainWindow.xaml* を開き (ヒント: **上下の矢印アイコン**でウィンドウを入れ替えることができます)、その内容を次のコードに置き換えてください。 この xaml コードを使用して UI ウィンドウを作成します。 イベント ハンドラーである `FacePhoto_MouseMove` と `BrowseButton_Click` に注目してください。

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

*[MainWindow.xaml]* を展開し、*MainWindow.xaml.cs* を開いて、その内容を以下のコードに置き換えます。 赤色の波線 (下線) は無視してください。初回ビルド後に消えます。

先頭の 2 行は、クライアント ライブラリの名前空間をインポートするものです。 次に、`MainWindow` コンストラクターで Azure リージョンが設定される間に、サブスクリプション キーを引数として `FaceClient` が作成されます。 `BrowseButton_Click` と `FacePhoto_MouseMove` の 2 つのメソッドは、*MainWindow.xaml* に宣言されていたイベント ハンドラーに対応します。

`BrowseButton_Click` は、ユーザーが jpg 画像を選択するための `OpenFileDialog` を作成します。 メイン ウィンドウには、この画像が読み込まれて表示されます。 `BrowseButton_Click` の残りのコードと `FacePhoto_MouseMove` のコードは、以降の手順で挿入します。

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string baseUri =
            "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(baseUri, UriKind.Absolute))
            {
                faceClient.BaseUri = new Uri(baseUri);
            }
            else
            {
                MessageBox.Show(baseUri,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>サブスクリプション キーの挿入とリージョンの確認または変更

- *MainWindow.xaml.cs* から次の行を見つけて、`<Subscription Key>` を実際の Face API サブスクリプション キーに置き換えます。

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- *MainWindow.xaml.cs* から次の行を見つけて、実際のサブスクリプション キーに関連付けられている Azure リージョンに置き換えるか、確認します。

    ```csharp
    private const string baseUri =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
    ```

    サブスクリプション キーの入手先と同じ場所であることを確認してください。 たとえば、**westus** リージョンからサブスクリプション キーを取得した場合、`Westcentralus` を `Westus` に置き換えます。

    無料評価版を使用してサブスクリプション キーを取得した場合、キーのリージョンは **westcentralus** に設定されているため、変更は不要です。

### <a name="test-the-app"></a>アプリケーションをテストする

アプリをテストするために、メニューにある **[開始]** をクリックします。 ウィンドウが開いたら、左下隅の **[Browse]** をクリックします。 **[ファイルを開く]** ダイアログが表示されます。ウィンドウには、このダイアログで参照して選択した写真が表示されます。

![未変更の顔の画像を表示するスクリーンショット](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>画像をアップロードして顔を検出する

顔を検出する最も簡単な方法は、[Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API メソッドをラップする `FaceClient.Face.DetectWithStreamAsync` メソッドを呼び出して、ローカル画像をアップロードすることです。

`MainWindow` クラスの `FacePhoto_MouseMove` メソッドの下に以下のメソッドを挿入します。

分析の対象となる顔の各種属性が作成されると共に、送信した画像ファイルが `Stream` に読み込まれます。 その両方が `DetectWithStreamAsync` 呼び出しに渡されます。

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>それぞれの顔の周囲に四角形を描画する

画像の中から検出された顔の周囲にそれぞれ四角形を描画するコードを追加します。

*MainWindow.xaml.cs* で、`async` 修飾子を `BrowseButton_Click` メソッドに追加します。

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

`BrowseButton_Click` メソッドの末尾 (`FacePhoto.Source = bitmapSource` 行の後) に次のコードを追加します。

`UploadAndDetectFaces` の呼び出しによって、検出された顔のリストが設定されます。 次に、それぞれの顔の周囲に四角形が描画され、変更済みの画像がメイン ウィンドウに表示されます。

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>画像内の顔を説明する

`MainWindow` クラスの `UploadAndDetectFaces` メソッドの下に以下のメソッドを追加します。

これは顔の属性を、顔を表す文字列に変換するメソッドです。 顔の四角形にマウス ポインターを重ねると、その文字列が表示されます。

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>顔の説明を表示する

`FacePhoto_MouseMove` メソッドを次のコードに置き換えます。

顔の四角形にマウス ポインターを重ねると、このイベント ハンドラーによって顔の説明の文字列が表示されます。

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>アプリの実行

このアプリケーションを実行し、顔が含まれる画像を参照します。 Face サービスが応答するまで数秒お待ちください。 その後、画像内の顔に赤色の四角形が表示されます。 顔の四角形にマウスを移動すると、顔の説明がステータス バーに表示されます。

![顔を検出して四角形のフレームに収めた画像のスクリーンショット](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>まとめ

このチュートリアルでは、Face サービス クライアント ライブラリを使用するための基本的なプロセスを学習し、画像の中にある顔をフレームに収めて表示するアプリケーションを作成しました。

## <a name="next-steps"></a>次の手順

顔のランドマークの検出と使用について学習してください。

> [!div class="nextstepaction"]
> [画像内の顔を検出する方法](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
