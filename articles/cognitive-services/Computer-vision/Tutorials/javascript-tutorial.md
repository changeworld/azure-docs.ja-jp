---
title: Computer Vision API JavaScript チュートリアル | Microsoft Docs
description: Microsoft Cognitive Services で Computer Vision API を使用する基本的な Javascript アプリについて確認します。 OCR を実行し、サムネイルを作成して、イメージ内の視覚的な特徴を操作します。
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 09/19/2017
ms.author: kefre
ms.openlocfilehash: 89bdc0524e07c1cb6a1473e0a52791fe20271e06
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373445"
---
# <a name="computer-vision-api-javascript-tutorial"></a>Computer Vision API JavaScript チュートリアル

このチュートリアルでは、Microsoft Cognitive Services の Computer Vision REST API の機能について説明します。

Computer Vision REST API を使用して、光学文字認識 (OCR) を実行し、スマート トリミングされたサムネイルを作成するほか、イメージ内の視覚的な特徴 (顔など) を検出、カテゴライズ、タグ付け、および記述する、JavaScript アプリケーションについて確認します。 この例では、分析および処理用にイメージ の URL を送信できます。 このオープン ソースの例を、JavaScript で Computer Vision REST API を使用する独自のアプリをビルドする、テンプレートとして使用できます。

JavaScript フォーム アプリケーションは既に記述されていますが、Computer Vision の機能がありません。 このチュートリアルでは、Computer Vision REST API 固有のコードを追加して、アプリの機能を完成させます。

## <a name="prerequisites"></a>前提条件

### <a name="platform-requirements"></a>プラットフォームの要件

このチュートリアルは、単純なテキスト エディターを使用して開発されています。

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Computer Vision API にサブスクライブしてサブスクリプション キーを取得する 

