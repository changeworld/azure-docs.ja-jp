---
title: Azure Speaker Recognition サービス
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Speaker Recognition は、固有の音声特性によって話者を確認および識別するアルゴリズムを提供します。 Speaker Recognition は、"だれが話しているのか" という質問に回答するために使用されます。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: trbye
ms.openlocfilehash: 2d4ce6f274efbd4d8afe2ac48856b0fc312f0a09
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261771"
---
# <a name="what-is-the-azure-speaker-recognition-service"></a>Azure Speaker Recognition サービスとは

Speaker Recognition サービスは、固有の音声特性によって話者を検証および識別するアルゴリズムを提供します。 Speaker Recognition は、"だれが話しているのか" という質問に回答するために使用されます。 1 人の話者のてオーディオ トレーニング データを提供すると、話者の声の固有の特性に基づいて登録プロファイルが作成されます。 次に、このプロファイルに対してオーディオ音声サンプルをクロスチェックして、話者が同じ人物であることを確認します (話者認証)。または、登録されている話者プロファイルの "*グループ*" に対してオーディオ音声サンプルをクロスチェックして、グループ内の任意のプロファイルと一致するかどうかを確認します (話者識別)。 これに対して、[話者ダイアライゼーション](batch-transcription.md#speaker-separation-diarization)では、オーディオのセグメントを話者ごとにバッチ操作でグループ化します。

## <a name="speaker-verification"></a>話者認証

話者認証は、パスフレーズまたは自由形式の音声入力のいずれかを使用して、登録済みの話者の ID を確認するプロセスを合理化します。 この機能を使用すると、コール センターでの顧客 ID の確認から施設への非接触型アクセスまで、さまざまなソリューションで安全かつスムーズな顧客エンゲージメントのために個人を検証することができます。

### <a name="how-does-speaker-verification-work"></a>話者認証のしくみ

![話者認証のしくみ](media/speaker-recognition/speaker-rec.png)

話者認証には、テキストに依存する種類とテキストに依存しない種類があります。 **テキストに依存する**検証は、登録フェーズと検証フェーズの両方で、使用する同じパスフレーズを話者が選択する必要があることを意味します。 **テキストに依存しない**検証は、話者が登録および検証のフレーズで日常的な言語で話すことができることを意味します。

**テキストに依存する**検証の場合、事前定義されたフレーズのセットに含まれるパスフレーズを話すことによって話者の声が登録されます。 声の特徴がオーディオ録音から抽出され一意の音声シグネチャが形成されます。一方で、選択したパスフレーズも認識されます。 音声シグネチャとパスフレーズを一緒に使用して、話者を確認します。 

**テキストに依存しない**検証では、類似性をスコアリングするためにのみ音声の特徴を抽出するため、登録時または検証対象のオーディオ サンプルで話者が話す内容についての制限はありません。 

API の目的は、オーディオがライブ ユーザーの声か、声をまねたものか、登録済み話者の録音かを判断することではありません。 

## <a name="speaker-identification"></a>話者識別

話者識別は、登録されている話者のグループ内で不明な話者の ID を判断するために使用されます。 話者識別により、音声を個々の話者に帰属させ、次のような複数の話者がいるシナリオから価値を引き出すことができます。

* リモート ミーティングの生産性向上のためのソリューションをサポートする 
* 複数ユーザーのデバイスのパーソナル化を構築する

### <a name="how-does-speaker-identification-work"></a>話者識別のしくみ

話者識別の登録は**テキストに依存しません**。つまり、オーディオ内で話者が話す内容に制限はありません。 話者認証と同様に、オーディオ登録フェーズでは、話者の声が記録され、声の特徴が抽出されて一意の音声シグネチャが形成されます。 識別フェーズでは、入力音声のサンプルが、指定された登録済みの音声のリスト (各要求で最大 50) と比較されます。

## <a name="data-security-and-privacy"></a>データのセキュリティとプライバシー

登録用の音声オーディオや音声シグネチャの特徴を含む話者登録データは、セキュリティで保護されたシステムに格納されます。 登録用の音声オーディオは、アルゴリズムがアップグレードされたときにのみ使用されます。その際は、特徴を再度抽出する必要があります。 認識フェーズ中にサービスに送信される音声録音や抽出された声の特徴は、サービスに保持されません。 

データが保持される期間は開発者が制御します。 開発者は、API 呼び出しによって個々の話者の登録データを作成、更新、および削除できます。 サブスクリプションが削除されると、そのサブスクリプションに関連付けられているすべての話者の登録データも削除されます。 

Cognitive Services リソース全般に言えることですが、話者認識サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 開発者は、話者認識用の適切なアクセス許可が、ユーザーから付与されていることを確認する必要があります。 詳細については、Microsoft セキュリティ センターの  [Cognitive Services のページ](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/) を参照してください。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> * テキストに依存しない話者認証については、[ビデオ チュートリアル](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/)を参照してください。
