---
title: Cognitive Services のゲート プロセス
titleSuffix: Azure Cognitive Services
description: 新しい Cognitive Services コンテナーと API への早期アクセスを申請する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599347"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Azure Cognitive Services のゲート プロセス

> [!NOTE]
> サービス内容は、ゲート付きプレビューが完了すると、アクセスのための申請を必要としない "ゲートなし" パブリック プレビューに入ります。 プレビュー プロセスの後、一般提供 (GA) としてリリースされます。

新しい Azure Cognitive Services オファリングが導入されると、お客様が申請によりアクセスを要求する必要があるゲート付きプレビューが行われます。 このゲート プロセスは、サービス内容が広く利用可能になる前に、その改善の機会を特定するのに役立ちます。 

この記事では、ゲート付き Cognitive Services オファリングの申請プロセスについて説明します。

## <a name="eligibility-and-approval-process"></a>資格と承認のプロセス

ゲート プロセスは、関心度を測定し、お客様のニーズをよりよく理解するために実施されています。 Microsoft チームは、有効な Azure サブスクリプションと有効なビジネス シナリオを有する Microsoft の法人のお客様からの[申請](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)を受け入れています。 次の場合に、お客様の申請が拒否される可能性があります。

 - どの組織にも所属していない
 - 有効な Azure サブスクリプションがない
 - 申請が個人の電子メール (@hotmail.com、@gmail.com、@yahoo.com) で送信された
 - 適切な理由またはビジネス シナリオの提示がなかった

Microsoft では、さまざまなお客様セグメントからの需要を考慮して、承認プロセスの迅速化に取り組んでいます。 しかしながら、タイムラインをお約束することはできません。 決定がなされた後、Microsoft チームからお客様とお客様のアカウント管理チームに次の手順についてご連絡させていただきます。 ご理解とご辛抱をお願いいたします。

申請が承認された場合、Microsoft チームから詳細、ドキュメント、ガイダンスを記載した電子メールが送信されます。 Cognitive Services の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/cognitive-services/)をご覧ください。


現在、次のサービスは、ゲート プロセスを通じて提供されています。

## <a name="gated-web-apis"></a>ゲート付き Web API

|サービス  |
|---------|
|Anomaly Detector v2     | 

## <a name="gated-online-containers"></a>ゲート付きオンライン コンテナー

| サービス                             | コンテナー                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Computer Vision][cv-containers]    | Read                                                                          |
| [Face][fa-containers]               | Face                                                                          |
| [Form Recognizer][fr-containers]    | Form Recognizer                                                               |
| [Speech Service API][sp-containers] | 音声テキスト変換 (カスタムと標準) およびテキスト読み上げ (カスタムと標準) |
| [Translator Text][tt-containers]    | Translator Text                                                               |

> [!IMPORTANT]
> サービスまたはコンテナー化されたオファリングが一覧にない場合は、ゲートが設定されていないか、使用できません。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ゲート付きサービスにサインアップする](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