例を作成する前に、Microsoft Cognitive Services の一部である Computer Vision API にサブスクライブする必要があります。 サブスクリプションとキー管理について詳しくは、[サブスクリプション](https://azure.microsoft.com/try/cognitive-services/)に関するページをご覧ください。 このチュートリアルでは、主キーと 2 次キーの両方を使用できます。 

## <a name="download-the-tutorial-project"></a>チュートリアル プロジェクトをダウンロードする

[Cognitive Services JavaScript Computer Vision チュートリアル](https://github.com/Azure-Samples/cognitive-services-javascript-computer-vision-tutorial)を複製するか、または .zip ファイルをダウンロードしそれを空のディレクトリに抽出します。

すべてのチュートリアル コードが追加された完成したチュートリアルの使用を希望する場合は、**Completed** フォルダー内のファイルを使用できます。

## <a name="add-the-tutorial-code"></a>チュートリアル コードを追加する

JavaScript アプリケーションは、機能ごとに 1 つ、合計で 6 つの .html ファイルで設定されています。 各ファイルは Computer Vision の別個の関数を示します (分析、OCR など)。 チュートリアルの 6 つのセクションは互いに独立しているため、チュートリアル コードを 1 つまたは 6 つすべてのファイルに追加することも、いくつかのファイルにのみ追加することもできます。 チュートリアル コードは任意の順番でファイルに追加できます。

それでは始めましょう。

## <a name="analyze-an-image"></a>イメージを分析する

Computer Vision の分析機能は、2,000 を超える認識可能なオブジェクト、生物、シーン、およびアクションのイメージを分析します。 分析が完了すると、分析機能は、わかりやすいタグ、色の分析、キャプションなどでイメージを説明する、JSON オブジェクトを返します。

チュートリアル アプリケーションの分析機能を完成させるには、次の手順を実行します。

### <a name="analyze-step-1-add-the-event-handler-code-for-the-form-button"></a>分析の手順 1: フォーム ボタンのイベント ハンドラー コードを追加する

テキスト エディターで **analyze.html** ファイルを開き、ファイル下部にある **analyzeButtonClick** 関数を探します。

**analyzeButtonClick** イベント ハンドラー関数はフォームをクリアし、URL で指定されたイメージを表示して、その後 **AnalyzeImage** 関数を呼び出してイメージを分析します。

次のコードをコピーして **analyzeButtonClick** 関数に貼り付けます。

```javascript
function analyzeButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    AnalyzeImage(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="analyze-step-2-add-the-wrapper-for-the-rest-api-call"></a>分析の手順 2: REST API 呼び出しにラッパーを追加する

**AnalyzeImage** 関数は、REST API 呼び出しをラップしてイメージを分析します。 正常に返されると、指定のテキスト領域に書式設定された JSON の分析が表示され、指定の範囲にキャプションが表示されます。

**AnalyzeImage** 関数コードをコピーして **analyzeButtonClick** 関数の直後に貼り付けます。

```javascript
/* Analyze the image at the specified URL by using Microsoft Cognitive Services Analyze Image API.
 * @param {string} sourceImageUrl - The URL to the image to analyze.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function AnalyzeImage(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "visualFeatures": "Categories,Description,Color",
        "details": "",
        "language": "en",
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseAnalyze +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.description && data.description.captions) {
            var caption = data.description.captions[0];
            
            if (caption.text && caption.confidence) {
                captionSpan.text("Caption: " + caption.text +
                    " (confidence: " + caption.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="analyze-step-3-run-the-application"></a>分析の手順 3: アプリケーションを実行する

**analyze.html** ファイルを保存し、Web ブラウザーで開きます。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 分析するイメージへの URL を入力し、**[Analyze Image]\(イメージの分析\)** ボタンをクリックしてイメージを分析し、結果を確認します。

## <a name="recognize-a-landmark"></a>ランドマークを認識する

Computer Vision のランドマーク機能は、山や有名な建物などの自然および人口のランドマークのイメージを分析します。 分析が完了すると、ランドマーク機能はイメージで見つかったランドマークを識別する JSON オブジェクトを返します。

チュートリアル アプリケーションのランドマーク機能を完成させるには、次の手順を実行します。

### <a name="landmark-step-1-add-the-event-handler-code-for-the-form-button"></a>ランドマークの手順 1: フォーム ボタンのイベント ハンドラー コードを追加する

テキスト エディターで **landmark.html** ファイルを開き、ファイル下部にある **landmarkButtonClick** 関数を探します。

**landmarkButtonClick** イベント ハンドラー関数はフォームをクリアし、URL で指定されたイメージを表示して、その後 **IdentifyLandmarks** 関数を呼び出してイメージを分析します。

次のコードをコピーして **landmarkButtonClick** 関数に貼り付けます。

```javascript
function landmarkButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyLandmarks(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="landmark-step-2-add-the-wrapper-for-the-rest-api-call"></a>ランドマークの手順 2: REST API 呼び出しにラッパーを追加する

**IdentifyLandmarks** 関数は、REST API 呼び出しをラップしてイメージを分析します。 正常に返されると、指定のテキスト領域に書式設定された JSON の分析が表示され、指定の範囲にキャプションが表示されます。

**IdentifyLandmarks** 関数コードをコピーして **landmarkButtonClick** 関数の直後に貼り付けます。

```javascript
/* Identify landmarks in the image at the specified URL by using Microsoft Cognitive Services 
 * Landmarks API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for landmarks.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyLandmarks(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "landmarks"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseLandmark +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.landmarks) {
            var landmark = data.result.landmarks[0];
            
            if (landmark.name && landmark.confidence) {
                captionSpan.text("Landmark: " + landmark.name +
                    " (confidence: " + landmark.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="landmark-step-3-run-the-application"></a>ランドマークの手順 3: アプリケーションを実行する

**landmark.html** ファイルを保存し、Web ブラウザーで開きます。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 分析するイメージへの URL を入力し、**[Analyze Image]\(イメージの分析\)** ボタンをクリックしてイメージを分析し、結果を確認します。

## <a name="recognize-celebrities"></a>著名人を認識する

Computer Vision の著名人機能は、有名な人物のイメージを分析します。 分析が完了すると、著名人機能はイメージで見つかった著名人を識別する JSON オブジェクトを返します。

チュートリアル アプリケーションの著名人機能を完成させるには、次の手順を実行します。

### <a name="celebrities-step-1-add-the-event-handler-code-for-the-form-button"></a>著名人の手順 1: フォーム ボタンのイベント ハンドラー コードを追加する

テキスト エディターで **celebrities.html** ファイルを開き、ファイル下部にある **celebritiesButtonClick** 関数を探します。

**celebritiesButtonClick** イベント ハンドラー関数はフォームをクリアし、URL で指定されたイメージを表示して、その後 **IdentifyCelebrities** 関数を呼び出してイメージを分析します。

次のコードをコピーして **celebritiesButtonClick** 関数に貼り付けます。

```javascript
function celebritiesButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    IdentifyCelebrities(sourceImageUrl, $("#responseTextArea"), $("#captionSpan"));
}
```

### <a name="celebrities-step-2-add-the-wrapper-for-the-rest-api-call"></a>著名人の手順 2: REST API 呼び出しにラッパーを追加する

```javascript
/* Identify celebrities in the image at the specified URL by using Microsoft Cognitive Services 
 * Celebrities API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for celebrities.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 * @param {<span> element} captionSpan - The span to display the image caption.
 */
function IdentifyCelebrities(sourceImageUrl, responseTextArea, captionSpan) {
    // Request parameters.
    var params = {
        "model": "celebrities"
    };
    
    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseCelebrities +
             "?" + 
             $.param(params),
                    
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
        
        // Extract and display the caption and confidence from the first caption in the description object.
        if (data.result && data.result.celebrities) {
            var celebrity = data.result.celebrities[0];
            
            if (celebrity.name && celebrity.confidence) {
                captionSpan.text("Celebrity name: " + celebrity.name +
                    " (confidence: " + celebrity.confidence + ").");
            }
        }
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Prepare the error string.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        
        // Put the error JSON in the response textarea.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Show the error message.
        alert(errorString);
    });
}
```

### <a name="celebrities-step-3-run-the-application"></a>著名人の手順 3: アプリケーションを実行する

**celebrities.html** ファイルを保存し、Web ブラウザーで開きます。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 分析するイメージへの URL を入力し、**[Analyze Image]\(イメージの分析\)** ボタンをクリックしてイメージを分析し、結果を確認します。

## <a name="intelligently-generate-a-thumbnail"></a>サムネイルをインテリジェントに生成する

Computer Vision のサムネイル機能はイメージのサムネイルを生成します。 サムネイル機能は**スマート トリミング**機能を使用して、イメージ内の対象の領域を特定してサムネイルをその領域の中央に配置し、より美しいサムネイル イメージを生成します。

チュートリアル アプリケーションのサムネイル機能を完成させるには、次の手順を実行します。

### <a name="thumbnail-step-1-add-the-event-handler-code-for-the-form-button"></a>サムネイルの手順 1: フォーム ボタンのイベント ハンドラー コードを追加する

テキスト エディターで **thumbnail.html** ファイルを開き、ファイル下部にある **thumbnailButtonClick** 関数を探します。

**thumbnailButtonClick** イベント ハンドラー関数がフォームをクリアし、URL で指定されているイメージを表示して、その後 **getThumbnail** 関数を 2 回呼び出して、スマート トリミングされたサムネイルとスマート トリミングされていないサムネイルの 2 つのサムネイルを作成します。

次のコードをコピーして **thumbnailButtonClick** 関数に貼り付けます。

```javascript
function thumbnailButtonClick() {

    // Clear the display fields.
    document.getElementById("sourceImage").src = "#";
    document.getElementById("thumbnailImageSmartCrop").src = "#";
    document.getElementById("thumbnailImageNonSmartCrop").src = "#";
    document.getElementById("responseTextArea").value = "";
    document.getElementById("captionSpan").text = "";
    
    // Display the image.
    var sourceImageUrl = document.getElementById("inputImage").value;
    document.getElementById("sourceImage").src = sourceImageUrl;
    
    // Get a smart cropped thumbnail.
    getThumbnail (sourceImageUrl, true, document.getElementById("thumbnailImageSmartCrop"), 
        document.getElementById("responseTextArea"));
    
    // Get a non-smart-cropped thumbnail.
    getThumbnail (sourceImageUrl, false, document.getElementById("thumbnailImageNonSmartCrop"),
        document.getElementById("responseTextArea"));
}
```

### <a name="thumbnail-step-2-add-the-wrapper-for-the-rest-api-call"></a>サムネイルの手順 2: REST API 呼び出しにラッパーを追加する

**getThumbnail** 関数は、REST API 呼び出しをラップしてイメージを分析します。 正常に返されると、サムネイルが指定した img 要素に表示されます。

次の **getThumbnail** 関数をコピーして **thumbnailButtonClick** 関数の直後に貼り付けます。

```javascript
/* Get a thumbnail of the image at the specified URL by using Microsoft Cognitive Services
 * Thumbnail API.
 * @param {string} sourceImageUrl URL to image.
 * @param {boolean} smartCropping Set to true to use the smart cropping feature which crops to the
 *                                more interesting area of an image; false to crop for the center
 *                                of the image.
 * @param {<img> element} imageElement The img element in the DOM which will display the thumnail image.
 * @param {<textarea> element} responseTextArea - The text area to display the Response Headers returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function getThumbnail (sourceImageUrl, smartCropping, imageElement, responseTextArea) {
    // Create the HTTP Request object.
    var xhr = new XMLHttpRequest();
    
    // Request parameters.
    var params = "width=100&height=150&smartCropping=" + smartCropping.toString();

    // Build the full URI.
    var fullUri = common.uriBasePreRegion + 
                  document.getElementById("subscriptionRegionSelect").value + 
                  common.uriBasePostRegion + 
                  common.uriBaseThumbnail +
                  "?" + 
                  params;
    
    // Identify the request as a POST, with the URI and parameters.
    xhr.open("POST", fullUri);
    
    // Add the request headers.
    xhr.setRequestHeader("Content-Type","application/json");
    xhr.setRequestHeader("Ocp-Apim-Subscription-Key", 
        encodeURIComponent(document.getElementById("subscriptionKeyInput").value));
    
    // Set the response type to "blob" for the thumbnail image data.
    xhr.responseType = "blob";
    
    // Process the result of the REST API call.
    xhr.onreadystatechange = function(e) {
        if(xhr.readyState === XMLHttpRequest.DONE) {
            
            // Thumbnail successfully created.
            if (xhr.status === 200) {
                // Show response headers.
                var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                responseTextArea.value = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                
                // Show thumbnail image.
                var urlCreator = window.URL || window.webkitURL;
                var imageUrl = urlCreator.createObjectURL(this.response);
                imageElement.src = imageUrl;
            } else {
                // Display the error message. The error message is the response body as a JSON string. 
                // The code in this code block extracts the JSON string from the blob response.
                var reader = new FileReader();
                
                // This event fires after the blob has been read.
                reader.addEventListener('loadend', (e) => {
                    responseTextArea.value = JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                });
                
                // Start reading the blob as text.
                reader.readAsText(xhr.response);
            }
        }
    }
    
    // Execute the REST API call.
    xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
}
```

### <a name="thumbnail-step-3-run-the-application"></a>サムネイルの手順 3: アプリケーションを実行する

**thumbnail.html** ファイルを保存し、Web ブラウザーで開きます。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 分析するイメージへの URL を入力し、**[Generate Thumbnails]\(サムネイルの生成\)** ボタンをクリックしてイメージを分析し、結果を確認します。

## <a name="read-printed-text-ocr"></a>印字されたテキストを読み取る (OCR)

Computer Vision の光学式文字認識 (OCR) 機能は、印字されたテキストのイメージを分析します。 分析が完了すると、OCR 機能はテキストとイメージ内のそのテキストの位置が含まれる JSON オブジェクトを返します。

チュートリアル アプリケーションの OCR 機能を完成させるには、次の手順を実行します。

### <a name="ocr-step-1-add-the-event-handler-code-for-the-form-button"></a>OCR の手順 1: フォーム ボタンのイベント ハンドラー コードを追加する

テキスト エディターで **ocr.html** ファイルを開き、ファイル下部にある **ocrButtonClick** 関数を探します。

**ocrButtonClick** イベント ハンドラー関数はフォームをクリアし、URL で指定されたイメージを表示して、その後 **ReadOcrImage** 関数を呼び出してイメージを分析します。

次のコードをコピーして **ocrButtonClick** 関数に貼り付けます。

```javascript
function ocrButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    $("#captionSpan").text("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadOcrImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="ocr-step-2-add-the-wrapper-for-the-rest-api-call"></a>OCR の手順 2: REST API 呼び出しにラッパーを追加する

**ReadOcrImage** 関数は、REST API 呼び出しをラップしてイメージを分析します。 正常に返されると、指定のテキスト領域にそのテキストとその位置を示す、書式設定された JSON が表示されます。

次の **ReadOcrImage** 関数をコピーして **ocrButtonClick** 関数の直後に貼り付けます。

```javascript
/* Recognize and read printed text in an image at the specified URL by using Microsoft Cognitive 
 * Services OCR API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for printed text.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadOcrImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "language": "unk",
        "detectOrientation ": "true",
    };

    // Perform the REST API call.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseOcr +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data) {
        // Show formatted JSON on webpage.
        responseTextArea.val(JSON.stringify(data, null, 2));
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="ocr-step-3-run-the-application"></a>OCR の手順 3: アプリケーションを実行する

**ocr.html** ファイルを保存し、Web ブラウザーで開きます。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 読み取るテキストのイメージへの URL を入力し、**[Read Image]\(イメージの読み取り\)** ボタンをクリックしてイメージを分析し、結果を確認します。

## <a name="read-handwritten-text-handwriting-recognition"></a>手書きのテキストを読み取る (手書き認識)

Computer Vision の手書き認識機能は、手書きのテキストのイメージを分析します。 分析が完了すると、手書き認識機能はテキストとイメージ内のそのテキストの位置が含まれる JSON オブジェクトを返します。

チュートリアル アプリケーションの手書き認識機能を完成させるには、次の手順を実行します。

### <a name="handwriting-recognition-step-1-add-the-event-handler-code-for-the-form-button"></a>手書き認識の手順 1: フォーム ボタンのイベント ハンドラー コードを追加する

テキスト エディターで **handwriting.html** ファイルを開き、ファイル下部にある **handwritingButtonClick** 関数を探します。

**handwritingButtonClick** イベント ハンドラー関数はフォームをクリアし、URL で指定されたイメージを表示して、その後 **HandwritingImage** 関数を呼び出してイメージを分析します。

次のコードをコピーして **handwritingButtonClick** 関数に貼り付けます。

```javascript
function handwritingButtonClick() {

    // Clear the display fields.
    $("#sourceImage").attr("src", "#");
    $("#responseTextArea").val("");
    
    // Display the image.
    var sourceImageUrl = $("#inputImage").val();
    $("#sourceImage").attr("src", sourceImageUrl);
    
    ReadHandwrittenImage(sourceImageUrl, $("#responseTextArea"));
}
```

### <a name="handwriting-recognition-step-2-add-the-wrapper-for-the-rest-api-call"></a>手書き認識の手順 2: REST API 呼び出しにラッパーを追加する

**ReadHandwrittenImage** 関数は、イメージの分析に必要な 2 つの REST API 呼び出しをラップします。 手書き認識は時間のかかるプロセスであるため、2 段階のプロセスが使用されます。 最初の呼び出しはプロセスを行う画像を送信し、2 番目の呼び出しは、プロセスが完了したときに検出されたテキストを取得します。

テキストを取得すると、指定のテキスト領域にそのテキストとその位置を示す、書式設定された JSON が表示されます。

次の **ReadHandwrittenImage** 関数をコピーして **handwritingButtonClick** 関数の直後に貼り付けます。

```javascript
/* Recognize and read text from an image of handwriting at the specified URL by using Microsoft 
 * Cognitive Services Recognize Handwritten Text API.
 * @param {string} sourceImageUrl - The URL to the image to analyze for handwriting.
 * @param {<textarea> element} responseTextArea - The text area to display the JSON string returned
 *                             from the REST API call, or to display the error message if there was 
 *                             an error.
 */
