---
title: Audio Content Creation - Speech サービス
titleSuffix: Azure Cognitive Services
description: Audio Content Creation は、Microsoft のテキスト読み上げ出力をご使用のアプリや製品に合わせてカスタマイズしたり微調整したりするためのオンライン ツールです。
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: eur
ms.openlocfilehash: 866bb0c361856a906e71d47dd19891c276a450ca
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507666"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Audio Content Creation ツールを使用して合成を向上させる

[Audio Content Creation](https://aka.ms/audiocontentcreation) は、オーディオブック、ニュース放送、ビデオ ナレーション、チャットボットなどのさまざまなシナリオに対応した自然度の高いオーディオ コンテンツを作成できる、使いやすく強力なツールです。 Audio Content Creation を使用すると、効率的かつ低コストの方法で、テキスト読み上げ音声を微調整したり、カスタマイズされたオーディオ エクスペリエンスを設計したりできます。

このツールは、[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) に基づいています。 これにより、音声の特徴、音声スタイル、話す速度、発音、韻律などのテキスト読み上げ出力属性をリアルタイムまたはバッチ合成で調整できます。

60 種類以上の言語にわたる 150 以上の事前構築済みの音声に簡単にアクセスできます。これには、最先端のニューラル TTS 音声や自分のカスタム音声 (作成済みの場合) が含まれます。

Audio Content Creation の[ビデオ チュートリアル](https://youtu.be/ygApYuOOG6w)を参照してください。

## <a name="how-to-get-started"></a>開始方法

Audio Content Creation は無料のツールですが、使用する Azure Speech サービスに対して課金されます。 このツールを使用するには、Azure アカウントでログインし、音声リソースを作成する必要があります。 Azure アカウントごとに、月単位の無料音声クォータがあります。これには、ニューラル TTS 音声の場合は (1 か月あたり) 50 万文字、標準およびカスタムの音声の場合は (1 か月あたり) 500 万文字、さらに (1 か月あたり) 1 つのカスタム音声エンドポイント ホスティング サービスが含まれます。 月単位で割り当てられる量は、通常、3 人から 5 人の小規模なコンテンツ チームにとっては十分です。 ここでは、Azure アカウントを作成し、音声リソースを取得する方法の手順について説明します。

### <a name="step-1---create-an-azure-account"></a>手順 1 - Azure アカウントを作成する

Audio Content Creation を使用するには、[Microsoft アカウント](https://account.microsoft.com/account)と [Azure アカウント](https://azure.microsoft.com/free/ai/)が必要です。 これらの手順に従って、[アカウントを設定](./overview.md#try-the-speech-service-for-free)します。

[Azure portal](https://portal.azure.com/) は、Azure アカウントを管理するための一元的な場所です。 音声リソースを作成し、製品アクセスを管理し、単純な Web アプリから複雑なクラウド デプロイまで、あらゆるものを監視できます。

### <a name="step-2---create-a-speech-resource"></a>手順 2 - 音声リソースを作成する

Azure アカウントにサインアップしたら、Speech サービスにアクセスするために、Azure アカウントで音声リソースを作成する必要があります。 [音声リソースを作成する方法](./overview.md#create-the-azure-resource)についての説明をご覧ください。

新しい音声リソースを展開するまでに少し時間がかかります。 デプロイが完了したら、Audio Content Creation の使用を開始できます。

 > [!NOTE]
   > ニューラル音声を使用する予定の場合は、[ニューラル音声をサポートするリージョン](regions.md#neural-and-standard-voices)でリソースを作成するようにしてください。

### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>手順 3 - Azure アカウントと音声リソースを使用して Audio Content Creation にログインする

1. Azure アカウントと音声リソースを取得したら、 **[Get started]\(作業の開始\)** をクリックして [Audio Content Creation](https://aka.ms/audiocontentcreation) にログインできます。
2. ホーム ページでは、Speech Studio ですべての製品が一覧表示されます。 **[Audio Content Creation]** をクリックして開始します。
3. **[Speech Studio へようこそ]** ページが表示され、そこで音声サービスについて設定できます。 Azure サブスクリプションと、作業する音声リソースを選択します。 **[リソースを使用する]** をクリックして設定を完了します。 Audio Content Creation ツールに次回ログインすると、ユーザーは現在の音声リソースの下のオーディオ作業ファイルに直接リンクされます。 Azure サブスクリプションの詳細と状態については、[Azure portal](https://portal.azure.com/) で確認できます。 Azure サブスクリプションの所有者または管理者であれば、使用可能な音声リソースがない場合に、 **[新しいリソースの作成]** をクリックして Speech Studio で新しい音声リソースを作成することもできます。 特定の Azure サブスクリプションのユーザー ロールである場合は、新しい音声リソースを作成するアクセス許可が付与されていない可能性があります。 音声リソースへのアクセスを取得するには、管理者にお問い合わせください。 
4. 上部のナビゲーションにある **[設定]** オプションを使用して、いつでも音声リソースを変更できます。
5. ディレクトリを切り替える場合は、 **[設定]** またはプロファイルに移動して操作してください。 

## <a name="how-to-use-the-tool"></a>ツールの使用方法

次の図に、テキスト読み上げの出力を微調整するための手順を示します。 それぞれのステップの詳細については、下のリンクを使用してください。

:::image type="content" source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="テキスト読み上げの出力を微調整するための手順を示す図":::

1. 作業する音声リソースを選択します。
2. プレーン テキストまたは SSML スクリプトを使用して、[音声チューニング ファイルを作成](#create-an-audio-tuning-file)します。 Audio Content Creation にコンテンツを入力するか、アップロードします。
3. スクリプトの内容の音声と言語を選択します。 Audio Content Creation には、すべての [Microsoft テキスト読み上げ音声](language-support.md#text-to-speech)が含まれています。 標準、ニューラル、または独自のカスタム音声を使用できます。
   > [!NOTE]
   > カスタム ニューラル音声には、ゲート アクセスを使用できます。これにより、自然な音声と同じような高品位の音声を作成できます。 詳細については、[ゲート プロセス](./text-to-speech.md)に関するページを参照してください。

4. プレビューするコンテンツを選択し、**再生** アイコン (三角形) をクリックして、既定の合成出力をプレビューします。 テキストに変更を加えた場合は、**停止** アイコンをクリックしてから、**再生** アイコンをもう一度クリックして、変更されたスクリプトでオーディオを再生成する必要があります。 
5. 発音、切れ目、ピッチ、速さ、抑揚、音声スタイルなどを調整して、出力を改善します。 オプションの完全な一覧については、「[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md)」を参照してください。 次の[ビデオ](https://youtu.be/ygApYuOOG6w)で、Audio Content Creation を使用して音声出力を微調整する方法を示します。
6. [チューニングした音声を保存してエクスポートします](#export-tuned-audio)。 チューニング トラックをシステムに保存すると、作業を続行して出力を反復処理することができます。 出力に問題がなければ、エクスポート機能を使用して音声作成タスクを作成できます。 エクスポート タスクの状態を監視し、ご使用のアプリや製品で使用するための出力をダウンロードすることができます。

## <a name="create-an-audio-tuning-file"></a>音声チューニング ファイルを作成する

Audio Content Creation ツールにコンテンツを取り込むには、2 つの方法があります。

**オプション 1:**

1. 新しい音声チューニング ファイルを作成するには、 **[新規]**  >  **[ファイル]** をクリックします。
2. 編集ウィンドウに内容を入力するか貼り付けます。 各ファイルの文字数は最大 20,000 文字です。 スクリプトが 20,000 文字を超えている場合は、オプション 2 を使用して、内容を複数のファイルに自動的に分割できます。
3. 忘れずに保存してください。

**オプション 2:**

1. 1 つまたは複数のテキスト ファイルをインポートするには、 **[アップロード]** をクリックします。 プレーンテキストと SSML の両方がサポートされています。 スクリプト ファイルが 20,000 文字を超えている場合は、段落、文字、または正規表現によってファイルを分割してください。
3. テキスト ファイルをアップロードするときは、ファイルが以下の要件を満たしていることを確認してください。

   | プロパティ | 値/備考 |
   |----------|---------------|
   | ファイル形式 | プレーン テキスト (.txt)<br/> SSML テキスト (.txt)<br/> ZIP ファイルはサポートされていません |
   | エンコード形式 | UTF-8 |
   | ファイル名 | 各ファイルには一意の名前が必要です。 重複はサポートされていません。 |
   | テキストの長さ | テキスト ファイルの文字制限は 20,000 です。 ファイルが制限を超える場合は、ツール内の指示に従ってファイルを分割してください。 |
   | SSML の制限 | 各 SSML ファイルには、単一の SSML のみを含めることができます。 |

**プレーン テキストの例**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

**SSML テキストの例**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>チューニングした音声をエクスポートする

音声出力を確認し、チューニングと調整に問題がなければ、音声をエクスポートできます。

1. 音声作成タスクを作成するには、 **[エクスポート]** をクリックします。 長時間のオーディオ出力と完全なオーディオ出力エクスペリエンスがサポートされているため、**オーディオ ライブラリにエクスポートする** ことをお勧めします。 ローカル ディスクに音声を直接ダウンロードすることもできますが、使用できるのは最初の 10 分のみです。
2. チューニングした音声の出力形式を選択します。 サポートされている形式とサンプル レートの一覧については、以下を参照してください。
3. タスクの状態は **[Export task]\(エクスポート タスク\)** タブで確認できます。タスクが失敗した場合は、詳細情報のページで詳細なレポートを確認してください。
4. タスクが完了すると、 **[Audio Library]\(音声ライブラリ\)** タブから音声をダウンロードできるようになります。
5. **[Download]** をクリックします。 これで、カスタムのチューニングされた音声をご使用のアプリや製品で使用する準備ができました。

**サポートされている音声形式**

| Format | 16 kHz サンプル レート | 24 kHz サンプル レート |
|--------|--------------------|--------------------|
| wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Audio Content Creation ユーザーを追加または削除する方法

複数のユーザーが Audio Content Creation を使用する場合は、そのユーザーに Azure サブスクリプションと音声リソースへのアクセス権を付与できます。 ユーザーを Azure サブスクリプションに追加した場合、そのユーザーはその Azure サブスクリプションの下のすべてのリソースにアクセスできます。 しかし、ユーザーをある音声リソースのみに追加した場合、ユーザーはその音声リソースにしかアクセスできず、この Azure サブスクリプションの下の他のリソースにはアクセスできません。 音声リソースへのアクセス権を持つユーザーは、Audio Content Creation を使用できます。

ユーザーは [Microsoft アカウント](https://account.microsoft.com/account)を用意する必要があります。 ユーザーが Microsoft アカウントを持っていなくても、数分で作成できます。 ユーザーは、既存の電子メールを使用して Microsoft アカウントとリンクさせることも、新しい Outlook メールを作成して Microsoft アカウントとして使用することもできます。


### <a name="add-users-to-a-speech-resource"></a>ユーザーを音声リソースに追加する

ユーザーを音声リソースに追加して Audio Content Creation を使用できるようにするには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com/) で **[Cognitive Services]** を検索し、ユーザーを追加する音声リソースを選択します。
2. **[アクセス制御 (IAM)]** をクリックします。 **[追加]**  >  **[ロールの割り当ての追加 (プレビュー)]** を選択して、ロールの割り当ての追加ページを開きます。 
1. **[ロール]** タブで、 **[Cognitive Service User]\(Cognitive Service ユーザー\)** ロールを選択します。 この音声リソースの所有権をユーザーに付与する場合は、 **[所有者]** ロールを選択します。
1. **[メンバー]** タブで、ユーザーのメール アドレスを入力し、ディレクトリ内のユーザーを選択します。 メール アドレスは、Azure Active Directory によって信頼されている **Microsoft アカウント** である必要があります。 ユーザーは、個人のメール アドレスを使用して [Microsoft アカウント](https://account.microsoft.com/account)に簡単にサインアップできます。 
1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。
1. ユーザーは招待メールを受け取ります。 電子メールで **[招待を承諾]**  >  **[Azure への参加を承諾]** をクリックし、招待を受け入れます。 その後、ユーザーは Azure portal にリダイレクトされます。 ユーザーが Azure portal 内で行う必要のあるアクションはありません。 しばらくすると、ユーザーには音声リソース スコープでユーザーロールが割り当てられ、この音声リソースにアクセスできるようになります。 ユーザーが招待メールを受信しなかった場合は、[ロールの割り当て] でユーザーのアカウントを検索し、ユーザーのプロファイル内に移動できます。 [ID] -> [招待が受け入れられました] を探し、 **[(管理)]** をクリックして、電子メールの招待を再送信します。 招待リンクをユーザーにコピーすることもできます。 
1. ユーザーは [Audio Content Creation](https://aka.ms/audiocontentcreation) 製品ページにアクセスするか最新の情報に更新し、ユーザーの Microsoft アカウントでサインインします。 すべての音声製品の中から **[Audio Content Creation]** ブロックを選択します。 ポップアップ ウィンドウまたはページの右上にある設定で、音声リソースを選択します。 ユーザーが使用可能な音声リソースを見つけられない場合は、適切なディレクトリにいるかどうかを確認します。 適切なディレクトリを確認するには、右上隅にあるアカウント プロファイルをクリックし、[現在のディレクトリ] の横にある **[切り替え]** をクリックします。 複数のディレクトリを使用できる場合は、複数のディレクトリにアクセスできることを意味します。 別のディレクトリに切り替えて設定に移動し、適切な音声リソースが使用可能かどうかを確認します。 

    :::image type="content" source="media/audio-content-creation/add-role-first.png" alt-text="ロールの追加ダイアログ":::


同じ音声リソース内にいるユーザーは、Audio Content Creation スタジオでお互いの作業を見ることができます。 個々のユーザーに Audio Content Creation で固有のプライベート ワークプレースを使用させたい場合は、各ユーザーに対して[新しい音声リソースを作成](#step-2---create-a-speech-resource)し、各ユーザーにその音声リソースへの一意のアクセス権を付与してください。

### <a name="remove-users-from-a-speech-resource"></a>音声リソースからユーザーを削除する

1. Azure portal で **[Cognitive Services]** を検索し、ユーザーを削除する音声リソースを選択します。
2. **[アクセス制御 (IAM)]** をクリックします。 **[ロールの割り当て]** タブをクリックして、この音声リソースのすべてのロールの割り当てを表示します。
3. 削除するユーザーを選択し、 **[削除]**  >  **[Ok]** をクリックします。
    :::image type="content" source="media/audio-content-creation/remove-user.png" alt-text="[削除] ボタン":::

### <a name="enable-users-to-grant-access"></a>ユーザーがアクセス権を付与できるようにする

いずれかのユーザーが他のユーザーにアクセス権を付与できるようにする場合は、そのユーザーに音声リソースの所有者ロールを付与し、そのユーザーを Azure ディレクトリ閲覧者として設定する必要があります。
1. ユーザーを音声リソースの所有者として追加します。 [ユーザーを音声リソースに追加する方法](#add-users-to-a-speech-resource)を参照してください。
    :::image type="content" source="media/audio-content-creation/add-role.png" alt-text="ロールの所有者フィールド":::
2. [Azure portal](https://portal.azure.com/) で、左上にある折りたたまれたメニューを選択します。 **[Azure Active Directory]** をクリックして、 **[ユーザー]** をクリックします。
3. ユーザーの Microsoft アカウントを検索し、ユーザーの詳細ページに移動します。 **[割り当てられたロール]** をクリックします。
4. **[割り当ての追加]**  ->  **[ディレクトリ閲覧者]** をクリックします。 [割り当ての追加] ボタンがグレー表示されている場合は、アクセス権がないことを意味します。 ユーザーに割り当てを追加できるのは、このディレクトリの全体管理者のみです。

## <a name="see-also"></a>関連項目

* [Long Audio API](./long-audio-api.md)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
