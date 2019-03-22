---
title: Frequently Asked Questions - Personality Chat
titlesuffix: Azure Cognitive Services
description: FAQs on Personality Chat
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: 88dfbb2a547b44de1bb98ca536c7841570a12168
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57766837"
---
# <a name="frequently-asked-questions"></a>Frequently Asked Questions

## <a name="why-doesnt-this-answer-every-question-i-ask-it-like-other-chat-bots"></a>Why doesn't this answer every question I ask it, like other chat bots?

Project Personality chat will enhance a bot with common small talk that showcases personality and creates a more complete user experience. It's not designed to carry on long conversations about topics that aren't relevant to the bot's primary function. While it could respond to all conversations, it's restricted, in the beta version, to common small talk domains.

## <a name="how-can-i-customize-the-personality-to-suit-my-brand"></a>How can I customize the personality to suit my brand?

Select the closest persona from the available default personas. Today, you can take the editorial library and edit the responses to better suit your brand. In the future, you can upload a sample set of utterances from your chosen personality and find its closest persona ID version. There are also ways to retrain and customize the model.

## <a name="is-this-service-powering-existing-intelligent-agents-such-aszo"></a>Is this service powering existing intelligent agents such as Zo?

The services powering Zo, Cortana, and Project Personality Chat share some similar techniques, but are different stacks. It has incorporated learnings from the experiences with Zo and Cortana.

## <a name="can-this-service-lead-to-bad-customer-experiences"></a>Can this service lead to bad customer experiences?

To provide a richer experience, Personality Chat can generate responses beyond the ones in the editorial dataset, and it tries to interpret all user input. So, it's possible a response won't seem right in context. A number of controls have been put into place to help prevent unfavorable responses, building on knowledge from intelligent agents like Zo. By default, Project Personality Chat is set to respond solely to recognized user intents. You may want to test whether Project Personality Chat is suitable for your circumstances. Your feedback is welcome if you see anything that needs further training. If you use this service with your customers in the future, we recommend you consider anonymized logging to help you identify issues with live user interactions.
