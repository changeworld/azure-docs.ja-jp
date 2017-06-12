---
title: Bing Speech API in Microsoft Cognitive Services | Microsoft Docs
description: Use the Bing Speech API to add speech-driven actions to your apps, including real-time interaction with users.
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 02/28/2017
ms.author: prrajan
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: af16523b62a1fdb3c38613d8768273afa40014fe
ms.contentlocale: ja-jp
ms.lasthandoff: 05/31/2017

---
# <a name="bing-speech-api-overview"></a>Bing speech API overview
Welcome to Speech Service – Microsoft’s speech recognition and text to speech cloud offering which helps users to voice-enable their applications and bring in a delightful conversational experience. 

Microsoft Speech Service has two APIs:
* [Speech to Text API](#speech-to-text-speech-recognition):   
    Using REST API : For apps converting short spoken commands (up to 15 seconds) to text without real-time streaming or user feedback.  
    Using WebSocket API : For apps converting long audio (up to 10 minutes) to text with intermediate results.
* [Text to Speech API](#text-to-speech-speech-synthesis) 
    For apps requiring the functionality of converting text into audio that can be played back to the user. 

## <a name="speech-to-text-stt"></a>Speech to Text (STT)
Microsoft's Speech Service *transcribes* audio streams into text suitable for display to a user. Transcription includes adding appropriate capitalization and punctuation, masking profanity, and normalizing text. For example, if a user says `remind me to buy six pencils`, Microsoft's Speech Services will return the transcribed text `Remind me to buy 6 pencils.` There are two options for adding speech recognition capabilities to your app:
* The REST API uses chunked-transfer encoding to convert short spoken commands without real-time streaming or user feedback.
* The WebSocket API uses full-duplex communication to convert longer audio input and supports intermediate results.
Use this comparison chart to help you choose the API that fits your needs.

### <a name="speech-recognition-modes"></a>Speech recognition modes
The API supports multiple types of speech input depending on speaking style. Choosing the most suitable style you expect produces better recognition results:

| Mode | Description |
|---|---|
| Interactive | A user makes short requests and expects the application to perform an action in response. |
| Dictation | Users are engaged in a human-to-human conversation. |
| Conversation | The user recites longer utterances to the application for further processing. |

Refer to [Recognition Modes](api-reference-rest/bingvoicerecognition.md#recognition-modes) in API reference for more information.

### <a name="quick-feature-comparison"></a>Quick feature comparison
Here is a quick comparison of features between the REST and WebSocket APIs for speech recognition.

| Feature | WebSocket API | REST API |
|-----|-----|-----|
| Continuous Recognition | Yes | No |
| Audio Length  | 10 mins | 15 seconds |
| Partial results | Yes | No |
| Service Endpointing | Yes| No |
| Recognition Modes | Interactive, Dictation, Conversational| Interactive, Dictation, Conversational |
| Debug support with Telemetry | Yes | No |
| Subscription Key Authorization | Yes | No |
| Reco Results | Lexical, ITN, Masked | Lexical, ITN, Masked |
| N-Best | Up to 5 | Up to 5 |
| Confidence | Yes| Yes |  

Refer to [Output Format](api-reference-rest/bingvoicerecognition.md#output-format) in API reference for more information.

## <a name="supported-languages"></a>Supported languages  
Speech recognition API supports many spoken languages in multiple dialects. Refer to [Recognition Languages](api-reference-rest/bingvoicerecognition.md#recognition-languages) for full list of supported languages for each recognition mode.

## <a name="text-to-speech-tts"></a>Text to speech (TTS)
The speech synthesis REST API provides real-time text to speech conversion in a variety of different voices and languages. The maximum amount of audio returned for any single request must not exceed 15 seconds. 

### <a name="supported-languages"></a>Supported languages  
Text to speech API supports many voices in many languages in multiple dialects. Refer to [Supported Locales and Voice Fonts](api-reference-rest/bingvoiceoutput.md#a-namesuplocalesasupported-locales-and-voice-fonts) for full list of supported languages and voices.


