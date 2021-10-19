---
title: 互換性のために Unicode でエンコードされていないテキストを変換する
description: Azure Logic Apps で Unicode 以外の文字を処理するには、base64 エンコードおよび Azure Functions を使用してテキスト ペイロードを UTF-8 に変換します。
ms.date: 10/05/2021
ms.topic: how-to
ms.reviewer: estfan, azla
ms.service: logic-apps
ms.openlocfilehash: 1251a1622e62b7940c25ac810db10f70da560000
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618992"
---
# <a name="support-non-unicode-character-encoding-in-logic-apps"></a>Logic Apps での Unicode 以外の文字エンコードのサポート

テキスト ペイロードを扱う際、Azure Logic Apps では、テキストが UTF-8 などの Unicode 形式でエンコードされていると推測されます。 ワークフローでエンコードが異なる文字を受信、送信、または処理する際には、問題が発生する場合があります。 たとえば、Unicode をサポートしていないレガシ システムを使用する場合、フラット ファイルに含まれる文字が破損する可能性があります。

他の文字エンコードを使用するテキストを扱う場合は、Unicode 以外のペイロードに base64 エンコードを適用します。 この手順により、Logic Apps でテキストが UTF-8 形式であると想定されなくなります。 その後、Azure Functions を使用して、.NET でサポートされているエンコードを UTF-8 に変換できます。 

