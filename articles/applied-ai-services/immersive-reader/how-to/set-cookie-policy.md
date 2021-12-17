---
title: イマーシブ リーダーの Cookie ポリシーを設定する
titleSuffix: Azure Applied AI Services
description: この記事では、イマーシブ リーダーの Cookie ポリシーを設定する方法について説明します。
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 69cf0eb1659b243220eed1199dac06effc22f363
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780833"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>イマーシブ リーダーの Cookie ポリシーを設定する方法

イマーシブ リーダーでは、Cookie の使用が既定では無効にされます。 Cookie の使用がユーザーによって有効にされた場合、イマーシブ リーダーは Cookie を使用して、ユーザーの基本設定を保存したり、機能の使用状況を追跡したりします。 イマーシブ リーダーで Cookie の使用を有効にする場合は、EU の Cookie コンプライアンス ポリシーの要件を考慮してください。 EU の Cookie コンプライアンス ポリシーに従って必要なユーザーの同意を取得するのは、ホスト アプリケーションの責任となります。

Cookie ポリシーは、イマーシブ リーダーの[オプション](../reference.md#options)で設定できます。

## <a name="enable-cookie-usage"></a>Cookie の使用を有効にする

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Cookie の使用を無効にする

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>次のステップ

* [Node.js クイックスタート](../quickstarts/client-libraries.md?pivots=programming-language-nodejs)で、Node.js とイマーシブ リーダー SDK を使用して他にできることを確認する
* [Android チュートリアル](../how-to-launch-immersive-reader.md)を参照して、Android 用の Java または Kotlin を使用してイマーシブ リーダー SDK で他にできることを確認する
* [iOS チュートリアル](../how-to-launch-immersive-reader.md)を参照して、iOS 用の Swift を使用してイマーシブ リーダー SDK で他にできることを確認する
* [Python チュートリアル](../how-to-launch-immersive-reader.md)で、Python と Immersive Reader SDK を使用して他にできることを確認する
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](../reference.md)を探索する