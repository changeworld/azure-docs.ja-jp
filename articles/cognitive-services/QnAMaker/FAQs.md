---
title: QnA Maker tool FAQs | Microsoft Docs
description: Get answers to the most commonly asked questions about the QnA Maker tool.
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.technology: qnamaker
ms.topic: article
ms.date: 01/04/2017
ms.author: pchoudh
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6108d781335b44208a1a2353f7105b18763281eb
ms.lasthandoff: 04/12/2017

---

# <a name="faqs"></a>FAQs #
### <a name="who-are-the-target-audience-for-the-qna-maker-tool"></a>Who are the target audience for the QnA Maker tool?
QnA Maker is primarily meant to provide a FAQ data source which you can query from your Bot/Application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your Bot/Application.

### <a name="how-do-i-login-to-the-qna-maker-portal"></a>How do I login to the QnA Maker Portal?
You can login with your [Microsoft account](https://www.microsoft.com/en-us/account/).

### <a name="is-the-qna-maker-service-free"></a>Is the QnA Maker Service free?
Yes, currently the QnA Maker tool is free to use. However, we do meter the usage per account. See the Subscription Keys section of the documentation for details.

### <a name="my-urls-have-valid-faq-content-but-the-tool-cannot-extract-them-why-not"></a>My URLs have valid FAQ content, but the tool cannot extract them. Why not?
It’s possible that the tool is not able to auto-extract QnA from valid FAQ URLs. In such cases, you have an option to copy-paste the QnA content in a txt and try ingesting it. Alternately, you can always editorially add content to your knowledge base.

### <a name="what-format-does-the-tool-expect-the-file-content-to-be"></a>What format does the tool expect the file content to be?
We support two formats of files for ingestion.

    1.    .tsv: QnA contained in the format Question<Tab>Answer
    2.    .txt, .docx, .pdf: QnA contained as regular FAQ content, i.e. sequence of questions and answers.
    
### <a name="how-large-a-knowledge-base-can-i-create"></a>How large a knowledge base can I create?
Currently we have a limit of a 20MB knowledge base.

### <a name="the-updates-i-made-to-my-knowledge-base-are-not-reflected-on-publish-why-not"></a>The updates I made to my knowledge base are not reflected on publish. Why not?
Every edit operation, whether in Table update, Test or Settings needs to be saved before it can be published. Make sure you press the Save and retrain button after every edit operation.

### <a name="what-is-the-roadmap-of-the-qna-maker"></a>What is the roadmap of the QnA Maker?
Currently the QnA Maker tool handles semi-structured FAQ content. Eventually the vision is to be able to answer questions from un-structured content as well.

### <a name="do-i-need-to-use-bot-framework-in-order-to-use-qna-maker"></a>Do I need to use Bot Framework in order to use QnA Maker?
No, you don’t. However, QnA Maker is offered as one of several templates in [Azure Bot Service](https://azure.microsoft.com/services/bot-service/) which enables rapid intelligent bot development powered by Microsoft Bot Framework, and run in a serverless environment. Bots scale based on demand; pay only for the resources you consume.

### <a name="why-cant-i-replace-my-knowledge-base-with-the-upload-feature"></a>Why can’t I replace my Knowledge Base with the upload feature?
The format in which upload expects the file is, tab separated columns of Question, Answer and Source.

### <a name="when-should-i-refresh-my-subscription-keys"></a>When should I refresh my subscription keys?
You should refresh your subscription keys if you suspect that they have been compromised. Any requests with your subscription key will count towards your quota.

### <a name="how-safe-is-my-knowledge-base-data"></a>How safe is my knowledge base data?
Every knowledge base content is stored in Azure storage by the QnAMaker tool. You need a combination of knowledge base id and subscription key to access the knowledge base. The knowledge base contents are not used by the tool for any other purpose.

### <a name="does-the-kb-support-rich-data"></a>Does the KB support rich data?
The knowledge base supports Markdown. However, the auto-extraction from URLs has limited HTML to Markdown conversion capability. If you want to use full-fledged Markdown, you can modify your content directly in the Table, or upload a KB with the rich content. 

### <a name="does-the-qna-maker-support-non-en-languages"></a>Does the QnA Maker support non-EN languages?
The QnA Maker tool ingests and matches data in UTF-16 encoding. This means that any language should work as is. Having said that, we have only extensively tested the relevance of the service for EN yet.

### <a name="where-is-the-test-web-chat-url-from-the-old-portal-how-do-i-share-my-kb-with-others-now"></a>Where is the test web-chat URL from the old portal? How do I share my KB with others now?
In the new Service, we do not have the test URL anymore. The reason being, as part of the cognitive services all call are being metered. Since the test URL exposes the subscription key and the KB ID, it is a security risk. However, it is still super easy to chat with your KB and share it. Check out the Azure Bot Templates for [Question and Answer Bot](https://blog.botframework.com/2016/12/13/More-Ways-to-Make-Smart-Bots/). You can light up the QnA Bot in Skype in a few clicks, and then share it with anyone.

### <a name="how-do-i-embed-the-qna-maker-service-in-my-website"></a>How do I embed the QnA Maker service in my website?
Follow the below steps to embed the QnA Maker service as a web chat control in your website:
* Create your knowledge base at [https://qnamaker.ai](https://qnamaker.ai)
* Create your Azure service bot : [https://docs.botframework.com/en-us/azure-bots/build/first-bot](https://docs.botframework.com/en-us/azure-bots/build/first-bot/#navtitle) 
* Look for the Question and Answer Bot template. Select the KB ID you created in step 1
* Then enable it on web chat channel. Get the embed keys.
* Embed the webchat as shown in [https://docs.botframework.com/en-us/support/embed-chat-control2](https://docs.botframework.com/en-us/support/embed-chat-control2/#navtitle) 

### <a name="what-is-the-format-of-the-downloaded-chat-logs"></a>What is the format of the downloaded chat logs?
The chat logs are tab separated files, with the query and the frequency as the columns. Frequency is the number of times the same query was seen. The file is sorted in descending order of frequency. Select questions from the downloaded file you want to test, and then upload it to see what responses the system returned for them.