このソリューションは、"*マルチテナント*" および "*シングルテナント*" ワークフローの両方で機能します。 この[ソリューションは、AS2 コネクタで使用する](#convert-payloads-for-as2)こともできます。

## <a name="convert-payload-encoding"></a>ペイロード エンコードの変換

まず、指定したトリガーでコンテンツ タイプを正しく識別できることを確認します。 この手順により、Logic Apps では、テキストが UTF-8 であると想定されなくなります。 

プロパティ **[内容の種類を推測]** があるトリガーとアクションで、 **[いいえ]** を選択します。  通常、このプロパティは、操作の **[パラメーターの追加]** の一覧で確認できます。 ただし、操作にこのプロパティが含まれていない場合、コンテンツ タイプは受信メッセージによって設定されます。

次の一覧に、コンテンツ タイプを自動的に推測することを無効にできるコネクタをいくつか示します。
* [OneDrive](/connectors/onedrive/)
* [Azure Blob Storage](/connectors/azureblob/)
* [Azure File Storage](/connectors/azurefile/)
* [ファイル システム](/connectors/filesystem/)
* [Google ドライブ](/connectors/googledrive/)
* [SFTP - SSH](/connectors/sftpwithssh/)
 
`text/plain` コンテンツに要求トリガーを使用している場合は、呼び出しの `Content-Type` ヘッダーにある `charset` パラメーターを設定する必要があります。 そうでない場合、文字が破損する可能性があるか、またはパラメーターがペイロードのエンコード形式と一致しません。 詳細については、[ `text/plain` コンテンツ タイプを処理する方法](logic-apps-content-type.md#text-plain)に関するセクションを参照してください。

たとえば、`Content-Type` ヘッダーに正しい `charset` パラメーターが設定されている場合、HTTP トリガーによって受信コンテンツが UTF-8 に変換されます。

```json
{
    "headers": {
        <...>
        "Content-Type": "text/plain; charset=windows-1250"
        },
        "body": "non UTF-8 text content"
}
```

`Content-Type` ヘッダーを `application/octet-stream` に設定した場合、utf-8 でない文字を受け取ることもあります。 詳細については、[ `application/octet-stream` コンテンツ タイプを処理する方法](logic-apps-content-type.md#applicationxml-and-applicationoctet-stream)に関するセクションを参照してください。

## <a name="base64-encode-content"></a>コンテンツの base64 エンコード

コンテンツを [base64 でエンコード](workflow-definition-language-functions-reference.md#base64)する前に、[テキストを UTF-8 に変換](#convert-payload-encoding)するようにしてください。 テキストを UTF-8 に変換する前にコンテンツを base64 で文字列にデコードした場合、破損した文字が返される場合があります。

次に、.NET でサポートされているエンコードを別の .NET でサポートされているエンコードに変換します。 [Azure Functions のコード例](#azure-functions-version)や、[.NET のコード例](#net-version)を確認します。

> [!TIP]
> "*シングルテナント*" ロジック アプリの場合、変換関数をローカルで実行することで、パフォーマンスを向上させ、待機時間を短縮することができます。

### <a name="azure-functions-version"></a>Azure Functions バージョン

次の例は Azure Functions バージョン 2 用です。 

```csharp
using System;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

public static class ConversionFunctionv2 {
  [FunctionName("ConversionFunctionv2")]
  public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, TraceWriter log) {
    log.Info("C# HTTP trigger function processing a request.");

    Encoding inputEncoding = null;

    string requestBody = new StreamReader(req.Body).ReadToEnd();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null) {
      return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
    }

    Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

    try {
      string encodingInput = data.encodingInput.Value;
      inputEncoding = Encoding.GetEncoding(name: encodingInput);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    Encoding encodingOutput = null;
    try {
      string outputEncoding = data.encodingOutput.Value;
      encodingOutput = Encoding.GetEncoding(outputEncoding);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    return (ActionResult) new JsonResult(
      value: new {
        text = Convert.ToBase64String(
          Encoding.Convert(
            srcEncoding: inputEncoding,
            dstEncoding: encodingOutput,
            bytes: Convert.FromBase64String((string) data.text)))
      });
  }
}
```

### <a name="net-version"></a>.NET のバージョン

**.NET standard** および Azure Functions **バージョン 2** で使用する例を次に示します。

```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json;

    public static class ConversionFunctionNET
    {
        [FunctionName("ConversionFunctionNET")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequest req, TraceWriter log)
        {
            log.Info("C# HTTP trigger function processing a request.");

            Encoding inputEncoding = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null)
            {
                return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
            }

            Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

            try
            {
                string encodingInput = data.encodingInput.Value;
                inputEncoding = Encoding.GetEncoding(name: encodingInput);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            Encoding encodingOutput = null;
            try
            {
                string outputEncoding = data.encodingOutput.Value;
                encodingOutput = Encoding.GetEncoding(outputEncoding);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            return (ActionResult)new JsonResult(
                value: new
                {
                    text = Convert.ToBase64String(
                        Encoding.Convert(
                            srcEncoding: inputEncoding,
                            dstEncoding: encodingOutput,
                            bytes: Convert.FromBase64String((string)data.text)))
                });
        }
    }
```

これらの同じ概念を使用して、[ワークフローから Unicode 以外のペイロードを送信する](#send-non-unicode-payload)こともできます。

## <a name="sample-payload-conversions"></a>サンプルのペイロード変換

この例にある Base64 でエンコードされたサンプル入力文字列は、アクセント記号付きの文字を含む個人名の *H&eacute;lo&iuml;se* です。

入力例:

```json
{  
    "text": "SMOpbG/Dr3Nl",
    "encodingInput": "utf-8",
    "encodingOutput": "windows-1252"
}
```

出力例:

```json
{
    "text": "U01PcGJHL0RyM05s"
}
```

## <a name="send-non-unicode-payload"></a>Unicode 以外のペイロードを送信する

ワークフローから Unicode 以外のペイロードを送信する必要がある場合は、[ペイロードを UTF-8 に変換する](#convert-payload-encoding)手順を逆順で実行します。 テキストは、システム内で可能な限り UTF-8 のまま維持します。 次に、同じ関数を使用して、base64 でエンコードされた UTF-8 文字を目的のエンコーディングに変換します。 次に、テキストに base64 デコードを適用し、ペイロードを送信します。

## <a name="convert-payloads-for-as2"></a>AS2 のペイロードを変換する

[AS2 v2 コネクタ](logic-apps-enterprise-integration-as2.md)では、Unicode 以外のペイロードでこのソリューションを使用することもできます。 AS2 に渡すペイロードを UTF-8 に変換しないと、ペイロードの解釈時に問題が発生する可能性があります。 このような問題が発生すると文字が誤って解釈されるため、パートナー間の MIC ハッシュが不一致になる可能性があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Enterprise Integration Pack を使用して、Azure Logic Apps でフラット ファイルをエンコードおよびデコードする](logic-apps-enterprise-integration-flatfile.md)
