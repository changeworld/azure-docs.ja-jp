---
title: Speech Service SDK API ドキュメント - チュートリアル、API リファレンス
titleSuffix: Azure Cognitive Services
description: Speech Service SDK を使用してアプリを作成および開発する方法を説明します
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 7510b1501051b79f6bdaf33ec57647a7982fafda
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469420"
---
# <a name="ship-an-application"></a>アプリケーションの出荷

Azure Cognitive Services Speech SDK を配布するときは、[Speech SDK ライセンス](https://aka.ms/csspeech/license201809)だけでなく、[サード パーティ ソフトウェアに関する通知](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html)も確認します。 さらに、[Microsoft のプライバシーに関する声明](https://aka.ms/csspeech/privacy)も確認してください。

プラットフォームによって、ご自身のアプリケーションを実行するための依存関係には違いがあります。

## <a name="windows"></a>Windows

Cognitive Services Speech SDK は、Windows 10 および Windows Server 2016 でテストされています。

Cognitive Services Speech SDK には、[Visual Studio 2017 の Microsoft Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)が必要です。 最新バージョンの `Microsoft Visual C++ Redistributable for Visual Studio 2017` のインストーラーはこちらからダウンロードできます。

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

お使いのアプリケーションでマネージド コードを使用する場合は、ターゲット マシンに `.NET Framework 4.6.1` 以降が必要です。

マイク入力のために、Media Foundation ライブラリをインストールする必要があります。 これらのライブラリは、Windows 10 および Windows Server 2016 に含まれます。 マイクがオーディオ入力デバイスとして使用されていない場合は、これらのライブラリがなくても、Speech SDK を使用できます。

必要な Speech SDK ファイルは、お使いのアプリケーションと同じディレクトリに展開できます。 この方法で、お使いのアプリケーションはライブラリに直接アクセスできます。 必ず、お使いのアプリケーションと一致する正しいバージョン (Win32/x64) を選択してください。

| Name | 関数
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK。ネイティブおよびマネージド展開に必要
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | マネージド展開に必要
| `Microsoft.CognitiveServices.Speech.csharp.dll` | マネージド展開に必要

## <a name="linux"></a>Linux

ネイティブ アプリケーションについては、Speech SDK ライブラリ `libMicrosoft.CognitiveServices.Speech.core.so` を配布する必要があります。
必ず、お使いのアプリケーションと一致するバージョン (x86、x64) を選択してください。 Linux バージョンによっては、次の依存関係を追加しなければならない場合もあります。

* GNU C ライブラリの共有ライブラリ (POSIX Threads Programming ライブラリ `libpthreads` など)
* OpenSSL ライブラリ (`libssl.so.1.0.0`)
* cURL ライブラリ (`libcurl.so.4`)
* ALSA アプリケーションの共有ライブラリ (`libasound.so.2`)

たとえば、Ubuntu 16.04 では、GNU C ライブラリが既定でインストールされています。 残りの 3 つをインストールするには、次のコマンドを使用します。

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を認識する方法を確認する](quickstart-csharp-dotnet-windows.md)
