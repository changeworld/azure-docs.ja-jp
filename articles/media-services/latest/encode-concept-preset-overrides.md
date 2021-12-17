---
title: ジョブの送信で変換の設定を変更するためのプリセット オーバーライドの使用
description: この記事では、プリセット オーバーライドを使用して、ジョブのインスタンスごとに変換の設定を調整する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: johndeu
ms.custom: seodec18
ms.openlocfilehash: 3b748aa9e6b8d9f79572b05d10258839aabfa8f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699619"
---
# <a name="using-preset-overrides-to-control-per-job-settings"></a>ジョブごとに設定を制御するためのプリセット オーバーライドの使用

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="transforms-and-jobs-overview"></a>変換とジョブの概要

Media Services v3 でエンコードするには、[変換](/rest/api/media/transforms)と[ジョブ](/rest/api/media/jobs)を作成する必要があります。 変換では、エンコード設定と出力のレシピを定義します。ジョブはレシピのインスタンスです。 詳しくは、「[Transform と Job](transform-jobs-concept.md)」をご覧ください。

Media Services で分析をエンコードまたは使用するときに、変換でカスタム プリセットを定義して、使用する設定を定義できます。 場合によっては、すべてのシナリオに対してカスタム変換を作成する必要がないよう、ジョブごとに変換の設定をオーバーライドする必要があります。 変換プリセットの設定をオーバーライドするには、ジョブを変換に送信する前に、[ジョブ出力資産](/dotnet/api/microsoft.azure.management.media.models.joboutputasset)のプリセット オーバーライド プロパティを使用できます。

## <a name="preset-overrides"></a>プリセットのオーバーライド

プリセット オーバーライドを使用すると、変換オブジェクトに指定された設定を初回作成後にオーバーライドする、カスタマイズされたプリセットを渡すことができます。  このプロパティは、変換に新しいジョブを送信するときに、[ジョブ出力資産](/dotnet/api/microsoft.azure.management.media.models.joboutputasset)で使用できます。

これは、カスタム定義の変換の一部のプロパティ、または組み込みプリセットのプロパティをオーバーライドする必要がある場合に便利です。 たとえば、[オーディオ アナライザーの組み込みプリセット](/rest/api/media/transforms/create-or-update#audioanalyzerpreset)を使用するカスタム変換を作成したが、英語の "en-us" のオーディオ言語設定を使用するようにそのプリセットを最初に設定するシナリオについて考えます。  この結果の変換では、送信される各ジョブが、米国英語専用として音声テキスト変換文字起こしエンジンに送信されます。 その変換に送信されるすべてのジョブは、"en-us" 言語設定に固定されます。 すべての言語に対して変換を定義することで、このシナリオを回避できますが、それでは管理がはるかに難しく、アカウントの変換クォータの制限に達してしまう可能性があります。
このシナリオの最善の解決策は、ジョブを変換に送信する前に、ジョブ出力資産に対してプリセット オーバーライドを使用することです。  そうすれば、1 つの "オーディオ文字起こし" 変換を定義し、ジョブごとに必要な言語設定を渡すことができます。

プリセット オーバーライドを使用すると、変換に送信されるジョブごとに新しいカスタム プリセット定義を渡すことができます。 このプロパティは、2021-06-01 バージョンの API に基づくすべての SDK バージョンの[ジョブ出力](/dotnet/api/microsoft.azure.management.media.models.joboutput)エンティティで使用できます。

詳細については、REST のドキュメントでジョブ出力エンティティの [presetOverride](https://github.com/Azure/azure-rest-api-specs/blob/ce90f9b45945c73b8f38649ee6ead390ff6efe7b/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L1960) プロパティを参照してください。

> [!NOTE]
> プリセット オーバーライドを使用してできるのは、変換で定義されているプリセットの設定をオーバーライドすることだけです。  特定のプリセットから別の種類に切り替えることはできません。 たとえば、組み込みのコンテンツ認識エンコード プリセットで作成された変換を、オーディオ アナライザーのような別のプリセットを使用するようにオーバーライドしようとすると、エラー メッセージが表示されます。


## <a name="example-of-preset-override-in-net"></a>.NET でのプリセット オーバーライドの例

基本的なオーディオ アナライザー変換でプリセット オーバーライドを使用する方法を示す Media Services 用 .NET SDK の完全な使用例は、GitHub から入手できます。
ジョブ出力のプリセット オーバーライド プロパティを使用する方法の詳細については、「[オーディオ アナライザー プリセットを使用してメディア ファイルを分析する](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)」を参照してください。

## <a name="sample-code-of-preset-override-in-net"></a>.NET でのプリセット オーバーライドのサンプル コード

[!code-csharp[Main](../../../media-services-v3-dotnet/AudioAnalytics/AudioAnalyzer/program.cs#PresetOverride)]

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

* [Media Services を使用してアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
* [組み込みのプリセットを使用して HTTPS の URL をエンコードする](job-input-from-http-how-to.md)
* [組み込みのプリセットを使用してローカル ファイルをエンコードする](job-input-from-local-file-how-to.md)
* [自分の特定のシナリオまたはデバイス要件に対応するカスタム プリセットを構築する](transform-custom-presets-how-to.md)。
