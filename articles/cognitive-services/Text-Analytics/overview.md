---
title: Azure Text Analytics API | Microsoft Docs
description: Use the Text Analytics API for sentiment analysis, key phrase extraction, topic detection for English text, and much more.
services: cognitive-services
author: LuisCabrer
manager: mwinkle
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 05/02/2017
ms.author: onewth
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 497aec125fcb3cade7ca4a35440e279a7f6275fb
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017

---

# <a name="text-analytics-api"></a>Text Analytics API

Use a few lines of code to easily analyze sentiment, extract key phrases, detect topics, and detect language for any kind of text.

## <a name="sentiment-analysis"></a>Sentiment Analysis

Find out what users think of your brand or topic by analyzing any text using sentiment analysis. You are now easily able to monitor the perception of your brand or topic over time.

Sentiment score is generated using classification techniques, and returns a score between 0 and 1. The input features to the classifier include n-grams, features generated from part-of-speech tags, and embedded words. 

## <a name="key-phrase-extraction"></a>Key Phrase Extraction

Automatically extract key phrases to quickly identify the main points. We employ techniques from Microsoft Office's sophisticated Natural Language Processing toolkit.

For example, for the input text ‘The food was delicious and there were wonderful staff’, the service returns the main talking points: ‘food’ and ‘wonderful staff’.

## <a name="topic-detection"></a>Topic Detection

This service returns topics that have been detected in multiple text articles. The service is designed to work well for short, human written text such as reviews and user feedback. It can help you to understand the main issues or suggestions that customers are mentioning.

## <a name="language-detection"></a>Language Detection

The service can be used to detect which language the input text is written in. 120 languages are supported.

## <a name="supported-languages"></a>Supported Languages

| Language    | Language code | Sentiment | Key phrases | Topics |
|:----------- |:----:|:----:|:----:|:----:|
| Danish      | `da` | ✔ \* |  |   |
| German       | `de` | ✔ \* | ✔ |   |
| Greek       | `el` | ✔ \* |  |   |
| English     | `en` | ✔ | ✔ | ✔ |
| Spanish     | `es` | ✔ | ✔ |   |
| Finnish     | `fi` | ✔ \* |  |   |
| French      | `fr` | ✔ |  |   |
| Japanese    | `ja` |  | ✔ |   |
| Italian     | `it` | ✔ \* |  |   |
| Dutch       | `nl` | ✔ \* |  |   |
| Norwegian   | `no` | ✔ \* |  |   |
| Polish      | `pl` | ✔ \* |  |   |
| Portuguese  | `pt` | ✔ |  |   |
| Russian     | `ru` | ✔ \* |  |   |
| Swedish     | `sv` | ✔ \* |  |   |
| Turkish     | `tr` | ✔ \* |  |   |

\* indicates language support in preview

