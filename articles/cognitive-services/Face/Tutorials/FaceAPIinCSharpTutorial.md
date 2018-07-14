---
title: Face API C# チュートリアル | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 顔をフレーミングして画像内の顔を検出するために、Cognitive Services Emotion API を使用して単純な Windows アプリを作成します。
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ac4f247eb38cafc5582c1ffbb3752e049f0305ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374237"
---
# <a name="getting-started-with-face-api-in-c35-tutorial"></a>C&#35 の Face API の使用開始チュートリアル

このチュートリアルでは、Face API を使用する WPF Windows アプリケーションを作成します。 アプリケーションは、画像内の顔を検出し、各顔のまわりにフレームを描画し、ステータス バーに顔の説明を表示します。

![GettingStartCSharpScreenshot](../Images/getting-started-cs-detected.png)

## <a name="Preparation"></a>準備

このチュートリアルを使用するには、次の前提条件があります。

- Visual Studio 2015 以降がインストールされていることを確認してください。

## <a name="step1"></a>手順 1: Face API をサブスクライブし、サブスクリプション キーを入手する

Face API を使用するには、Microsoft Cognitive Services ポータルにサインアップして、Face API のサブスクリプションを新規登録する必要があります。 [サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページを参照してください。 このチュートリアルでは、プライマリまたはセカンダリ サブスクリプション キーを使用できます。

## <a name="step2"></a>手順 2: Visual Studio ソリューションを作成する

この手順では、画像を選択して表示する基本的なアプリケーションを作成する Windows WPF アプリケーション プロジェクトを構築します。 以下の手順に従います。

1. Visual Studio を開きます。
1. **[ファイル]** メニューから **[新規作成]**、**[プロジェクト]** の順にクリックします。
1. **[新しいプロジェクト]** ダイアログ ボックスで、アプリケーションの WPF を選択します。

   Visual Studio 2015 では、**[インストール済み]** &gt; **[テンプレート]** &gt; **[Visual C#]** &gt; **[Windows]** &gt; **[従来のデスクトップ]**&gt; の順に展開し、**[WPF アプリケーション]** を選択します。

   Visual Studio 2017 では、**[インストール済み]** &gt; **[テンプレート]** &gt; **[Visual C#]** &gt; **[Windows クラシック デスクトップ]** &gt; の順に展開し、**[WPF アプリ (.NET Framework)]** を選択します。
1. アプリケーションの名前を **FaceTutorial** に設定し、**[OK]** をクリックします。

   ![[WPF アプリケーション] が選択された [新しいプロジェクト] ダイアログ ボックス](../Images/vs2017-new-project.png)

1. **[ソリューション エクスプローラー]** で、プロジェクト (この場合は **FaceTutorial**) を右クリックし、**[NuGet パッケージの管理]** をクリックします。
1. **[NuGet パッケージ マネージャー]** ウィンドウで、パッケージのソースとして **[nuget.org]** を選択します。
1. **Newtonsoft.Json** を検索し、**[インストール]** を選択します。 (Visual Studio 2017 では、まず **[参照]** タブをクリックし、次に **[検索]** を選択します)。

   ![GettingStartCSharpPackageManager](../Images/install-nsoft-json.png)

## <a name="step3"></a>手順 3: Face API クライアント ライブラリを構成する

Face API は、HTTPS REST 要求を通して呼び出すことのできるクラウド API です。 .NET アプリケーションで使用しやすくするために、.NET クライアント ライブラリには、Face API REST 要求がカプセル化されています。 この例では、作業を簡単にするために、そのクライアント ライブラリを利用します。

次の指示に従って、クライアント ライブラリを構成します。

1. **[ソリューション エクスプローラー]** で、プロジェクト (この場合は **FaceTutorial**) を右クリックし、**[NuGet パッケージの管理]** をクリックします。
1. **[NuGet パッケージ マネージャー]** ウィンドウで、パッケージのソースとして **[nuget.org]** を選択します。
1. **Microsoft.ProjectOxford.Face** を検索し **[インストール]** します。 (Visual Studio 2017 では、まず **[参照]** タブをクリックし、次に **[検索]** を選択します)。

   ![GettingStartCSharpPackageManagerSDK](../Images/install-project-oxford-face.png)

1. **[ソリューション エクスプローラー]** でプロジェクト参照をチェックします。 **Microsoft.ProjectOxford.Common**、**Microsoft.ProjectOxford.Face**、および **Newtonsoft.Json** 参照がインストール完了時に自動的に追加されます。

   ![GetStartedCSharp-CheckInstallation.png](../Images/GetStartedCSharp-CheckInstallation.png)

## <a name="step3"></a>手順 4: 最初のコードをコピーして、貼り付ける

1. MainWindow.xaml を開き、既存のコードを次のコードに置き換えて、ウィンドウ UI を作成します。

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

1. MainWindow.xaml を開き、既存のコードを次のコードに置き換えます。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows;
    using System.Windows.Input;
    using System.Windows.Media;
    using System.Windows.Media.Imaging;
    using Microsoft.ProjectOxford.Common.Contract;
    using Microsoft.ProjectOxford.Face;
    using Microsoft.ProjectOxford.Face.Contract;

    namespace FaceTutorial
    {
        public partial class MainWindow : Window
        {
            // Replace the first parameter with your valid subscription key.
            //
            // Replace or verify the region in the second parameter.
            //
            // You must use the same region in your REST API call as you used to obtain your subscription keys.
            // For example, if you obtained your subscription keys from the westus region, replace
            // "westcentralus" in the URI below with "westus".
            //
            // NOTE: Free trial subscription keys are generated in the westcentralus region, so if you are using
            // a free trial subscription key, you should not need to change this region.
            private readonly IFaceServiceClient faceServiceClient =
                new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");

            Face[] faces;                   // The list of detected faces.
            String[] faceDescriptions;      // The list of descriptions for the detected faces.
            double resizeFactor;            // The resize factor for the displayed image.
            
            public MainWindow()
            {
                InitializeComponent();
            }
            
            // Displays the image and calls Detect Faces.

            private void BrowseButton_Click(object sender, RoutedEventArgs e)
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

1. サブスクリプション キーを挿入し、リージョンを確認します。

    MainWindow.xaml.cs ファイルで、次の行を特定します (28 および 29 行目)。

    ```csharp
    private readonly IFaceServiceClient faceServiceClient =
            new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");
    ```

    最初のパラメーターの `<Subscription Key>` を、手順 1. からの Face API サブスクリプション キーに置き換えます。

    また、サブスクリプション キーから取得した場所を 2 番目のパラメーターで使用していることを確認します。 たとえば、westus リージョンからサブスクリプション キーを取得した場合、URI の "**westcentralus**" を "**westus**" に置き換えます。

    無料評価版を使用してサブスクリプション キーを取得した場合、キーのリージョンは **westcentralus** に設定されているため、変更は不要です。

これで、アプリは、写真を参照してウィンドウに表示できます。

![GettingStartCSharpUI](../Images/getting-started-cs-ui.png)

## <a name="step4"></a>手順 5: 顔を検出して画像をアップロードする

顔を検出する最も簡単な方法は、画像ファイルを直接アップロードして [Face – Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API を呼び出すことです。
クライアント ライブラリを使用している場合、FaceServiceClient クラスの非同期メソッド DetectAsync を使用することでこれを行うことができます。
返された顔にはそれぞれ、その場所を示す四角形と一連の任意の顔属性が含まれます。

**MainWindow** クラスに次のコードを挿入します。

```csharp
// Uploads the image file and calls Detect Faces.

private async Task<Face[]> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IEnumerable<FaceAttributeType> faceAttributes =
        new FaceAttributeType[] { FaceAttributeType.Gender, FaceAttributeType.Age, FaceAttributeType.Smile, FaceAttributeType.Emotion, FaceAttributeType.Glasses, FaceAttributeType.Hair };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            Face[] faces = await faceServiceClient.DetectAsync(imageFileStream, returnFaceId: true, returnFaceLandmarks:false, returnFaceAttributes: faceAttributes);
            return faces;
        }
    }
    // Catch and display Face API errors.
    catch (FaceAPIException f)
    {
        MessageBox.Show(f.ErrorMessage, f.ErrorCode);
        return new Face[0];
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new Face[0];
    }
}
```

## <a name="step5"></a>手順 6: 画像の中の顔をマークする

この手順では、前の手順をすべて組み合わせて、画像内で検出された顔をマークします。

**MainWindow.xaml.cs** で、'async' 修飾子を **BrowseButton_Click** メソッドに追加します。

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

**BrowseButton_Click** イベント ハンドラーの末尾に次のコードを挿入します。

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faces = await UploadAndDetectFaces(filePath);
Title = String.Format("Detection Finished. {0} face(s) detected", faces.Length);

if (faces.Length > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = 96 / dpi;
    faceDescriptions = new String[faces.Length];

    for (int i = 0; i < faces.Length; ++i)
    {
        Face face = faces[i];

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
    faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="step6"></a>手順 7: 画像内の顔を説明する

この手順では、顔のプロパティを確認し、顔を説明する文字列を生成します。 この文字列は、マウス ポインターを顔の四角形の上に置いたときに表示されます。

このメソッドを **MainWindow** クラスに追加し、顔の詳細を文字列に変換します。

```csharp
// Returns a string that describes the given face.

private string FaceDescription(Face face)
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
    EmotionScores emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger     >= 0.1f) sb.Append(String.Format("anger {0:F1}%, ",     emotionScores.Anger * 100));
    if (emotionScores.Contempt  >= 0.1f) sb.Append(String.Format("contempt {0:F1}%, ",  emotionScores.Contempt * 100));
    if (emotionScores.Disgust   >= 0.1f) sb.Append(String.Format("disgust {0:F1}%, ",   emotionScores.Disgust * 100));
    if (emotionScores.Fear      >= 0.1f) sb.Append(String.Format("fear {0:F1}%, ",      emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f) sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral   >= 0.1f) sb.Append(String.Format("neutral {0:F1}%, ",   emotionScores.Neutral * 100));
    if (emotionScores.Sadness   >= 0.1f) sb.Append(String.Format("sadness {0:F1}%, ",   emotionScores.Sadness * 100));
    if (emotionScores.Surprise  >= 0.1f) sb.Append(String.Format("surprise {0:F1}%, ",  emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    HairColor[] hairColors = face.FaceAttributes.Hair.HairColor;
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

## <a name="step6"></a>手順 8: 顔の説明を表示する

**FacePhoto_MouseMove** メソッドのコードを次のコードに置き換えます。

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return from this method.
    if (faces == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faces.Length; ++i)
    {
        FaceRectangle fr = faces[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description for this face if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width && mouseXY.Y >= top && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // If the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
}
```

このアプリケーションを実行し、顔が含まれる画像を参照します。 クラウド API が応答するまで数秒お待ちください。 その後、画像内の顔に赤色の四角形が表示されます。 顔の四角形にマウスを移動すると、顔の説明がステータス バーに表示されます。

![GettingStartCSharpScreenshot](../Images/getting-started-cs-detected.png)

## <a name="summary"></a> まとめ

このチュートリアルでは、Face API を使用するための基本的なプロセスを学習し、画像内に顔マークを表示するアプリケーションを作成しました。 Face API の詳細については、ハウツー記事と [API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)をご覧ください。

## <a name="fullsource"></a> 完全なソース

ここには、WPF Windows アプリケーションの完全なソースが提供されています。

MainWindow.xaml:

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

MainWindow.xaml.cs:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using Microsoft.ProjectOxford.Common.Contract;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace the first parameter with your valid subscription key.
        //
        // Replace or verify the region in the second parameter.
        //
        // You must use the same region in your REST API call as you used to obtain your subscription keys.
        // For example, if you obtained your subscription keys from the westus region, replace
        // "westcentralus" in the URI below with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus region, so if you are using
        // a free trial subscription key, you should not need to change this region.
        private readonly IFaceServiceClient faceServiceClient =
            new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");

        Face[] faces;                   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();
        }

        // Displays the image and calls Detect Faces.

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

            // Detect any faces in the image.
            Title = "Detecting...";
            faces = await UploadAndDetectFaces(filePath);
            Title = String.Format("Detection Finished. {0} face(s) detected", faces.Length);

            if (faces.Length > 0)
            {
                // Prepare to draw rectangles around the faces.
                DrawingVisual visual = new DrawingVisual();
                DrawingContext drawingContext = visual.RenderOpen();
                drawingContext.DrawImage(bitmapSource,
                    new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
                double dpi = bitmapSource.DpiX;
                resizeFactor = 96 / dpi;
                faceDescriptions = new String[faces.Length];

                for (int i = 0; i < faces.Length; ++i)
                {
                    Face face = faces[i];

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
                faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
            }
        }

        // Displays the face description when the mouse is over a face rectangle.

        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
            // If the REST call has not completed, return from this method.
            if (faces == null)
                return;

            // Find the mouse position relative to the image.
            Point mouseXY = e.GetPosition(FacePhoto);

            ImageSource imageSource = FacePhoto.Source;
            BitmapSource bitmapSource = (BitmapSource)imageSource;

            // Scale adjustment between the actual size and displayed size.
            var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

            // Check if this mouse position is over a face rectangle.
            bool mouseOverFace = false;

            for (int i = 0; i < faces.Length; ++i)
            {
                FaceRectangle fr = faces[i].FaceRectangle;
                double left = fr.Left * scale;
                double top = fr.Top * scale;
                double width = fr.Width * scale;
                double height = fr.Height * scale;

                // Display the face description for this face if the mouse is over this face rectangle.
                if (mouseXY.X >= left && mouseXY.X <= left + width && mouseXY.Y >= top && mouseXY.Y <= top + height)
                {
                    faceDescriptionStatusBar.Text = faceDescriptions[i];
                    mouseOverFace = true;
                    break;
                }
            }

            // If the mouse is not over a face rectangle.
            if (!mouseOverFace)
                faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
        }

        // Uploads the image file and calls Detect Faces.

        private async Task<Face[]> UploadAndDetectFaces(string imageFilePath)
        {
            // The list of Face attributes to return.
            IEnumerable<FaceAttributeType> faceAttributes =
                new FaceAttributeType[] { FaceAttributeType.Gender, FaceAttributeType.Age, FaceAttributeType.Smile, FaceAttributeType.Emotion, FaceAttributeType.Glasses, FaceAttributeType.Hair };

            // Call the Face API.
            try
            {
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    Face[] faces = await faceServiceClient.DetectAsync(imageFileStream, returnFaceId: true, returnFaceLandmarks: false, returnFaceAttributes: faceAttributes);
                    return faces;
                }
            }
            // Catch and display Face API errors.
            catch (FaceAPIException f)
            {
                MessageBox.Show(f.ErrorMessage, f.ErrorCode);
                return new Face[0];
            }
            // Catch and display all other errors.
            catch (Exception e)
            {
                MessageBox.Show(e.Message, "Error");
                return new Face[0];
            }
        }

        // Returns a string that describes the given face.

        private string FaceDescription(Face face)
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
            EmotionScores emotionScores = face.FaceAttributes.Emotion;
            if (emotionScores.Anger >= 0.1f) sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
            if (emotionScores.Contempt >= 0.1f) sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
            if (emotionScores.Disgust >= 0.1f) sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
            if (emotionScores.Fear >= 0.1f) sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
            if (emotionScores.Happiness >= 0.1f) sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
            if (emotionScores.Neutral >= 0.1f) sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
            if (emotionScores.Sadness >= 0.1f) sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
            if (emotionScores.Surprise >= 0.1f) sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

            // Add glasses.
            sb.Append(face.FaceAttributes.Glasses);
            sb.Append(", ");

            // Add hair.
            sb.Append("Hair: ");

            // Display baldness confidence if over 1%.
            if (face.FaceAttributes.Hair.Bald >= 0.01f)
                sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

            // Display all hair color attributes over 10%.
            HairColor[] hairColors = face.FaceAttributes.Hair.HairColor;
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
    }
}
```

## <a name="related"></a> 次のステップ

- [Java for Android での Face API の使用開始](FaceAPIinJavaForAndroidTutorial.md)
- [Python での Face API の使用開始](FaceAPIinPythonTutorial.md)