function ReadHandwrittenImage(sourceImageUrl, responseTextArea) {
    // Request parameters.
    var params = {
        "handwriting": "true",
    };

    // This operation requrires two REST API calls. One to submit the image for processing,
    // the other to retrieve the text found in the image. 
    //
    // Perform the first REST API call to submit the image for processing.
    $.ajax({
        url: common.uriBasePreRegion + 
             $("#subscriptionRegionSelect").val() + 
             common.uriBasePostRegion + 
             common.uriBaseHandwriting +
             "?" + 
             $.param(params),
        
        // Request headers.
        beforeSend: function(jqXHR){
            jqXHR.setRequestHeader("Content-Type","application/json");
            jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", 
                encodeURIComponent($("#subscriptionKeyInput").val()));
        },
        
        type: "POST",
        
        // Request body.
        data: '{"url": ' + '"' + sourceImageUrl + '"}',
    })
    
    .done(function(data, textStatus, jqXHR) {
        // Show progress.
        responseTextArea.val("Handwritten image submitted.");
        
        // Note: The response may not be immediately available. Handwriting Recognition is an
        // async operation that can take a variable amount of time depending on the length
        // of the text you want to recognize. You may need to wait or retry this GET operation.
        //
        // Try once per second for up to ten seconds to receive the result.
        var tries = 10;
        var waitTime = 100;
        var taskCompleted = false;
        
        var timeoutID = setInterval(function () { 
            // Limit the number of calls.
            if (--tries <= 0) {
                window.clearTimeout(timeoutID);
                responseTextArea.val("The response was not available in the time allowed.");
                return;
            }

            // The "Operation-Location" in the response contains the URI to retrieve the recognized text.
            var operationLocation = jqXHR.getResponseHeader("Operation-Location");
            
            // Perform the second REST API call and get the response.
            $.ajax({
                url: operationLocation,
                
                // Request headers.
                beforeSend: function(jqXHR){
                    jqXHR.setRequestHeader("Content-Type","application/json");
                    jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key",
                        encodeURIComponent($("#subscriptionKeyInput").val()));
                },
                
                type: "GET",
            })
            
            .done(function(data) {
                // If the result is not yet available, return.
                if (data.status && (data.status === "NotStarted" || data.status === "Running")) {
                    return;
                }
                
                // Show formatted JSON on webpage.
                responseTextArea.val(JSON.stringify(data, null, 2));
                
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
            })
            
            .fail(function(jqXHR, textStatus, errorThrown) {
                // Indicate the task is complete and clear the timer.
                taskCompleted = true;
                window.clearTimeout(timeoutID);
                
                // Display error message.
                var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
                errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
                    jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
                alert(errorString);
            });
        }, waitTime);
    })
    
    .fail(function(jqXHR, textStatus, errorThrown) {
        // Put the JSON description into the text area.
        responseTextArea.val(JSON.stringify(jqXHR, null, 2));
        
        // Display error message.
        var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
        errorString += (jqXHR.responseText === "") ? "" : (jQuery.parseJSON(jqXHR.responseText).message) ? 
            jQuery.parseJSON(jqXHR.responseText).message : jQuery.parseJSON(jqXHR.responseText).error.message;
        alert(errorString);
    });
}
```

### <a name="handwriting-recognition-step-3-run-the-application"></a>手書き認識の手順 3: アプリケーションを実行する

**handwriting.html** ファイルを保存し、Web ブラウザーで開きます。 **[Subscription Key]\(サブスクリプション キー\)** フィールドにサブスクリプション キーを入力し、**[Subscription Region]\(サブスクリプション リージョン\)** で正しいリージョンが使用されていることを確認します。 読み取るテキストのイメージへの URL を入力し、**[Read Image]\(イメージの読み取り\)** ボタンをクリックしてイメージを分析し、結果を確認します。

## <a name="next-steps"></a>次の手順

- [Computer Vision API C&#35; チュートリアル](CSharpTutorial.md)
- [Computer Vision API Python チュートリアル](PythonTutorial.md)
