---
title: 音声読み上げを構成する
titleSuffix: Azure Applied AI Services
description: この記事では、音声読み上げのさまざまなオプションを構成する方法について説明します。
author: metanMSFT
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 2694525c406a40a4c133eb3199d2222542905ae6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779600"
---
# <a name="how-to-configure-read-aloud"></a>音声読み上げを構成する方法

この記事では、イマーシブ リーダーで音声読み上げのさまざまなオプションを構成する方法について説明します。

## <a name="automatically-start-read-aloud"></a>自動的に音声読み上げを開始する

`options` パラメーターには、音声読み上げを構成するために使用できるすべてのフラグが含まれています。 `autoplay` を `true` に設定して、イマーシブ リーダーを起動した後に自動的に音声読み取りが開始されるようにします。

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> ブラウザーの制限により、Safari では自動再生はサポートされていません。

## <a name="configure-the-voice"></a>音声を構成する

`voice` を `male` または `female` のいずれかに設定します。 すべての言語で両方の音声がサポートされているわけではありません。 詳細については、[言語サポート](./language-support.md)に関するページを参照してください。

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>再生速度を構成する

`speed` を `0.5` (50%) と `2.5` (250%) の間の数値 (これらの値を含む) に設定します。 この範囲外の値は、0.5 または 2.5 に固定されます。

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>次のステップ

* [イマーシブ リーダー SDK リファレンス](./reference.md)を参照する