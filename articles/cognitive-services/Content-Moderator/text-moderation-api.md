---
title: Text Moderation API in Content Moderator | Microsoft Docs
description: The Text Moderation API moderates text for profanity, reports malware and phishing URLs, and matches against lists specific to a business.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.technology: content-moderator
ms.topic: article
ms.date: 12/01/2016
ms.author: sajagtap
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: c4d2d009afcc91bfb909ef7f6c8ca4657e82bf34
ms.lasthandoff: 04/12/2017

---

# <a name="text-moderation-api"></a>Text Moderation API

Use the Content Moderator’s [text moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66e "Content Moderator Text Moderation API") to moderate text for profanity in more than 100 languages, report on malware and phishing URLs, and match against custom and shared lists that are specific to your business and users.

## <a name="language-detection"></a>Language detection

The first step to using the text moderation API is to have the algorithm detect the language of the content to be moderated. The API supports more than [100 languages](Text-Moderation-API-Languages.md). The **Detect Language** operation will return language codes for the predominant language comprising the submitted text in the following format: {"DetectedLanguage": "eng"}

## <a name="screening-for-profanity"></a>Screening for profanity

The text moderation API’s **Screen** operation does it all – screen the incoming text (maximum 1024 characters) for profanity, detect the presence of malware and phishing URLs, and PII, while matching against custom lists of terms if needed.

The response will contain one or more of these, depending on what you ask it to do:

- Auto-corrected text
- Original text
- Language
- PII
- Location(s) of detected term(s)/phrase(s) within the submitted text
- Terms: detected profanity content
- Detected malware URLs

Let’s look at these in greater detail.

## <a name="auto-correction"></a>Auto-correction

Let’s assume that the input text is: (the ‘lzay’ is intentional.)

    The <a href="www.bunnies.com">qu!ck</a> brown  <a href="b.suspiciousdomain.com">f0x</a> jumps over the lzay dog www.benign.net.

If you ask for auto-correction, the response will contain the corrected version of the text as in:

    “The quick brown fox jumps over the lazy dog."

## <a name="profanity-terms"></a>Profanity terms

If any terms are detected, those are included in the response, along with their starting index (location) within the original text.

## <a name="malware-and-phishing-urls"></a>Malware and phishing URLs

The Screen operation’s response will return any malware and phishing URLs detected and their categories as in adult, malware, and/or phishing, along with a probability score between 0 and 1.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Creating and managing your custom lists of terms

While the default, global list of terms works great for most cases, you may want to screen against terms that are specific to your business needs. For example, you may want to filter out any competitive brand names from posts by users. Your threshold of permitted text content may be very different from the default list.

The Content Moderator provides a complete [terms list API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Content Moderator Terms List API") with operations for creating and deleting lists of terms, and for adding and removing text terms from those lists.

A typical sequence of operations would be to:

1. Create a list.
1. Add terms to your list.
1. Screen terms against the ones in the list.
1. Delete term or terms from the list.
1. Delete the list.

