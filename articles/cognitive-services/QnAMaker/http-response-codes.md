---
title: API の HTTP 応答コード - QnA Maker
titleSuffix: Azure Cognitive Services
description: どのような HTTP 応答コードが QnA Maker Api から返されるかを理解する
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 96782d32817e4989c02e0ed098d7772c80aa26c8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079669"
---
# <a name="qna-maker-api-http-response-codes"></a>QnA Maker API の HTTP 応答コード
[管理](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)と予測 API は、HTTP 応答コードを返します。 応答メッセージには、要求に固有の情報が含まれていますが、HTTP 応答の状態コードは全般的なものです。 

### <a name="management"></a>管理

|コード|説明|
|:--|--|
|2xx|Success|
|400|"要求のパラメーターが正しくない" とは、必須パラメーターが見つからない、形式が正しくない、または大きすぎることを意味します|
|400|"要求の本文が正しくない" とは、JSON が見つからない、形式が正しくない、または大きすぎることを意味します|
|401|無効なキー|
|403|禁止されています - アカウントに適切なアクセス許可がありません|
|404|KB が存在しません|