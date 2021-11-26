---
title: Azure Video Analyzer for Media (旧称 Video Indexer) リリース ノート | Microsoft Docs
description: 常に最新の開発情報を把握していただけるよう、この記事では Azure Video Analyzer for Media (旧称 Video Indexer) の最新の更新情報を提供します。
ms.topic: article
ms.custom: references_regions
ms.date: 08/01/2021
ms.author: juliako
ms.openlocfilehash: 7341112e545e4fc0c74de8d32e9c2d54d3461057
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491923"
---
# <a name="video-analyzer-for-media-release-notes"></a>Video Analyzer for Media リリース ノート

>この URL (`https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us`) をコピーして、お使いの RSS フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

常に Azure Video Analyzer for Media (旧称 Video Indexer) の最新の開発情報を把握していただけるよう、この記事では次の事項に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

## <a name="november-2021"></a>2021 年 11 月
 
### <a name="public-preview-of-azure-video-analyzer-for-media-account-management-based-on-arm"></a>Azure Video Analyzer for Media での ARM ベースのアカウント管理のパブリック プレビュー

Azure Video Analyzer for Media では、Azure Resource Manager (ARM) ベースのアカウント管理のパブリック プレビューが導入されています。 ARM ベースの API を活用して、Azure portal からアカウントを作成、編集、削除することができます。

