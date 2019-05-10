---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 4f48fc0ad65984df42e2a85853f39e49d7481302
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/02/2019
ms.locfileid: "65020435"
---
### <a name="standard-and-neural-voices"></a>標準およびニューラル音声

このテーブルを使用して、リージョン/エンドポイントによる標準およびニューラル音声の可用性を決定します。

| リージョン | エンドポイント | 標準音声 | ニューラル音声 |
|--------|----------|-----------------|---------------|
| オーストラリア東部 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| カナダ中部 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 米国中部 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | はい | いいえ  |
| 東アジア | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | はい | いいえ  |
| 米国東部 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 米国東部 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | はい | いいえ  |
| フランス中部 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | はい | いいえ  |
| インド中部 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 東日本 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | はい | いいえ  |
| 韓国中部 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | はい | いいえ  |
| 米国中北部 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | はい | いいえ  |
| 北ヨーロッパ | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | はい | いいえ  |
| 米国中南部 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 東南アジア | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 英国南部 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 西ヨーロッパ | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |
| 米国西部 | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | はい | いいえ  |
| 米国西部 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | はい | はい |

### <a name="custom-voices"></a>カスタム音声

カスタム音声フォントを作成した場合は、作成したエンドポイントを使用します。 また、以下の一覧に示したエンドポイントを使用して、`{deploymentId}` を音声モデル用のデプロイ ID に置き換えます。

| リージョン | エンドポイント |
|--------|----------|
| オーストラリア東部 | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| カナダ中部 | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国中部 | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東アジア | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国東部 | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国東部 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| フランス中部 | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| インド中部 | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東日本 | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 韓国中部 | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国中北部 | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 北ヨーロッパ | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国中南部 | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 東南アジア | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 英国南部 | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 西ヨーロッパ | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国西部 | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| 米国西部 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
