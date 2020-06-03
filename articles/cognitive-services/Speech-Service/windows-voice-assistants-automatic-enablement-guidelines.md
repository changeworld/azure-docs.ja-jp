---
title: Windows の音声アシスタントのプライバシー ガイドライン
titleSuffix: Azure Cognitive Services
description: 既定で音声エージェントの音声によるアクティブ化を有効にするための手順
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997326"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Windows の音声アシスタントのプライバシー ガイドライン

ユーザーの音声データの収集方法と利用状況については、はっきりした情報をユーザーに提供することが重要です。また、この収集を行うかどうかとその方法については、ユーザーに決めてもらうことが重要です。 プライバシーの中心である "*開示*" と "*同意*" は、その使用に常時リスニングという性質がある以上、Windows に統合される音声アシスタントにとって特に重要です。

Windows で音声アシスタントを開発する人は、アシスタントのリスニング機能を反映するアプリケーション内に明瞭なユーザー インターフェイス要素を含める必要があります。

> [!NOTE]
> アプリケーションの更新後も含め、アシスタント アプリケーションで適切な開示と同意を提供できない場合、プライバシーの問題が解決されるまで、アシスタントを音声でアクティブ化できなくなることがあります。 

## <a name="minimum-requirements-for-feature-inclusion"></a>機能を含めるための最小要件

Windows ユーザーはアシスタント アプリケーションを利用できるかどうかを **`Settings > Privacy > Voice activation`** で確認し、制御できます。

 > [!div class="mx-imgBorder"]
 > [![privacy-app-listing](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "アシスタント アプリケーションの Windows 音声アクティブ化プライバシー設定エントリ")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

この一覧に含める資格を得るには、アプリケーションに次の条件が求められます。

1. アプリケーションが実行されていないときであっても、アプリケーションがあるキーワードを待ち受けていることとそのキーワードが何であるかをユーザーにはっきり伝えること
1. 関連するプライバシー ポリシーのリンクや参照を含め、ユーザーの音声データの利用方法に関する説明を含めること
1. アプリ内設定があればそれに加え、ユーザーが **`Settings > Privacy > Voice activation`** で自分のプライバシー選択を表示し、変更できることをユーザーに伝えること (任意で、直接アクセスのための `ms-settings:privacy-voiceactivation` のプロトコル リンクを含む)

以上の要件を満たし、Microsoft から承認を得られると、`Windows.ApplicationModel.ConversationalAgent` API に登録後、アシスタント アプリケーションが音声アクティブ化アプリの一覧に表示され、ユーザーはキーワード アクティブ化についてアプリケーションに同意を与えることができます。 既定では、これらの設定はいずれも `Off` であり、有効にするにはユーザーは手動で [設定] ページに移動する必要があります。

> [!NOTE]
> どのような場合であっても、音声でアクティブ化を許可するにはマイクを許可する必要があります。 アシスタント アプリケーションでマイクにアクセスできない場合、音声でアクティブ化するための資格が与えられず、音声アクティブ化プライバシー設定には無効な状態で表示されます。

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>マイク同意に含めるための追加要件

ユーザーが音声アクティブ化を簡単に選択できるアシスタントを開発する場合、上記にいくつかの要件を追加することでそれを達成できます。 これらが満たされると、アプリケーションにマイク アクセスが与えられたとき (一度だけ)、アシスタント アプリケーションの標準の (デバイスでロックされない) 音声アクティブ化設定が既定で `On` になります。 アシスタントを音声でアクティブ化する前に [設定] に進む必要がありません。

追加の要件として、アシスタント アプリケーションに次が求められます。

1. アシスタント アプリケーションでは、それが実行されていないときでも、ユーザーの音声の中からキーワードを聴き取ろうとし、ユーザーの同意を求めますが、そのことを、(たとえば、`AppCapability.RequestAccessAsync` API を使用し) マイクの同意を求める**前**に、ユーザーにはっきり指示すること
2. マイク アクセスを求めるか `Windows.ApplicationModel.ConversationalAgent` API を使用する**前**に、データの使用とプライバシー ポリシーに関する情報をすべて含めること
3. 音声キャプチャ動作を開示し、許可を要求するエクスペリエンス フローでは、指示的または誘導的な言い回しを避けること ("次のプロンプトで [はい] をクリックしてください" など)

以上の要件が満たされると、マイク アクセスが与えられたとき、音声でアクティブ化できるアプリケーションの一覧にアシスタント アプリケーションが `enabled` 状態で表示されます。

> [!NOTE]
> ロック時の音声アクティブ化はマイク アクセスをともなう自動有効化の対象にならず、ユーザーは音声アクティブ化プライバシー ページに進み、アシスタントのロック時アクセスを有効にする必要があります。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Windows での音声アシスタントのベスト プラクティスについて学習する](windows-voice-assistants-best-practices.md)