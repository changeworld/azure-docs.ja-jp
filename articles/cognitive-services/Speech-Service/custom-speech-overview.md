---
title: Custom Speech の概要 - 音声サービス
titleSuffix: Azure Cognitive Services
description: Custom Speech は、ご利用のアプリケーション、ツール、製品に使用する音声テキスト変換の正確性を評価して改善することのできる一連のオンライン ツールです。 作業を始めるにあたって必要なのは、少数のテスト オーディオ ファイルだけです。 以下のリンクに従って、カスタム音声テキスト変換エクスペリエンスの作成を開始しましょう。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 6dbe97d615753f0a90c8ba80aa7afa6dafa15eb2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658470"
---
# <a name="what-is-custom-speech"></a>Custom Speech とは

[Custom Speech](https://aka.ms/customspeech) は、アプリケーションや製品での Microsoft の音声テキスト変換の正確性を評価して改善するために使用できる、UI ベースのツールのセットです。 作業を始めるにあたって必要なのは、少数のテスト オーディオ ファイルだけです。 以下のリンクに従って、カスタム音声テキスト変換エクスペリエンスの作成を開始しましょう。

## <a name="whats-in-custom-speech"></a>Custom Speech の内容

Custom Speech で何らかの操作を行うには、Azure アカウントと音声サービスのサブスクリプションが必要です。 アカウントの取得後、データの準備、モデルのトレーニングとテスト、認識品質の調査、正確性の評価を行い、最後にそのカスタム音声テキスト変換モデルをデプロイして使用することができます。

この図は、[Custom Speech ポータル](https://aka.ms/customspeech)を構成する諸要素を示しています。 それぞれのステップの詳細については、下のリンクを使用してください。

![Custom Speech ポータルを構成するさまざまな要素を示した図。](./media/custom-speech/custom-speech-overview.png)

1. [サブスクライブしてプロジェクトを作成する](#set-up-your-azure-account) - Azure アカウントを作成し、音声サービスをサブスクライブします。 この統合されたサブスクリプションにより、音声テキスト変換、テキスト読み上げ、音声翻訳、および [Custom Speech ポータル](https://speech.microsoft.com/customspeech)にアクセスできるようになります。 その後、音声サービス サブスクリプションを使用して、初めての Custom Speech プロジェクトを作成します。

1. [テスト データをアップロードする](how-to-custom-speech-test-data.md) - テスト データ (オーディオ ファイル) をアップロードして、ご利用のアプリケーション、ツール、製品に使用する Microsoft の音声テキスト変換プランを評価します。

1. [認識品質を調査する](how-to-custom-speech-inspect-data.md) - [Custom Speech ポータル](https://speech.microsoft.com/customspeech)を使用して、アップロードした音声を再生し、テスト データの音声認識品質を調査します。 定量的な測定については、[データの調査](how-to-custom-speech-inspect-data.md)に関するページを参照してください。

1. [正確性を評価して改善する](how-to-custom-speech-evaluate-data.md) - 音声テキスト変換モデルの正確性を評価して改善します。 さらにトレーニングが必要であるかどうかは、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)で得られる "*ワード エラー率*" を使用して判断できます。 正確性に問題がなければ、音声サービスの API を直接使用できます。 相対平均で 5% から 20% 正確性を高めたい場合、ポータルの **[トレーニング]** タブを使用して、追加のトレーニング データ (人間がラベル付けしたトランスクリプトと関連テキストなど) をアップロードします。

1. [モデルをトレーニングしてデプロイする](how-to-custom-speech-train-model.md) - 音声テスト データと一緒に、文書化されたトランスクリプト (10 から 1,000 時間) と関連テキスト (200 MB 未満) を入力することにより、音声テキスト変換モデルの正確性を高めます。 このデータが音声テキスト変換モデルのトレーニングに役立てられます。 トレーニング後、再テストを行い、その結果に問題がなければ、モデルをカスタム エンドポイントにデプロイすることができます。

## <a name="set-up-your-azure-account"></a>Azure アカウントの設定

[Custom Speech ポータル](https://speech.microsoft.com/customspeech)を使用してカスタム モデルを作成する前に、Azure アカウントと音声サービス サブスクリプションが必要です。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](overview.md#try-the-speech-service-for-free)。

> [!NOTE]
> 必ず Standard (S0) サブスクリプションを作成してください。無料 (F0) サブスクリプションはサポートされません。

Azure アカウントと音声サービス サブスクリプションを作成したら、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)にサインインして、ご利用のサブスクリプションを接続する必要があります。

1. [Custom Speech ポータル](https://aka.ms/custom-speech)にサインインします。
1. 作業に必要なサブスクリプションを選択し、音声プロジェクトを作成します。
1. ご利用のサブスクリプションに変更を加えたい場合は、上部のナビゲーションにある **歯車** アイコンを使用します。

## <a name="how-to-create-a-project"></a>プロジェクトを作成する方法

データ、モデル、テスト、エンドポイントなどのコンテンツは、[Custom Speech ポータル](https://speech.microsoft.com/customspeech)の **[プロジェクト]** にまとめられます。 プロジェクトは、ドメインと国および言語ごとに作成されます。 たとえば、米国の英語を使用するコール センターのプロジェクトを作成することが考えられます。

初めてのプロジェクトを作成するには、 **[音声テキスト変換/Custom Speech]** を選択し、 **[新しいプロジェクト]** をクリックします。 ウィザードの手順に従ってプロジェクトを作成します。 プロジェクトの作成後、4 つのタブが表示されます ( **[データ]** 、 **[テスト中]** 、 **[トレーニング]** 、 **[デプロイ]** )。 それぞれのタブの使い方については、「[次の手順](#next-steps)」に記載のリンクを使用してください。

> [!IMPORTANT]
> [Custom Speech ポータル](https://aka.ms/custom-speech)は最近更新されました。 CRIS.ai ポータルで、または API を使用して以前のデータ、モデル、テスト、および公開されたエンドポイントを作成した場合は、新しいポータルで新しいプロジェクトを作成して、これらの古いエンティティに接続する必要があります。

## <a name="model-lifecycle"></a>モデルのライフサイクル

カスタム音声には、**基本モデル** と **カスタム モデル** の両方が使用されます。 言語ごとに、1 つ以上の **基本モデル** があります。 一般に、新しい音声モデルが通常の音声サービスにリリースされると、新しい **基本モデル** として Custom Speech サービスにもインポートされます。 通常、これらは 3 から 6 か月に 1 回更新され、普通は最新のモデルの方が精度が高いため、古いモデルは時間の経過と共にあまり役に立たなくなります。

これに対し、**カスタム モデル** は、選択した基本モデルを特定の顧客シナリオに適合させることによって作成されます。 特定のカスタム モデルがニーズを満たすようになったら、それを長期間使い続けることができますが、定期的に最新の基本モデルに更新し、追加されたデータで再トレーニングすることをお勧めします。

モデルのライフサイクルに関しては、他に次のような用語が重要です。

* **適応**: 基本モデルを選択し、テキスト データやオーディオ データを使用して、ドメインやシナリオに合わせてカスタマイズすること
* **デコード**: モデルを使用して音声認識を実行すること (音声をテキストにデコードする)
* **エンドポイント**: 特定のユーザー "*のみ*" がアクセスできる、基本モデルまたはカスタム モデルのユーザー固有のデプロイ。

### <a name="expiration-timeline"></a>有効期限のタイムライン

新しいモデルや新機能が利用可能になると、古くて正確さが低下したモデルは廃止されます。モデルとエンドポイントの有効期限については、次のタイムラインを参照してください。

**基本モデル** 

* 適応: 1 年間使用できます。 モデルがインポートされたら、カスタム モデルを作成するために 1 年間使用できます。 1 年後には、より新しいバージョンの基本モデルから新しいカスタム モデルを作成する必要があります。  
* デコード: インポートの後で 2 年間使用できます。 これは、このモデルで 2 年間はエンドポイントを作成し、バッチ トランスクリプトを使用できることを意味します。 
* エンドポイント: デコードと同じタイムラインで使用できます

**カスタム モデル**

* デコード: モデルが作成されてから 2 年間使用できます。 これは、カスタム モデルを作成してから 2 年間 (バッチ、リアルタイム、テスト) 使用できることを意味します。 2 年後には、基本モデルが適合に非推奨になっていることが多いため、**モデルを再トレーニングする必要があります**。  
* エンドポイント: デコードと同じタイムラインで使用できます

基本モデルまたはカスタム モデルいずれかの有効期限が切れると、常に **最新バージョンの基本モデル** にフォールバックします。 このため、実装が中断されることはありませんが、カスタム モデルの有効期限が切れると、"*特定のデータ*" に対して精度が低下する可能性があります。 モデルの有効期限は、Custom Speech ポータルの次の場所で確認できます。

* モデルのトレーニングの概要
* モデルのトレーニングの詳細
* デプロイの概要
* デプロイの詳細

また、[`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) または [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) Custom Speech API を使用し、JSON 応答の `deprecationDates` プロパティで、有効期限を確認することもできます。

Custom Speech ポータルのデプロイ セクションまたは Custom Speech API を使用して、エンドポイントで使用されているモデルを変更することにより、ダウンタイムなしで Custom Speech エンドポイントのモデルをアップグレードすることができます。

## <a name="next-steps"></a>次のステップ

* [データを準備してテストする](how-to-custom-speech-test-data.md)
* [データを検査する](how-to-custom-speech-inspect-data.md)
* [モデルの精度を評価して改善する](how-to-custom-speech-evaluate-data.md)
* [モデルのトレーニングとデプロイ](how-to-custom-speech-train-model.md)
