---
title: Speaker Recognition の概要 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speaker Recognition は、音声生物測定学を使用して、固有の音声特性で話者を確認および識別するアルゴリズムを提供します。 Speaker Recognition は、"だれが話しているのか" という質問に回答するために使用されます。 この記事は、Speaker Recognition サービスの利点と機能の概要です。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
keywords: Speaker Recognition, 音声生物測定学
ms.openlocfilehash: 44772137d9b9e3366a2a04fded34fa81dafe8886
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058390"
---
# <a name="what-is-speaker-recognition"></a>Speaker Recognition とは

Speaker Recognition は、オーディオ クリップでだれが話しているのかを判断するために使用されます。 このサービスでは、音声生物測定学を使用して、独自の音声特性によって話者を確認および識別できます。 1 人の話者のオーディオ トレーニング データを提供すると、話者の声の固有の特性に基づいて登録プロファイルが作成されます。 次に、このプロファイルに対してオーディオ音声サンプルをクロスチェックして、話者が同じ人物であることを確認します (話者認証)。または、登録されている話者プロファイルの "*グループ*" に対してオーディオ音声サンプルをクロスチェックして、グループ内の任意のプロファイルと一致するかどうかを確認します (話者識別)。

> [!IMPORTANT]
> Microsoft では、Speaker Recognition へのアクセスを制限しています。 [Azure Cognitive Services Speaker Recognition 制限付きアクセス レビュー](https://aka.ms/azure-speaker-recognition)を通じて使用する場合に該当します。 承認後、Speaker Recognition API にアクセスできます。 詳細については、[Speaker Recognition の制限付きアクセス](/legal/cognitive-services/speech-service/speaker-recognition/limited-access-speaker-recognition)に関するページを参照してください。

## <a name="speaker-verification"></a>話者認証

話者認証は、パスフレーズまたは自由形式の音声入力のいずれかを使用して、登録済みの話者の ID を確認するプロセスを合理化します。 この機能を使用すると、コール センターでの顧客 ID の確認から施設への非接触型アクセスまで、さまざまなソリューションで安全かつスムーズな顧客エンゲージメントのために個人を検証することができます。

### <a name="how-does-speaker-verification-work"></a>話者認証のしくみ

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="話者認証フローチャート。":::

話者認証には、テキストに依存する種類とテキストに依存しない種類があります。 **テキストに依存する** 検証は、登録フェーズと検証フェーズの両方で、使用する同じパスフレーズを話者が選択する必要があることを意味します。 **テキストに依存しない** 検証は、話者が登録および検証のフレーズで日常的な言語で話すことができることを意味します。

**テキストに依存する** 検証の場合、事前定義されたフレーズのセットに含まれるパスフレーズを話すことによって話者の声が登録されます。 声の特徴がオーディオ録音から抽出され一意の音声シグネチャが形成されます。一方で、選択したパスフレーズも認識されます。 音声シグネチャとパスフレーズを一緒に使用して、話者を確認します。 

**テキストに依存しない** 検証では、登録時の初期アクティブ化フレーズの他には、登録時の話者の発言内容に関する制限はありません。 確認する音声のサンプルに制限はありません。これは、類似性をスコア付けする音声の特徴だけを抽出するためです。 

API の目的は、オーディオがライブ ユーザーの声か、声をまねたものか、登録済み話者の録音かを判断することではありません。 

## <a name="speaker-identification"></a>話者識別

話者識別は、登録されている話者のグループ内で不明な話者の ID を判断するために使用されます。 話者識別により、音声を個々の話者に帰属させ、次のような複数の話者がいるシナリオから価値を引き出すことができます。

* リモート ミーティングの生産性向上のためのソリューションをサポートする 
* 複数ユーザーのデバイスのパーソナル化を構築する

### <a name="how-does-speaker-identification-work"></a>話者識別のしくみ

話者識別の登録は **テキストに依存しません**。つまり、オーディオ内で話者が話す内容には、登録時の初期アクティブ化フレーズを除いて制限はありません。 話者認証と同様に、オーディオ登録フェーズでは、話者の声が記録され、声の特徴が抽出されて一意の音声シグネチャが形成されます。 識別フェーズでは、入力音声のサンプルが、指定された登録済みの音声のリスト (各要求で最大 50) と比較されます。

## <a name="data-security-and-privacy"></a>データのセキュリティとプライバシー

登録用の音声オーディオや音声シグネチャの特徴を含む話者登録データは、セキュリティで保護されたシステムに格納されます。 登録用の音声オーディオは、アルゴリズムがアップグレードされたときにのみ使用されます。その際は、特徴を再度抽出する必要があります。 認識フェーズ中にサービスに送信される音声録音や抽出された声の特徴は、サービスに保持されません。 

データが保持される期間は開発者が制御します。 開発者は、API 呼び出しによって個々の話者の登録データを作成、更新、および削除できます。 サブスクリプションが削除されると、そのサブスクリプションに関連付けられているすべての話者の登録データも削除されます。 

Cognitive Services リソース全般に言えることですが、話者認識サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 開発者は、話者認識用の適切なアクセス許可が、ユーザーから付与されていることを確認する必要があります。 詳細については、「[Speaker Recognition のデータとプライバシー](/legal/cognitive-services/speech-service/speaker-recognition/data-privacy-speaker-recognition)」を参照してください。 詳細については、Microsoft セキュリティ センターの  [Cognitive Services のページ](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) を参照してください。 

## <a name="common-questions-and-solutions"></a>一般的な質問と解決策

| Question | 解決策 |
|---------|----------|
| Speaker Recognition はどのようなシナリオで使用できますか。 | コール センターでの顧客認証、音声ベースの患者チェックイン、会議の文字起こし、マルチユーザー デバイスのパーソナル化|
| 識別と認証の違いは何ですか。 | 識別とは、話者のグループの内、どのメンバーが話しているかを検出するプロセスです。 認証では、話者が既知の、または **登録されている** 音声と一致することを確認します。|
| テキスト依存の認証およびテキストに依存しない認証の違いとは。 | テキスト依存の認証では、登録と認識の両方に特定のパスフレーズが必要です。 テキストに依存しない検証を行うには、登録のために特定のアクティブ化フレーズで始まるより長い音声サンプルが必要ですが、認識中を含め、話す内容は問われません。|
| どの言語がサポートされていますか。 | 英語、フランス語、スペイン語、中国語、ドイツ語、イタリア語、日本語、ポルトガル語 |
| どの Azure リージョンがサポートされていますか。 | Speaker Recognition はプレビュー サービスであり、現在は米国西部リージョンでのみご利用いただけます。|
| サポートされているオーディオ形式は何ですか。 | Mono 16 ビット、16kHz PCM エンコード済み WAV |
| **Accept** と **Reject** 応答が正確ではない場合、しきい値はどのように調整しますか。 | 最適なしきい値はシナリオによって大きく変化するため、API は、単純に既定のしきい値である 0.5 に基づいて、"Accept" または "Reject" を決定します。 上級ユーザーは、既定の決定を上書きし、実際のシナリオに基づいて結果を微調整することをお勧めします。 |
| 1 人の話者を複数回登録できますか。 | はい。テキスト依存の認証では、話者を最大 50 回登録できます。 テキストに依存しない認証、または話者識別では、最大 300 秒の音声を登録できます。 |
| Azure に格納されるデータは何ですか。 | 登録音声は、音声プロファイルが[削除](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments)されるまでサービスに格納されます。 認識音声サンプルは保持または保存されません。 |

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> * アプリケーションで使用できる一般的なデザイン パターンの実行に関する、Speaker Recognition の[基本の記事](./get-started-speaker-recognition.md)を完了してください。
> * テキストに依存しない話者認証については、[ビデオ チュートリアル](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/)を参照してください。

## <a name="responsible-use-of-ai"></a>AI の責任ある使用
> [!div class="ResponsibleUseOfAI"]
> * [Speaker Recognition の透明性に関するメモ](/legal/cognitive-services/speech-service/speaker-recognition/transparency-note-speaker-recognition)
> * [Speaker Recognition の特性と制限](/legal/cognitive-services/speech-service/speaker-recognition/characteristics-and-limitations-speaker-recognition)
> * [Speaker Recognition の制限付きアクセス](/legal/cognitive-services/speech-service/speaker-recognition/limited-access-speaker-recognition)
> * [Speaker Recognition との統合と責任ある使用に関するガイダンス](/legal/cognitive-services/speech-service/speaker-recognition/guidance-integration-responsible-use-speaker-recognition)
> * [Speaker Recognition のデータとプライバシー](/legal/cognitive-services/speech-service/speaker-recognition/data-privacy-speaker-recognition)