詳細については、[Video Analyzer for Media アカウントの作成](https://techcommunity.microsoft.com/t5/azure-ai/azure-video-analyzer-for-media-is-now-available-as-an-azure/ba-p/2912422)に関する記事を参照してください。

### <a name="peoples-clothing-detection"></a>人物の衣服の検出

詳細なビデオ設定を使用してビデオにインデックスを付ける際に、新しい **人物の衣服の検出** 機能を表示できます。 メディア ファイルで人物が検出された場合、Media Player によって、それらの人物が来ている衣服の種類を表示できるようになりました。 

## <a name="october-2021"></a>2021 年 10 月

### <a name="embed-widgets-in-your-app-using-azure-video-analyzer-for-media-package"></a>Azure Video Analyzer for Media パッケージを使ってアプリにウィジェットを埋め込む

新しい Azure Video Analyzer for Media (AVAM) `@azure/video-analyzer-for-media-widgets` npm パッケージを使って、アプリに `insights` ウィジェットを追加し、ニーズに合わせてカスタマイズします。

新しい AVAM パッケージを使うと、`iframe` 要素を追加してコグニティブな分析情報ウィジェットを埋め込むのではなく、ウィジェットとアプリを簡単に埋め込んで通信できます。 詳細については、[アプリへの Video Analyzer for Media ウィジェットの埋め込みとカスタマイズ](https://techcommunity.microsoft.com/t5/azure-media-services/embed-and-customize-azure-video-analyzer-for-media-widgets-in/ba-p/2847063)に関するドキュメントを参照してください。 

## <a name="august-2021"></a>2021 年 8 月

### <a name="re-index-video-or-audio-files"></a>ビデオまたはオーディオ ファイルのインデックスの再作成

インデックス作成プロセス中に失敗したビデオまたはオーディオ ファイルのインデックスを再作成するオプションが追加されました。

### <a name="improve-accessibility-support"></a>アクセシビリティ サポートの向上

CSS、テーマ、およびアクセシビリティに関連するバグを修正しました。

* high contrast
* [ポータル](https://www.videoindexer.ai)のアカウント設定と分析情報ビュー。  

## <a name="july-2021"></a>2021 年 7 月

### <a name="automatic-scaling-of-media-reserved-units"></a>メディア占有ユニットの自動スケーリング
 
2021 年 8 月 1 日以降、Azure Video Analyzer for Media (旧称 Video Indexer) では、[Azure Media Services](../../media-services/latest/media-services-overview.md) による[メディア占有ユニット (MRUs)](../../media-services/latest/concept-media-reserved-units.md) の自動スケーリングが有効になりました。その結果、Azure Video Analyzer for Media を使用してメディアを管理する必要がなくなります。 これにより、自動スケーリングされるビジネス ニーズに応じて、多くの場合では価格を下げることができるなど、価格の最適化が可能になります。

## <a name="june-2021"></a>2021 年 6 月
 
### <a name="video-analyzer-for-media-deployed-in-six-new-regions"></a>6 つの新しいリージョンへの Video Analyzer for Media のデプロイ
 
フランス中部、米国中部、ブラジル南部、米国中西部、韓国中部、および西日本リージョンで、Video Analyzer for Media の有料アカウントを作成できるようになりました。
  
## <a name="may-2021"></a>2021 年 5 月

### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>音声テキスト変換 (STT)、翻訳、検索に対する新しいソース言語のサポート

Video Analyzer for Media では、中国語 (広東語) ("zh-HK")、オランダ語 (オランダ) ("Nl-NL")、チェコ語 ("Cs-CZ")、ポーランド語 ("Pl-PL")、スウェーデン語 (スウェーデン) ("Sv-SE")、ノルウェー語 ("nb-NO")、フィンランド語 ("fi-FI")、カナダ フランス語 ("fr-CA')、タイ語 ("th-TH")、アラビア語: (アラブ首長国連邦) ("ar-AE"、"ar-EG")、(イラク) ("ar-IQ")、(ヨルダン) ("ar-JO")、(クウェート) ("ar-KW")、(レバノン) ("ar-LB")、(オマーン) ("ar-OM")、(カタール) ("ar-QA")、(パレスチナ自治政府) ("ar-PS")、(シリア) ("ar-SY")、トルコ語 ("tr-TR") で STT、翻訳、検索がサポートされるようになりました。 

これらの言語は、API と Video Analyzer for Media Web サイトの両方で使用できます。 **[ビデオのソース言語]** の下にあるコンボ ボックスから言語を選択してください。

### <a name="new-theme-for-azure-video-analyzer-for-media"></a>Azure Video Analyzer for Media の新しいテーマ

新しいテーマ ('ライト' および 'ダーク' のテーマとともに 'Azure') を使用できます。 テーマを選択するには、Web サイトの右上隅にある歯車アイコンをクリックし、 **[ユーザー設定]** の下でテーマを見つけます。
 
### <a name="new-open-source-code-you-can-leverage"></a>利用できる新しいオープンソース コード 

[GitHub リポジトリ](https://github.com/Azure-Samples/media-services-video-indexer)で、新しい 3つの GitHub プロジェクトを利用できます。

* 新しく追加された[ウィジェットのカスタマイズ](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets)を活用するためのコード。
* ビデオ ライブラリに[カスタム検索](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/VideoSearchWithAutoMLVision)を追加するためのソリューション。
* ビデオ ライブラリに[重複除去](https://github.com/Azure-Samples/media-services-video-indexer/commit/6b828f598f5bf61ce1b6dbcbea9e8b87ba11c7b1)を追加するためのソリューション。
 
### <a name="new-option-to-toggle-bounding-boxes-for-observed-people-on-the-player"></a>プレーヤーで (観察対象人物の) 境界ボックスを切り替える新しいオプション  

詳細なビデオ設定を使用してビデオにインデックスを付ける際に、観察対象人物の新たな機能を表示できます。 メディア ファイルで検出された人物がいる場合、Media Player を通じて検出された人物の境界ボックスを有効にすることができます。

## <a name="april-2021"></a>2021 年 4 月

Video Indexer サービスの名前が、Azure Video Analyzer for Media に変更されました。

### <a name="improved-upload-experience-in-the-portal"></a>ポータルでのアップロード エクスペリエンスの向上
 
[ポータル](https://www.videoindexer.ai)で利用できる、Video Analyzer for Media のアップロード機能が新しくなりました。 メディア ファイルをアップロードするには、 **[メディア ファイル]** タブから **[アップロード]** ボタンを押します。

### <a name="new-developer-portal-in-available-in-gov-cloud"></a>gov-cloud で利用できる新しい開発者ポータル
 
[Video Analyzer for Media の開発者ポータル](https://api-portal.videoindexer.ai)が、米国政府向け Azure でも利用できるようになりました。

### <a name="observed-people-tracing-preview"></a>観察対象人物追跡 (プレビュー)  

Azure Video Analyzer for Media を使用して、ビデオ内の観察対象人物を検出し、その人物のビデオ フレーム内の位置、人物が現れたときの正確なタイムスタンプ (開始、終了) などの情報が得られるようになりました。 この API では、検出された各人物インスタンスの境界ボックス座標 (ピクセル単位) が、その信頼度を含めて返されます。 

たとえば、ある人物がビデオに映っている場合、検出操作を行うと、その人物の姿形がビデオ フレーム内の座標と共に一覧表示されます。 この機能を使用して、ビデオ内でその人物の足取りを判別できます。 ビデオ内に同一人物のインスタンスが複数存在するかどうかも判別できます。 

新たに追加された観察対象人物追跡機能は、ファイルにインデックスを付けるときに **[詳細オプション]**  ->  **[Advanced video]\(詳細ビデオ\)** または **[Advanced video + audio]\(詳細ビデオ + オーディオ\)** プリセット ([動画 + オーディオのインデックス作成] の下) を選択して使用できます。 標準および基本のインデックス作成プリセットには、この新しい詳細モデルは含まれません。 

Video Analyzer for Media Web サイトでビデオの分析情報の表示を選択すると、ページに観察対象人物追跡が表示され、検出されたすべての人物のサムネイルが示されます。 人物のサムネイルを選択し、その人物が映っている場面をビデオ プレーヤーで確認できます。  

この機能は、Video Analyzer for Media によって生成される JSON ファイルでも使用できます。 詳細については、「[Trace observed people in a video](observed-people-tracing.md)」\(ビデオ内で確認されたユーザーを追跡する\)を参照してください。

### <a name="detected-acoustic-events-with-audio-effects-detection-preview"></a>**オーディオ エフェクト検出** (プレビュー) を使用して検出された音響イベント

検出された音響イベントをクローズド キャプション ファイルで確認できるようになりました。 このファイルは、Video Analyzer for Media からダウンロードでき、GetArtifact API の成果物として使用できます。

**オーディオ エフェクト検出** (プレビュー) コンポーネントを使用して、さまざまな音響イベントを検出し、それらをさまざまな音響カテゴリ (発砲、叫び声、観客の反応など) に分類します。 詳細については、「[オーディオ効果の検出](audio-effects-detection.md)」を参照してください。

## <a name="march-2021"></a>2021 年 3 月

### <a name="audio-analysis"></a>音声分析 

音声分析が、さまざまな価格で新たに追加された音声機能のバンドルで利用できるようになりました。 新しい **基本音声** 分析プリセットは、音声の文字起こしの抽出、出力キャプションと字幕の書式設定のみを備えた低コストのオプションです。 **基本音声** プリセットからは、2 つの別個の測定 (文字起こし分、およびキャプションと字幕の書式設定分) が生成されて請求書に計上されます。 価格の詳細については、[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)に関する記事を参照してください。

新たに追加されたバンドルは、 **[詳細設定] オプション** ->  **[Basic Audio]\(基本音声\)** プリセット ( **[Video + audio indexing]\(ビデオ + 音声インデックス作成\)** のドロップダウン ボックス) を選択して、ファイルのインデックスを作成または再作成したときに使用できます。

### <a name="new-developer-portal"></a>新しい開発者ポータル 

Video Analyzer for Media の[開発者ポータル](https://api-portal.videoindexer.ai/)が新しくなりました。新しい Video Analyzer for Media API をお試しください。[GitHub リポジトリ](https://github.com/Azure-Samples/media-services-video-indexer)、[スタック オーバーフロー](https://stackoverflow.com/questions/tagged/video-indexer)、関連するブログ記事が含まれた [Video Analyzer for Media の技術コミュニティ](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer)、[Video Analyzer for Media の FAQ](faq.yml)、フィードバックを提供し、機能を提案するための[ユーザーの声](https://feedback.azure.com/d365community/forum/09041fae-0b25-ec11-b6e6-000d3a4f0858)、およびウィジェット コード サンプルが含まれた ["CodePen" リンク](https://codepen.io/videoindexer)など、関連するすべてのリソースが 1 か所で見つかります。 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>分析情報ウィジェットの高度なカスタマイズ機能 

Video Analyzer for Media の分析情報ウィジェットを独自のサービスに埋め込んで、そのスタイルとデータをカスタマイズするための SDK が利用できるようになりました。 SDK では、標準の Video Analyzer for Media 分析情報ウィジェットと、完全にカスタマイズ可能な分析情報ウィジェットがサポートされています。 コード サンプルは、[Video Analyzer for Media の GitHub リポジトリ](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization)で入手できます。 この高度なカスタマイズ機能を使用すると、ソリューション開発者はカスタム スタイル設定を適用し、(Video Analyzer for Media の分析情報の有無に関係なく) 顧客独自の AI データを取り込んで分析情報ウィジェットに表示できます。 

### <a name="video-analyzer-for-media-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Video Analyzer for Media が米国中北部、米国西部、カナダ中部にデプロイされました 

米国中北部、米国西部、カナダ中部のリージョンで Video Analyzer for Media の有料アカウントを作成できるようになりました。
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>音声テキスト変換 (STT)、翻訳、検索に対する新しいソース言語のサポート 

Video Analyzer for Media では、デンマーク語 ("da-DK")、ノルウェー語 ("nb-NO")、スウェーデン語 ("sv-SE")、フィンランド語 ("fi-FI")、カナダ フランス語 ("fr-CA")、タイ語 ("th-TH")、アラビア語 ("ar-BH"、"ar-EG"、"ar-IQ"、"ar-JO"、"ar-KW"、"ar-LB"、"ar-OM"、"ar-QA"、"ar-S"、"ar-SY")、およびトルコ語 ("tr-TR") で、STT、翻訳、検索がサポートされるようになりました。 これらの言語は、API と Video Analyzer for Media Web サイトの両方で使用できます。 
 
### <a name="search-by-topic-in-video-analyzer-for-media-website"></a>Video Analyzer for Media Web サイトでトピックによって検索する 

[Video Analyzer for Media Web サイト](https://www.videoindexer.ai/account/login) ページの上部にある検索機能を使用して、特定のトピックでビデオを検索できるようになりました。 

## <a name="february-2021"></a>2021 年 2 月

### <a name="multiple-account-owners"></a>複数アカウントの所有者 

アカウント所有者ロールが Video Analyzer for Media に追加されました。 ユーザーの追加、変更、削除を行ったり、それらのロールを変更できます。 アカウントを共有する方法の詳細については、[ユーザーの招待](invite-users.md)に関するページを参照してください。

### <a name="audio-event-detection-public-preview"></a>音声イベントの検出 (パブリック プレビュー)

> [!NOTE]
> この機能は試用版アカウントでのみ使用できます。 

Video Analyzer for Media では、銃声、ガラスの割れる音、アラーム、サイレン、爆発音、犬が吠える声、叫び声、笑い声、観客の反応 (声援、拍手、ブーイング)、静寂など、声以外の音声効果を検出できるようになりました。 

新たに追加された音声効果機能は、 **[詳細設定] オプション** ->  **[高度なオーディオ]** プリセット ([Video + audio indexing]\(ビデオ + 音声インデックス作成\)) を選択してファイルのインデックスを作成したときに使用できます。 標準のインデックス作成には、**静寂** と **観客の反応** だけが含まれます。 

以前の音声効果モデルに含まれていた **拍手** イベント タイプは、**観客の反応** イベント タイプの一部として抽出されるようになりました。

[Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトでビデオの **分析情報** を表示するように選択すると、音声効果がページに表示されます。

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="音声イベントの検出":::

### <a name="named-entities-enhancement"></a>名前付きエンティティの機能強化  

抽出された人物と場所の一覧は、全体的に拡張および更新されています。 

また、モデルには、ビデオの "Sam" や "Home" のような、状況に応じた、有名ではない人物や場所が含まれるようになりました。 

## <a name="january-2021"></a>2021 年 1 月

### <a name="video-analyzer-for-media-is-deployed-on-us-government-cloud"></a>Video Analyzer for Media が米国政府のクラウドにデプロイ済み 

Video Analyzer for Media 有料アカウントをバージニアおよびアリゾナのリージョンにある米国政府のクラウドで作成できるようになりました。 Video Analyzer for Media 無料試用版オファリングは、記載のリージョンでは利用できません。 詳細については、Video Analyzer for Media のドキュメントを参照してください。 

### <a name="video-analyzer-for-media-deployed-in-the-india-central-region"></a>Video Analyzer for Media がインド中部リージョンにデプロイ済み 

Video Analyzer for Media 有料アカウントをインド中部リージョンで作成できるようになりました。 

### <a name="new-dark-mode-for-the-video-analyzer-for-media-website-experience"></a>Video Analyzer for Media Web サイト エクスペリエンス向けの新しいダーク モード

Video Analyzer for Media Web サイト エクスペリエンスがダーク モードで利用できるようになりました。 ダーク モードを有効にするには、設定パネルを開き、 **[ダーク モード]** オプションのトグルをオンにします。 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="ダーク モードの設定":::

## <a name="december-2020"></a>2020 年 12 月

### <a name="video-analyzer-for-media-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Analyzer for Media がスイス西部とスイス北部にデプロイ済み

Video Analyzer for Media 有料アカウントをスイス西部とスイス北部のリージョンで作成できるようになりました。

## <a name="october-2020"></a>2020 年 10 月

### <a name="animated-character-identification-improvements"></a>アニメーション化されたキャラクターの識別の向上  

Video Analyzer for Media では、Cognitive Services の Custom Vision との統合によって、アニメーション化されたコンテンツのキャラクターの検出、グループ化、認識がサポートされます。 検出とキャラクターの認識におけるこの AI アルゴリズムに大きな改良を加えた結果、分析情報の正確性と識別されるキャラクターが大幅に改善されました。

### <a name="planned-video-analyzer-for-media-website-authenticatication-changes"></a>Video Analyzer for Media Web サイトの認証に予定されている変更

2021 年 3 月 1 日以降、Facebook または LinkedIn を使用して [Video Analyzer for Media Web サイト](https://www.videoindexer.ai/) [開発者ポータル](video-indexer-use-apis.md)にサインアップとサインインすることができなくなります。

これらのいずれかのプロバイダーを使用して、サインアップとサインインができるようになります。Azure AD、Microsoft、Google。

> [!NOTE]
> LinkedIn と Facebook に接続されている Video Analyzer for Media アカウントには、2021 年 3 月 1 日以降アクセスできなくなります。 
> 
> 引き続きアクセスできるよう、所有している Azure AD、Microsoft、または Google メール アドレスを Video Analyzer for Media アカウントに[招待](invite-users.md)してください。 [招待](invite-users.md)に関するページの説明に従って、サポートされているプロバイダーの追加の所有者を加えることができます。 <br/>
> あるいは、有料アカウントを作成し、データを移行できます。

## <a name="august-2020"></a>2020 年 8 月

### <a name="mobile-design-for-the-video-analyzer-for-media-website"></a>Video Analyzer for Media Web サイトのモバイル設計

Video Analyzer for Media Web サイトのエクスペリエンスで、モバイル デバイスがサポートされるようになりました。 ユーザー エクスペリエンスは、モバイル画面のサイズに合わせて最適化されます (カスタマイズ UI は除く)。 

### <a name="accessibility-improvements-and-bug-fixes"></a>アクセシビリティの向上とバグ修正 

WCAG (Web Content Accessibility Guidelines) の一部として、および Microsoft アクセシビリティ標準の一部として、Video Analyzer for Media Web サイトのエクスペリエンスがレベル C に揃えられました。 キーボード ナビゲーション、プログラムによるアクセス、スクリーン リーダーに関連するいくつかのバグ修正および改善が行われました。 

## <a name="july-2020"></a>2020 年 7 月

### <a name="ga-for-multi-language-identification"></a>GA になった複数言語の識別

複数言語の識別はプレビューから GA に移行され、運用環境で使用する準備ができています。

"プレビューから GA" への移行に関連する価格への影響はありません。

### <a name="video-analyzer-for-media-website-improvements"></a>Video Analyzer for Media Web サイトの向上

#### <a name="adjustments-in-the-video-gallery"></a>ビデオ ギャラリーでの調整

詳細な分析情報の検索用に、追加のフィルター機能を備えた新しい検索バーが追加されました。 検索結果も拡張されています。

複数のファイルを含むビデオ アーカイブの並べ替えと管理を行う機能を備えた新しいリスト ビュー。

#### <a name="new-panel-for-easy-selection-and-configuration"></a>選択と構成が容易な新しいパネル

選択とユーザーの構成が容易なサイド パネルが追加され、シンプルで迅速なアカウントの作成と共有や、構成の設定が可能になっています。

サイド パネルは、ユーザー設定やヘルプにも使用されます。

## <a name="june-2020"></a>2020 年 6 月

### <a name="search-by-topics"></a>トピックで検索

検索 API を使用して、特定のトピックでビデオを検索できるようになりました (API のみ)。

トピックは、`textScope` (省略可能なパラメーター) の一部として追加されます。 詳細については [API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos) を参照してください。  

### <a name="labels-enhancement"></a>ラベルの機能強化

ラベルのタグ付け機能がアップグレードされました。これにより、特定しやすいより視覚的なラベルが追加されます。

## <a name="may-2020"></a>2020 年 5 月

### <a name="video-analyzer-for-media-deployed-in-the-east-us"></a>Video Analyzer for Media が米国東部にデプロイ済み

Video Analyzer for Media 有料アカウントを米国東部リージョンで作成できるようになりました。
 
### <a name="video-analyzer-for-media-url"></a>Video Analyzer for Media の URL

Video Analyzer for Media のリージョン エンドポイントはすべて、www でのみ始まるように統一されました。 必要な操作はありません。

今後は、ウィジェットを埋め込む場合も、Video Analyzer for Media Web アプリケーションにログインする場合も www.videoindexer.ai にアクセスします。

また、wus.videoindexer.ai も www にリダイレクトされます。 詳細については、「[アプリに Video Analyzer for Media ウィジェットを埋め込む](video-indexer-embed-widgets.md)」を参照してください。

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-widget-parameters-capabilities"></a>新しいウィジェット パラメーターの機能

**Insights** ウィジェットには、`language` と `control` という新しいパラメーターが含まれています。

**プレーヤー** ウィジェットには、新しい `locale` パラメーターがあります。 `locale` と `language` パラメーターは両方とも、プレーヤーの言語を制御します。

詳細については、「[ウィジェットの種類](video-indexer-embed-widgets.md#widget-types)」セクションを参照してください。 

### <a name="new-player-skin"></a>新しいプレーヤー スキン

新しいプレーヤー スキンが、更新されたデザインで利用可能になりました。

### <a name="prepare-for-upcoming-changes"></a>今後予定されている変更の準備をする

* 現在、次の API からアカウント オブジェクトが返されます。

    * [Create-Paid-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Get-Account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Get-Accounts-With-Token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    アカウント オブジェクトには、[Video Analyzer for Media Web サイト](https://www.videoindexer.ai/)の場所をポイントする `Url` フィールドがあります。
有料アカウントの場合、`Url` フィールドは現在、パブリック Web サイトではなく、内部 URL をポイントしています。
数週間のうちに、Microsoft はこれを変更し、すべてのアカウント (試用版および有料版) に [Video Analyzer for Media Web サイト](https://www.videoindexer.ai/)の URL を返すようになります。

    内部 URL を使用しないでください。[Video Analyzer for Media パブリック API](https://api-portal.videoindexer.ai/) を使用する必要があります。
* Video Analyzer for Media URL をアプリケーションに埋め込んでいて、URL が [Video Analyzer for Media Web サイト](https://www.videoindexer.ai/)も Video Analyzer for Media API エンドポイント (`https://api.videoindexer.ai`) もポイントしておらず、リージョン エンドポイント (`https://wus2.videoindexer.ai` など) をポイントしている場合は、URL を再生成してください。

   次のいずれかでこれを行うことができます。

    * URL を Video Analyzer for Media ウィジェット API をポイントする URL (たとえば、[Insights ウィジェット](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget)) に置き換える
    * Video Analyzer for Media Web サイトを使用して新しい埋め込み URL を生成する
         
         **[再生]** を押してビデオのページに移動し、 **&lt;/&gt; [埋め込み]** ボタンをクリックして、その URL をご利用のアプリケーションにコピーします。
   
    リージョンの URL はサポートされておらず、今後数週間のうちにブロックされます。

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="custom-language-support-for-additional-languages"></a>追加言語に対するカスタム言語サポート

Video Analyzer for Media で、`ar-SY`、`en-UK`、`en-AU` のカスタム言語モデルがサポートされるようになりました (API のみ)。
 
### <a name="delete-account-timeframe-action-update"></a>アカウント削除アクションのタイムフレームの更新

アカウント削除アクションで、48 時間ではなく 90 日以内にアカウントが削除されるようになりました。
 
### <a name="new-video-analyzer-for-media-github-repository"></a>新しい Video Analyzer for Media GitHub リポジトリ

さまざまなプロジェクトを含む新しい Video Analyzer for Media GitHub、ファースト ステップ ガイド、およびコード サンプルを使用できるようになりました。 https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger の更新

Video Analyzer for Mediaで、**認証** と **操作** が単一の [Video Analyzer for Mediad OpenAPI 仕様 (swagger)](https://api-portal.videoindexer.ai/api-details#api=Operations&operation) に統合されました。 開発者は、[Video Analyzer for Media 開発者ポータル](https://api-portal.videoindexer.ai/)で API を見つけることができます。

## <a name="december-2019"></a>2019 年 12 月

### <a name="update-transcript-with-the-new-api"></a>新しい API を使用して音声テキストを更新する

[Update-Video-Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) API を使用して、音声テキストの特定のセクションを更新します。

### <a name="fix-account-configuration-from-the-video-analyzer-for-media-portal"></a>Video Analyzer for Media ポータルからアカウント構成を修正する

次のような問題を解決するために、Media Services 接続構成を更新できるようになりました。 

* Azure Media Services のリソースが正しくない
* パスワードの変更
* Media Services リソースがサブスクリプション間で移動された  

アカウント構成を修正するには、Video Analyzer for Media ポータルで、(所有者として) [設定] > [アカウント] タブの順に移動します。

### <a name="configure-the-custom-vision-account"></a>Custom Vision アカウントを構成する

Video Analyzer for Media ポータルを使用して、有料アカウントで Custom Vision アカウントを構成します (以前は API でのみサポートされていました)。 これを行うには、Video Analyzer for Media ポータルにサインインし、[モデルのカスタマイズ] > [アニメーション化されたキャラクター] > [構成] の順に選択します。 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>シーン、ショット、キーフレーム - 1 つの分析情報ウィンドウに表示されるようになりました

シーン、ショット、およびキーフレームは、使用とナビゲーションを容易にするために 1 つの分析情報に結合されるようになりました。 目的のシーンを選択すると、そこに含まれるショットとキーフレームが表示されます。 

### <a name="notification-about-a-long-video-name"></a>長いビデオ名に関する通知

ビデオ名が 80 文字より長い場合、Video Analyzer for Media にはアップロード時に状況を説明するエラーが表示されます。

### <a name="streaming-endpoint-is-disabled-notification"></a>ストリーミング エンドポイントが無効になっているという通知

ストリーミング エンドポイントが無効になっている場合、Video Analyzer for Media のプレーヤー ページに状況を説明するエラーが表示されます。

### <a name="error-handling-improvement"></a>エラー処理の改善

現在のインデックスの再作成の変更が誤って上書きされるのを防ぐために、ビデオにアクティブにインデックスが作成された場合は、[ビデオのインデックスを再作成](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) API と[ビデオ インデックス](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) API から状態コード 409 が返されるようになりました。

## <a name="november-2019"></a>2019 年 11 月
 
* 韓国語のカスタム言語モデルのサポート

    Video Analyzer for Media では、API とポータルの両方で韓国語 (`ko-KR`) のカスタム言語モデルがサポートされるようになりました。 
* 音声テキスト変換 (STT) でサポートされる新しい言語

    Video Analyzer for Media API では、アラビア語 (レバント) (ar-SY)、英語 (英国) 言語 (en-GB)、英語 (オーストラリア) 言語 (en-AU) の STT がサポートされるようになりました。
    
    動画のアップロードでは、zh-HANS が zh-CN に置き換えられています。どちらもサポートされていますが、zh-CN の方がより正確であるため、推奨されています。
    
## <a name="october-2019"></a>2019 年 10 月
 
* ギャラリーでアニメーション化された文字を検索する

    アニメーション化された文字のインデックスを作成すると、アカウントのビデオ ギャラリーで検索できるようになりました。 詳細については、[アニメーション化された文字の認識](animated-characters-recognition.md)に関する記事を参照してください。

## <a name="september-2019"></a>2019 年 9 月
 
IBC 2019 で発表されたさまざまな進化:
 
* アニメーション文字の認識 (パブリック プレビュー)

    Custom Vision との統合で、アニメーション コンテンツでグループを検出したり、文字を認識したりできるようになりました。 詳細については、「[アニメーション キャラクターの検出](animated-characters-recognition.md)」を参照してください。
* 複数言語の識別 (パブリック プレビュー)

    オーディオ トラックに含まれる複数の言語からセグメントを検出し、それに基づいて多言語のトランスクリプトを作成します。 初回サポート:英語、スペイン語、ドイツ語、フランス語。 詳細については、「[複数言語のコンテンツを自動的に識別および文字起こしする](multi-language-identification-transcription.md)」を参照してください。
* 人と場所に関して名前付きのエンティティを抽出する

    自然言語処理 (NLP) を使用して、音声および視覚テキストからブランド、場所、および人物を抽出します。
* 編集タイプでショットを分類する

    クローズアップ、ミディアムショット、ツーショット、屋内、屋外など、編集タイプでショットにタグを付けます。詳細については、「[編集ショット タイプの検出](scenes-shots-keyframes.md#editorial-shot-type-detection)」を参照してください。
* トピック推論の機能強化 - 現在のレベル 2
    
    トピック推論モデルでは、さらに細かな IPTC 分類に対応できるようになりました。 詳しくは、「[Azure Media Services の AI を活用した新たなイノベーション](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)」をお読みください。

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-analyzer-for-media-deployed-in-uk-south"></a>Video Analyzer for Media が英国南部にデプロイ済み

Video Analyzer for Media 有料アカウントを英国南部リージョンで作成できるようになりました。

### <a name="new-editorial-shot-type-insights-available"></a>利用可能になった新しい編集ショット タイプ分析情報

ビデオ ショットに追加された新しいタグにより、コンテンツ作成ワークフローで使用される次のような一般的な編集語句を識別するための "ショット タイプ" が提供されます: 超クローズアップ、クローズアップ、ワイド、中程度、2 ショット、屋外、室内、左の顔、右の顔 (JSON で使用可能)。

### <a name="new-people-and-locations-entities-extraction-available"></a>利用可能になった新しい人と場所のエンティティ抽出

Video Analyzer for Media では、ビデオの OCR と文字起こしからの自然言語処理 (NLP) により、名前付きの場所と人が識別されます。 Video Analyzer for Media では、機械学習アルゴリズムを使用して、特定の場所 (エッフェル塔など) や人 (John Doe など) などのビデオでの言及が認識されます。

### <a name="keyframes-extraction-in-native-resolution"></a>ネイティブ解像度でのキーフレームの抽出

Video Analyzer for Media によって抽出されたキーフレームを、ビデオの元の解像度で使用できます。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA になった画像からのカスタム顔モデルのトレーニング

画像からの顔のトレーニングが、プレビュー モードから GA になりました (API およびポータルで利用可能)。

> [!NOTE]
> "プレビューから GA" への移行に関連する価格への影響はありません。

### <a name="hide-gallery-toggle-option"></a>ギャラリー非表示の切り替えオプション

ユーザーは、ポータルからギャラリー タブを非表示にすることができます (サンプル タブの非表示と同様)。
 
### <a name="maximum-url-size-increased"></a>URL の最大サイズの拡大

ビデオのインデックス作成時にサポートされる URL クエリ文字列が (2048 文字ではなく) 4096 文字になります。
 
### <a name="support-for-multi-lingual-projects"></a>多言語プロジェクトのサポート

異なる言語でインデックス付けされた複数のビデオに基づいて、プロジェクトを作成できるようになりました (API のみ)。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>ウィジェットとしてのエディター

Video Analyzer for Media AI エディターが、お客様のアプリケーションに埋め込むためのウィジェットとして提供されています。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>ポータルでクローズド キャプション ファイルからカスタム言語モデルを更新する

お客様は、ポータルのカスタマイズ ページから、言語モデルの入力として VTT、SRT、TTML の各ファイル形式を指定できます。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-analyzer-for-media-deployed-to-japan-east"></a>Video Analyzer for Media が東日本にデプロイ済み

Video Analyzer for Media 有料アカウントを東日本リージョンで作成できるようになりました。

### <a name="create-and-repair-account-api-preview"></a>アカウントの作成および修復 API (プレビュー)

[Azure Media Service の接続エンドポイントまたはキーを更新](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)できる新しい API が追加されました。

### <a name="improve-error-handling-on-upload"></a>アップロードでのエラー処理の向上 

基になる Azure Media Services アカウントの構成に誤りがある場合には、説明のメッセージが返されます。

### <a name="player-timeline-keyframes-preview"></a>プレーヤー タイムラインのキーフレーム プレビュー 

プレーヤーのタイムライン上の各時間に対するイメージ プレビューを表示できるようになりました。

### <a name="editor-semi-select"></a>エディターの半選択

エディターで特定の分析情報期間を選択した結果として選択されるすべての分析情報のプレビューを表示できるようになりました。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>クローズド キャプション ファイルからのカスタム言語モデルの更新

[カスタム言語モデルの作成](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model)および[カスタム言語モデルの更新](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) API は、言語モデルの入力として、VTT、SRT、および TTML ファイル形式をサポートするようになりました。

[ビデオ トランスクリプトの更新 API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript) を呼び出すと、トランスクリプトが自動的に追加されます。 ビデオに関連付けられたトレーニング モデルも自動的に更新されます。 使用する言語モデルをカスタマイズおよびトレーニングする方法については、「[Video Analyzer for Media で言語モデルをカスタマイズする](customize-language-model-overview.md)」を参照してください。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新しいダウンロード トランスクリプト形式 – TXT と CSV

Video Analyzer for Media では、既にサポートされているクローズド キャプション形式 (SRT、VTT、TTML) に加えて、TXT および CSV 形式でのトランスクリプトのダウンロードがサポートされるようになりました。

## <a name="next-steps"></a>次のステップ

[概要](video-indexer-overview.md)
