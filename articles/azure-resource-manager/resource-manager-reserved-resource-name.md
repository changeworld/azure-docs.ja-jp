---
title: "Azure で予約されたリソース名のエラー | Microsoft Docs"
description: "予約された単語を含むリソース名を指定するときのエラーを解決する方法について説明します。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 2f06b7bc65ea309bc5374b41f402f8e7bc4d2a38
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2017
---
# <a name="resolve-reserved-resource-name-errors"></a>予約されたリソース名のエラーを解決する

この記事では、名前に予約された単語が含まれるリソースをデプロイしているときに発生するエラーについて説明します。

## <a name="symptom"></a>症状

パブリック エンドポイント経由で使用可能なリソースをデプロイしているときに、次のエラーが表示されることがあります。

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>原因

パブリック エンドポイントが存在するリソースは、名前の中で予約された単語または商標を使用できません。

次の単語は予約されています。

* ACCESS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

次の単語は、名前の中の単語全体または部分文字列のどちらとしても使用できません。

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>解決策

いずれの予約された単語も使用していない名前を指定してください。