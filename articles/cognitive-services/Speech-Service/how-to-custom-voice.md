---
title: カスタム音声を使用して合成を改善する - 音声サービス
titleSuffix: Azure Cognitive Services
description: Custom Voice は、ブランド用に認識性の高い固有の音声を作成できる、一連のオンライン ツールです。 作業を始めるにあたって必要なのは、少数のオーディオ ファイルと関連する文字起こしだけです。 以下のリンクに従って、カスタム音声テキスト変換エクスペリエンスの作成を開始しましょう。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 45f4ca6349c14f21ce48a1fcf34b7fc8998dd278
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733518"
---
# <a name="get-started-with-custom-voice"></a>Custom Voice の概要

[Custom Voice](https://aka.ms/customvoice) は、ブランド用に認識性の高い固有の音声を作成できる、一連のオンライン ツールです。 作業を始めるにあたって必要なのは、少数のオーディオ ファイルと関連する文字起こしだけです。 以下のリンクに従って、カスタム テキスト読み上げエクスペリエンスの作成を開始しましょう。

## <a name="whats-in-custom-voice"></a>Custom Voice の内容

Custom Voice を開始する前に、Azure アカウントと音声サービスのサブスクリプションが必要になります。 アカウントを作成したら、データの準備、モデルのトレーニングおよびテスト、音声品質の評価、そしてカスタム音声モデルのデプロイを行えます。

下の図では、[Custom Voice ポータル](https://aka.ms/customvoice)を使用してカスタム音声モデルを作成する手順を示しています。 リンクを使用して、詳細を確認してください。

![Custom Voice のアーキテクチャ図](media/custom-voice/custom-voice-diagram.png)

1. [サブスクライブしてプロジェクトを作成する](#set-up-your-azure-account) - Azure アカウントを作成し、音声サービスのサブスクリプションを作成します。 この一元化されたサブスクリプションを通じて、音声テキスト変換、テキスト読み上げ、音声翻訳、Custom Voice ポータルにアクセスすることができます。 次に、音声サービス サブスクリプションを使用して、最初の Custom Voice プロジェクトを作成します。

2. [データをアップロードする](how-to-custom-voice-create-voice.md#upload-your-datasets) - Custom Voice ポータルまたは Custom Voice API を使用して、データ (オーディオとテキスト) をアップロードします。 ポータルで、発音スコアと信号対雑音比を調査および評価できます。 詳細については、[Custom Voice 用にデータを準備する方法](how-to-custom-voice-prepare-data.md)に関する記事を参照してください。

3. [モデルをトレーニングする](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) - 自分のデータを使用して、カスタム テキスト読み上げ音声モデルを作成します。 モデルはさまざまな言語でトレーニングできます。 トレーニング後にモデルをテストし、結果に満足したら、モデルをデプロイできます。

4. [モデルをデプロイする](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - 自分のテキスト読み上げ音声モデルのカスタム エンドポイントを作成して、自分の製品、ツール、アプリケーションの音声合成にそれを使用します。

## <a name="custom-neural-voices"></a>カスタム ニューラル音声

現在、Custom Voice では、Standard およびニューラル レベルがサポートされています。 カスタム ニューラル音声によって、ユーザーはより少ないデータでより高品質の音声モデルを構築できるようになります。また、AI を責任を持ってデプロイするための手段として利用できます。 カスタム ニューラル音声を使用して、より自然な会話インターフェイスのためのよりリアルな音声を開発し、顧客とエンド ユーザーが責任ある方法で最新のテキスト読み上げテクノロジを使用できるようにすることをお勧めします。 [カスタム ニューラル音声の詳細については、こちらを参照してください](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)。 

> [!NOTE]
> 責任ある AI を設計するという Microsoft の取り組みの一環として、Microsoft ではカスタム ニューラル音声の使用を制限しています。 このテクノロジにアクセスできるのは、アプリケーションの審査を受け、弊社の責任ある AI の原則に沿ってアプリケーションを使用することを確約した場合のみです。 詳細については、[アクセスの制限に対するポリシー](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)と[適用](https://aka.ms/customneural)に関する記事を参照してください。 Custom Voice の Standard バージョンとニューラル バージョンでサポートされている[言語](language-support.md#customization)と[リージョン](regions.md#custom-voices)は異なります。 開始する前に詳細を確認してください。  

## <a name="set-up-your-azure-account"></a>Azure アカウントの設定

Custom Speech ポータルを使用してカスタム モデルを作成する前に、音声サービス サブスクリプションが必要です。 Azure で音声サービス サブスクリプションを作成するには、以下の手順に従います。 Azure アカウントがない場合、新しいものにサインアップできます。  

Azure アカウントと音声サービス サブスクリプションを作成したら、Custom Voice ポータルにサインインして、自分のサブスクリプションを接続する必要があります。

1. Azure portal から音声サービス サブスクリプション キーを取得します。
2. [Custom Voice ポータル](https://aka.ms/custom-voice)にサインインします。
3. 自分のサブスクリプションを選択し、音声プロジェクトを作成します。
4. 別の Speech サブスクリプションに切り替えたい場合は、上部のナビゲーションにある歯車アイコンを使用します。

> [!NOTE]
> サービスを使用する前に、Azure で F0 または S0 の Speech Service キーを作成しておく必要があります。 カスタム ニューラル音声では S0 レベルのみがサポートされています。 

## <a name="how-to-create-a-project"></a>プロジェクトを作成する方法

データ、モデル、テスト、エンドポイントなどのコンテンツは、Custom Voice ポータルの **[プロジェクト]** にまとめられます。 各プロジェクトは、国と言語、および作成したい音声の性別に固有です。 たとえば、米国の英語 ('en-US') を使用するコール センターのチャット ボット用に、女性の音声のプロジェクトを作成できます。

初めてのプロジェクトを作成するには、**[Text-to-Speech/Custom Voice]\(テキスト読み上げ/Custom Voice\)** タブを選択して、**[新しいプロジェクト]** をクリックします。 ウィザードの手順に従ってプロジェクトを作成します。 プロジェクトの作成後、4 つのタブが表示されます: **[データ]** 、 **[トレーニング]** 、 **[テスト中]** 、 **[デプロイ]** 。 それぞれのタブの使い方については、「[次の手順](#next-steps)」に記載のリンクを使用してください。

> [!IMPORTANT]
> [カスタム音声ポータル](https://aka.ms/custom-voice)は最近更新されました。 CRIS.ai ポータルで、または API を使用して以前のデータ、モデル、テスト、および公開されたエンドポイントを作成した場合は、新しいポータルで新しいプロジェクトを作成して、これらの古いエンティティに接続する必要があります。

## <a name="how-to-migrate-to-custom-neural-voice"></a>カスタム ニューラル音声に移行する方法

ニューラル以外 (または標準) の Custom Voice を使用している場合は、次の手順に従って、カスタム ニューラル音声に移行することを検討してください。 カスタム ニューラル音声の使用に移行することで、より自然な会話インターフェイスのためのよりリアルな音声を開発し、顧客とエンド ユーザーが責任ある方法で最新のテキスト読み上げテクノロジを使用できるようになります。 

1. 詳細については、[アクセスの制限に対するポリシー](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)と[適用](https://aka.ms/customneural)に関する記事を参照してください。 カスタム ニューラル音声サービスへのアクセスは、Microsoft の資格基準に基づいて、Microsoft の独自の裁量の対象であることに注意してください。 お客様は、お客様のアプリケーションがレビューされ、「[責任ある AI の原則](https://microsoft.com/ai/responsible-ai)」と[倫理規定](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)に従ってこのテクノロジを使用することに合意した後にのみ、このテクノロジにアクセスできます。 
2. アプリケーションが承認されると、"ニューラル" トレーニング機能にアクセスできるようになります。 アプリケーションで指定したのと同じ Azure サブスクリプションを使用して [Custom Voice ポータル](https://speech.microsoft.com/customvoice)にログインしていることを確認します。 
    > [!IMPORTANT]
    > ボイス タレントを保護し、無許可の録音やボイス タレントからの承諾なしの音声モデルのトレーニングを防止するために、ボイス タレントが自分の同意を与える録音された声明をお客様がアップロードすることが求められます。 録音スクリプトを準備するときは、この文を必ず含めてください。 "I [state your first and last name] am aware that recordings of my voice will be used by [state the name of the company] to create and use a synthetic version of my voice." (私 [自分の姓名] は、私の音声の合成バージョンを作成して使用するために、私の音声が [会社名] によって使用されることを承知しています。)
    > この文は、音声による同意ファイルとして **[ボイス タレント]** タブにアップロードする必要があります。 この文は、トレーニング データセット内の録音が、同意したのと同じ人物によって行われたかどうかを確認するために使用されます。
3. カスタム ニューラル音声モデルが作成されたら、新しいエンドポイントに音声モデルをデプロイします。 ニューラル音声モデルを使用して新しいカスタム音声エンドポイントを作成するには、 **[テキスト読み上げ]、[Custom Voice]、[展開]** の順に移動します。 **[モデルのデプロイ]** を選択し、ご自分のカスタム エンドポイントの **名前** と **説明** を入力します。 次に、このエンドポイントに関連付けるカスタム ニューラル音声モデルを選択し、デプロイを確認します。  
4. 新しいモデルを使用して新しいエンドポイントを作成した場合は、アプリ内のコードを更新します。 

## <a name="next-steps"></a>次のステップ

- [Custom Voice のデータを用意する](how-to-custom-voice-prepare-data.md)
- [Custom Voice を作成する](how-to-custom-voice-create-voice.md)
- [ガイド:音声サンプルを録音する](record-custom-voice-samples.md)