---
title: キーワード作成クイックスタート - Speech サービス
titleSuffix: Azure Cognitive Services
description: デバイスは常にキーワード (またはフレーズ) をリッスンしています。 ユーザーがキーワードを話すと、ユーザーが話をやめるまで、デバイスからクラウドにディクテーションが送信されます。 キーワードをカスタマイズすることは、デバイスを差別化し、ブランドを強化する上で効果的な方法です。
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: eur
ms.custom: devx-track-csharp, ignite-fall-2021
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 59756ec624bfc4d716bcf222195f53029be05420
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493185"
---
# <a name="get-started-with-custom-keyword"></a>Custom Keyword の概要

このクイックスタートでは、カスタム キーワードの操作の基本について学びます。 キーワードは、音声で製品をアクティブにするための単語または短い語句です。 キーワード モデルは、Speech Studio 内で作成します。 次に、アプリケーション内の Speech SDK で使用するモデル ファイルをエクスポートします。

## <a name="prerequisites"></a>前提条件

この記事の手順では、Speech サブスクリプションと Speech SDK が必要です。 まだサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](overview.md#try-the-speech-service-for-free)。 SDK を入手するには、ご利用のプラットフォームの[インストール ガイド](quickstarts/setup-platform.md)を参照してください。

## <a name="create-a-keyword-in-speech-studio"></a>Speech Studio でキーワードを作成する

カスタム キーワードを使用する前に、[Speech Studio](https://aka.ms/sdsdk-speechportal) の [[Custom Keyword]\(カスタム キーワード\)](https://aka.ms/sdsdk-wakewordportal) ページを使用してキーワードを作成する必要があります。 キーワードを指定すると、Speech SDK で使用できる `.table` ファイルが生成されます。

> [!IMPORTANT]
> カスタム キーワード モデルと、生成される `.table` ファイルは、Speech Studio で **のみ** 作成できます。
> SDK または REST 呼び出しを使用してカスタム キーワードを作成することはできません。

1. [Speech Studio](https://aka.ms/sdsdk-speechportal) に移動して **[サインイン]** します。 Speech サブスクリプションをお持ちでない場合には、[**音声サービスの作成**](https://go.microsoft.com/fwlink/?linkid=2086754)に移動します。

1. [[カスタム キーワード]](https://aka.ms/sdsdk-wakewordportal) ページで **[新しいプロジェクトの作成]** を選択します。 

1. カスタム キーワード プロジェクトの **[名前]** 、 **[説明]** 、 **[言語]** を入力します。 言語は 1 プロジェクトにつき 1 つのみ選択できます。現在、サポートは英語 (米国) と中国語 (標準、簡体字) に制限されています。 

    ![キーワード プロジェクトについて説明する](media/custom-keyword/custom-kw-portal-new-project.png)

1. 一覧からプロジェクト名を選択します。 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-project-list.png" alt-text="キーワード プロジェクトの選択。":::

1. 仮想アシスタントのカスタム キーワードを作成して、 **[新しいモデルを作成する]** を選択します。

1. モデルの **[名前]** 、 **[説明]** 、 **[キーワード]** を自由に入力して、 **[次へ]** を選択します。 効果的なキーワードを選択する方法については、[ガイドライン](keyword-recognition-guidelines.md#choosing-an-effective-keyword)を参照してください。

    ![キーワードを入力する](media/custom-keyword/custom-kw-portal-new-model.png)

1. ポータルで、キーワードの発音候補が作成されます。 再生ボタンを選択して各候補の音声を聞き、正しくない発音があれば横にあるチェックを削除します。ユーザーによるキーワードの読み方として想定されるものに対応するすべての発音を選択し、 **[次へ]** を選択すると、キーワード モデルの生成が始まります。 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-choose-prons.png" alt-text="正しい発音を選択する場所を示すスクリーンショット。":::

1. モデルの種類を選択し、 **[作成]** を選択します。 モデルタイプ **[高度]** をサポートしているリージョンの一覧は、「[キーワード認識がサポートされているリージョン](keyword-recognition-region-support.md)」ドキュメントで確認できます。 

1. モデルが生成されるまでに最大で 30 分かかる場合があります。 モデルが完了すると、キーワードの一覧が **[処理中]** から **[成功]** に変わります。 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-review-keyword.png" alt-text="キーワードの確認。":::

1. 左側の折りたたみ可能なメニューから **[Tune]\(チューニング\)** を選択してモデルのチューニング オプションを選択したら、モデルをダウンロードします。 ダウンロードしたファイルは `.zip` アーカイブです。 アーカイブを抽出すると、`.table` 拡張子を持つファイルが表示されます。 SDK では `.table` ファイルを使用するので、パスをメモしておいてください。

    :::image type="content" source="media/custom-keyword/custom-kw-portal-download-model.png" alt-text="モデルのテーブルのダウンロード。":::


## <a name="use-a-keyword-model-with-the-speech-sdk"></a>Speech SDK でのキーワード モデルの使用

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Speech SDK を取得する](speech-sdk.md)
