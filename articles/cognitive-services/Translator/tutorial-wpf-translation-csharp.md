---
title: 'チュートリアル: C# を使用して Translator Text の WPF アプリケーションを作成する | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: このチュートリアルで は、C# を使って WPF アプリケーションをビルドすることで、Translator Text API を使用してテキストの翻訳、サポートされている言語のローカライズされた一覧を取得するなどの方法を説明します。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: nolachar
ms.openlocfilehash: 353c1d91b7925a84667ef1bb7c38ab87c6a89cc1
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39716374"
---
# <a name="tutorial-write-a-wpf-application-for-translator-text-using-c35"></a>チュートリアル: C&#35; を使用して Translator Text の WPF アプリケーションを作成する

このチュートリアルでは、Azure の Microsoft Cognitive Services に組み込まれている Translator Text API (V3) を使用して、対話形式のテキスト変換ツールを作成します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * サービスでサポートされている言語の一覧を取得する
> * ユーザー入力テキストのある言語から別の言語への翻訳を実行する

このアプリケーションは、他の 2 つの Microsoft Cognitive Services との統合も備えています。

|||
|-|-|
|[Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|翻訳されるテキストのソース言語を自動的に検出するために必要に応じて使用されます|
|[Bing Spell Check](https://azure.microsoft.com/services/cognitive-services/spell-check/)|英語のソース テキストの場合、翻訳がより正確になるようにスペルミスを修正するために使用されます

![[チュートリアル プログラムの実行]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>前提条件

このコードを Windows 上で実行するには、[Visual Studio 2017](https://www.visualstudio.com/downloads/) が必要です。 (無料の Community Edition でかまいません。)

また、プログラムで使用される 3 つの Azure サービスのサブスクリプション キーも必要です。 Translator Text サービスのキーは、Azure ダッシュボードから取得できます。 毎月最大 200 万文字を無料で翻訳できる、無料価格レベルを利用できます。

Text Analytics と Bing Spell Check の両方のサービスには、無料試用版が用意されており、[Cognitive Services の試行](https://azure.microsoft.com/try/cognitive-services/)でサインアップできます。 また、Azure ダッシュボードを介してどちらかのサブスクリプションも作成できます。 Text Analytics には無料レベルがあります。

このチュートリアルのソース コードは下に示されています。 お使いのサブスクリプション キーは、`MainWindow.xaml.cs` 内で `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY` などの変数としてソース コードにコピーする必要があります。

> [!IMPORTANT]
> Text Analytics サービスは、複数のリージョンで使用できます。 このチュートリアルのソース コード内の URI は、`westus` リージョンにあり、無料試用版に使用されるリージョンです。 別のリージョンにサブスクリプションがある場合は、それに応じてこの URI を更新してください。

## <a name="source-code"></a>ソース コード

これは、Microsoft Translator Text API のソース コードです。 アプリを実行するには、Visual Studio で新しい WPF プロジェクトの適切なファイルにソース コードをコピーします。

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

これは、アプリケーションの機能を提供する分離コード ファイルです。

```csharp
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string from
    /// one language to another. The languages are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected. English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog if there's an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
        private void PopulateLanguageMenus()
        {
            // Add option to automatically detect the source language
            FromLanguageComboBox.Items.Add("Detect");

            int count = languageCodesAndTitles.Count;
            foreach (string menuItem in languageCodesAndTitles.Keys)
            {
                FromLanguageComboBox.Items.Add(menuItem);
                ToLanguageComboBox.Items.Add(menuItem);
            }

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
            response = WebRequest.GetResponse();
            using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
            {
                var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
                var languages = result["translation"];

                languageCodes = languages.Keys.ToArray();
                foreach (var kv in languages)
                {
                    languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
                }
            }
        }

        // ***** PERFORM TRANSLATION ON BUTTON CLICK
        private async void TranslateButton_Click(object sender, EventArgs e)
        {
            string textToTranslate = TextToTranslate.Text.Trim();

            string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
            string fromLanguageCode;

            // auto-detect source language if requested
            if (fromLanguage == "Detect")
            {
                fromLanguageCode = DetectLanguage(textToTranslate);
                if (!languageCodes.Contains(fromLanguageCode))
                {
                    MessageBox.Show("The source language could not be detected automatically " +
                        "or is not supported for translation.", "Language detection failed",
                        MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }
            }
            else
                fromLanguageCode = languageCodesAndTitles[fromLanguage];

            string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

            // spell-check the source text if the source language is English
            if (fromLanguageCode == "en")
            {
                if (textToTranslate.StartsWith("-"))    // don't spell check in this case
                    textToTranslate = textToTranslate.Substring(1);
                else
                {
                    textToTranslate = CorrectSpelling(textToTranslate);
                    TextToTranslate.Text = textToTranslate;     // put corrected text into input field
                }
            }

            // handle null operations: no text or same source/target languages
            if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
            {
                TranslatedTextLabel.Content = textToTranslate;
                return;
            }

            // send HTTP request to perform the translation
            string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
            string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

            System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

このファイルは、アプリケーションのユーザー インターフェイス (WPF フォーム) を定義します。 独自バージョンのフォームをデザインする場合は、この XAML は必要ありません。

```xml
<Window x:Class="MSTranslatorTextDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MSTranslatorTextDemo"
        mc:Ignorable="d"
        Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
    <Grid>
        <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
        <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
        <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
        <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

        <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
        <ComboBox x:Name="ToLanguageComboBox"
                HorizontalAlignment="Left"
                Margin="306,88,0,0"
                VerticalAlignment="Top"
                Width="175" FontSize="14" TabIndex="2">

        </ComboBox>
        <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
        <ComboBox x:Name="FromLanguageComboBox"
            HorizontalAlignment="Left"
            Margin="42,88,0,0"
            VerticalAlignment="Top"
            Width="175" FontSize="14" TabIndex="1"/>
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>サービス エンドポイント

Microsoft Translator サービスには、さまざまな翻訳機能を提供する多数のエンドポイントがあります。 このチュートリアルで使用するものは次のとおりです。

|||
|-|-|
|`Languages`|Translator Text API の他の操作で現在サポートされている一連の言語を返します。|
|`Translate`|ソース テキスト、ソース言語コード、およびターゲット言語コードが与えられると、ターゲット言語へのソース テキストの翻訳を返します。|

## <a name="the-translation-app"></a>翻訳アプリケーション

翻訳アプリケーションのユーザー インターフェイスは、Windows Presentation Foundation (WPF) を使用してビルドされています。 次の手順に従って、Visual Studio で新しい WPF プロジェクトを作成します。

* **[ファイル]** メニューから、**[新規作成] > [プロジェクト]** をクリックします。
* [新しいプロジェクト] ウィンドウで、**[インストール済み] > [テンプレート] > [Visual C#]** を開きます。 使用可能なプロジェクト テンプレートの一覧は、ダイアログの中央に表示されます。
* プロジェクト テンプレート一覧にあるドロップダウン メニューで **.NET Framework 4.5.2** が選択されていることを確認してください。
* プロジェクト テンプレート一覧で **[WPF App (.NET Framework)]**(WPF アプリ (.NET Framework)) をクリックします。
* ダイアログの下部にあるフィールドを使用して、新しいプロジェクトとそれを含むソリューションに名前を付けます。
* **[OK]** をクリックして、新しいプロジェクトとソリューションを作成します。

![[Visual Studio での新しい WPF アプリケーションの作成]](media/translator-text-csharp-new-project.png)

次の .NET Framework アセンブリへの参照をプロジェクトに追加します。

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

プロジェクトに次の NuGet パッケージ`Newtonsoft.Json`をインストールします。

ここでソリューション エクスプローラーで `MainWindow.xaml` ファイルを探し、開きます。 当初、これは空白です。 次に、完成したユーザー インターフェイスがどのように表示されるかを、コントロールの名前を青色で記して示します。 コードにも表示されるので、ユーザー インターフェイスのコントロールに同じ名前を使用します。

![[Visual Studio デザイナーでのメイン ウィンドウの注釈付きのビュー]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> このチュートリアルのソース コードには、このフォームの XAML ソースが含まれます。 Visual Studio でフォームをビルドするのではなく、これをプロジェクトに貼り付けることができます。

* `FromLanguageComboBox` *(コンボボックス)* - Microsoft Translator でテキスト翻訳にサポートされている言語の一覧を表示します。 ユーザーは、翻訳前の言語を選択します。
* `ToLanguageComboBox` *(コンボボックス)* - `FromComboBox` と同じ言語の一覧を表示しますが、ユーザーの翻訳後の言語を選択するために使用されます。
* `TextToTranslate` *(テキストボックス)* - ユーザーはここに、翻訳されるテキストを入力します。
* `TranslateButton` *(ボタン)* -ユーザーは、このボタンをクリックして (または Enter キーを押して)、テキストを翻訳します。
* `TranslatedTextLabel` *(ラベル)* -ユーザーのテキストの翻訳がここに表示されます。

このフォームの独自バージョンを作成する場合は、弊社のものと "*まったく*" 同じものにする必要はありません。 ただし、言語名が切り取られないように言語のドロップダウンが十分広くなっていることを確認してください。

## <a name="the-mainwindow-class"></a>MainWindow クラス

分離コード ファイル `MainWindow.xaml.cs` は、プログラムが実行する動作をプログラムに実行させるコードが進行する場所です。 作業は次の 2 回行われます。

* プログラムが開始され、`MainWindow` がインスタンス化されるときに、Translator の API を使用して言語の一覧を取得し、ドロップダウン メニューにこれらを入力します。 このタスクは、各セッションの開始時に 1 回行われます。

* ユーザーが **[翻訳]** ボタンをクリックすると、そのユーザーが選択した言語と入力したテキストが取得され、`Translate` API が呼び出されて、翻訳が実行されます。 また、翻訳前に、テキストの言語を決定し、スペルを修正するその他の機能も呼び出せます。

クラスの先頭をご覧ください。

```csharp
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog if there's an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

ここで宣言された 2 つのメンバー変数は、言語に関する情報を保持します。

|||
|-|-|
|`languageCodes`<br>文字列の配列|言語コードをキャッシュします。 この Translator サービスは、英語には `en` というように短いコードを使用して言語を識別します。|
|`languageCodesAndTitles`<br>SortedDictionary|ユーザー インターフェイス内の「わかりやすい」名前を API で使用される短いコードにマッピングします。 大文字と小文字を区別せずアルファベット順の並べ替えを保持します。|

アプリケーションで実行される最初のコードは、`MainWindow` コンストラクターです。 最初に、メソッド `HandleExceptions` をグローバル エラー ハンドラーとして設定します。 このようにして、例外が処理されない場合は、少なくともエラー警告が表示されます。

次に、API サブスクリプション キーがすべてちょうど 32 文字の長さになっていることを確認します。 そうでない場合、その最も可能性の高い理由は "*誰か*" が API キーに貼り付けなかったためです。 この場合、エラー メッセージを表示して、処理を終了します (もちろんこのテストに合格しても、キーが有効であるわけではありません)。

少なくとも正しい長さのキーがある場合、`InitializeComponent()` を呼び出すと、メイン アプリケーション ウィンドウで XAML 記述の検索、読み込み、およびインスタンス化を行うことにより、ユーザー インターフェイスがロールします。

最後に、言語ドロップダウン メニューを設定します。 このタスクには、3 つの別個のメソッド呼び出しが必要です。これについて詳しくは、以降のセクションをご覧ください。

## <a name="get-supported-languages"></a>サポートされている言語を取得する

Microsoft Translator サービスは、この文書の作成時点で合計 61 の言語をサポートしており、適宜さらに追加されます。 したがって、サポートされる言語をプログラムにハードコードしないことをお勧めします。 代わりに、Translator サービスにサポートしている言語を問い合わせてください。 サポートされている言語はすべて、その他のサポートされているどの言語にも翻訳できます。

`Languages` API を呼び出して、サポートされている言語の一覧を取得します。

`Languages` API は、省略可能な GET クエリ パラメーター *scope* を取得します。 *scope* は、`translation`、`transliteration`、`dictionary` の 3 つの値のいずれかを取ることができます。 このコードは、値 `translation` を使用します。

`Languages` API はまた、省略可能な HTTP ヘッダー `Accept-Language` も取得します。 このヘッダーの値は、サポートされている言語の名前が返される言語を決定します。 値は、適格な BCP 47 言語タグである必要があります。 このコードは `en` の値を使用して、英語で言語名を表示します。

`Languages` API は、次のような JSON 応答を返します。

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
}
```

言語コード (たとえば、`af` など) と言語名 (`Afrikaans` など) を抽出できるようにするために、このコードは NewtonSoft.Json メソッド [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) を使用します。

この背景知識に基づいて、言語コードとその名前を取得する次のメソッドを作成します。

```csharp
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
    response = WebRequest.GetResponse();
    using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
    {
        var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
        var languages = result["translation"];

        languageCodes = languages.Keys.ToArray();
        foreach (var kv in languages)
        {
            languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
        }
    }
}
```

`GetLanguagesForTranslate()` は最初に HTTP 要求を作成します。 `scope=translation` クエリ文字列パラメーターは、テキスト翻訳でサポートされているこれらの言語のみを要求します。 Text Translation API サブスクリプション キーが要求ヘッダーに追加されます。 サポートされている言語は英語で返されるため、`en` の値を持つ `Accept-Language` ヘッダーが追加されます。

要求が完了した後、JSON 応答が解析され、ディクショナリに変換されてから、言語コードが `languageCodes` メンバー変数に追加されます。 言語コードとわかりやすい言語名を含んだキーと値のペアがループ処理され、`languageCodesAndTitles` メンバー変数に追加されます  (フォームのドロップダウン メニューには、わかりやすい名前が表示されますが、翻訳を要求するにはコードが必要です)。

## <a name="populate-the-language-menus"></a>言語メニューに入力する

ほとんどのユーザー インターフェイスは、XAML で定義されているので、呼び出し `InitializeComponent()` 以外は設定するために行う必要のあることはあまりありません。 行う必要のあるもう 1 つの作業は、**[翻訳元の言語]** および **[翻訳先の言語]** のドロップダウンにわかりやすい言語名を追加するというもので、これは `PopulateLanguageMenus()` で行われます。

```csharp
private void PopulateLanguageMenus()
{
    // Add option to automatically detect the source language
    FromLanguageComboBox.Items.Add("Detect");

    int count = languageCodesAndTitles.Count;
    foreach (string menuItem in languageCodesAndTitles.Keys)
    {
        FromLanguageComboBox.Items.Add(menuItem);
        ToLanguageComboBox.Items.Add(menuItem);
    }

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

メニューの入力は、`languageCodesAndTitles` ディクショナリを反復してそれぞれのキー (「わかりやすい」名前) を両方のメニューに追加していくという簡単なものです。 メニューを入力した後、既定の "to" (翻訳後) および "from" (翻訳前) の言語を、**[検出]** (言語を自動検出する) と **[英語]** に設定します。

> [!TIP]
> メニューの既定の選択肢を設定しない場合、ユーザーは、"to" や "from" の言語を最初に選択せずに **[翻訳]** をクリックできます。 既定値を使用すれば、この問題を扱う必要がなくなります。

`MainWindow` が初期化され、ユーザー インターフェイスが作成されたので、コードはユーザーが **[翻訳]** ボタンをクリックするまで待機します。

## <a name="perform-translation"></a>翻訳を実行する

ユーザーが **[Translate]**(翻訳) をクリックすると、WPF は次に示す `TranslateButton_Click()` イベント ハンドラーを呼び出します。

```csharp
private async void TranslateButton_Click(object sender, EventArgs e)
{
    string textToTranslate = TextToTranslate.Text.Trim();

    string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
    string fromLanguageCode;

    // auto-detect source language if requested
    if (fromLanguage == "Detect")
    {
        fromLanguageCode = DetectLanguage(textToTranslate);
        if (!languageCodes.Contains(fromLanguageCode))
        {
            MessageBox.Show("The source language could not be detected automatically " +
                "or is not supported for translation.", "Language detection failed",
                MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }
    }
    else
        fromLanguageCode = languageCodesAndTitles[fromLanguage];

    string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

    // spell-check the source text if the source language is English
    if (fromLanguageCode == "en")
    {
        if (textToTranslate.StartsWith("-"))    // don't spell check in this case
            textToTranslate = textToTranslate.Substring(1);
        else
        {
            textToTranslate = CorrectSpelling(textToTranslate);
            TextToTranslate.Text = textToTranslate;     // put corrected text into input field
        }
    }

    // handle null operations: no text or same source/target languages
    if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
    {
        TranslatedTextLabel.Content = textToTranslate;
        return;
    }

    // send HTTP request to perform the translation
    string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
    string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

    System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
    var requestBody = JsonConvert.SerializeObject(body);

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

最初に、"to" と "from" の言語を、ユーザーが入力したテキストとともにフォームから取得します。

ソース言語が **[検出]** に設定されている場合、`DetectLanguage()` を呼び出して、テキストの言語を判断します。 テキストが、Translator API でサポートされていない言語で記されている可能性や (翻訳可能な言語より多くの言語が検出されることがあります)、Text Analytics API が検出できない可能性があります。 その場合は、ユーザーに通知するメッセージが表示され、翻訳せずに戻ります。

ソース言語が英語の場合 (指定または検出)、`CorrectSpelling()` でテキストのスペルを確認し、修正を適用します。 修正したテキストが入力フィールドに戻され、ユーザーは修正が行われたことがわかります。 (ユーザーは翻訳されているテキストの前にハイフンを付けて、スペル修正を抑制できます)。

ユーザーがテキストを一切入力していない場合、または "to" と "from" の言語が同じ場合、翻訳は不要で、要求を回避できます。

翻訳要求を実行するコードはわかりやすいものにする必要があります。URI をビルドし、要求を作成し、それを送信し、応答を解析します。 テキストを表示するには、`TranslatedTextLabel` コントロールに送信します。

次に、POST 要求の本文で、シリアル化された JSON 配列でテキストを `Translate` API に渡します。 JSON 配列には、翻訳する複数のテキストを含められますが、ここでは 1 つで十分です。

`X-ClientTraceId` という名前の HTTP ヘッダーは省略可能です。 値は GUID にする必要があります。 クライアント提供のトレース ID は、予測どおりに進まないときに要求をトレースする場合に役立ちます。 ただし、役立たせるには、X ClientTraceID の値をクライアントが記録する必要があります。 クライアント トレース ID と要求の日付は、発生する可能性のある問題を Microsoft が診断する場合に役立ちます。

> [!NOTE]
> このチュートリアルは、Microsoft Translator サービスに焦点を当てているため、`DetectLanguage()` と `CorrectSpelling()` のメソッドは詳しく取り扱っていません。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Microsoft Translator Text API のリファレンス](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
