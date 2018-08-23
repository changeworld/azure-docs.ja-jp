---
title: Azure Cognitive Services の Cognitive Services Speech SDK API のドキュメント - チュートリアル、API リファレンス
description: Cognitive Services Speech SDK を使用してアプリを作成および開発する方法を説明します
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 65ff0e47cf7a53d519bfd0c50ea4c3ebd09a5766
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "41929808"
---
# <a name="shipping-an-application"></a>アプリケーションの配布

Cognitive Services Speech SDK を配布するときは、[Speech SDK ライセンス](license.md)だけでなく、[サード パーティ ソフトウェアに関する通知](third-party-notices.md)も確認します。 さらに、[Microsoft のプライバシーに関する声明](https://aka.ms/csspeech/privacy)も確認してください。

プラットフォームによって、ご自身のアプリケーションを実行するための依存関係には違いがあります。

## <a name="windows"></a>Windows

Cognitive Services Speech SDK は、Windows 10 および Windows Server 2016 でテストされています。

Cognitive Services Speech SDK には、[Visual Studio 2017 の Microsoft Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)が必要です。 最新バージョンの `Microsoft Visual C++ Redistributable for Visual Studio 2017` のインストーラーはこちらからダウンロードできます。

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

お使いのアプリケーションでマネージド コードが使用されている場合は、ターゲット マシンに `.NET Framework 4.6.1` 以降が必要です。

マイク入力のために、Media Foundation ライブラリをインストールする必要があります。 これらのライブラリは、Windows 10 および Windows Server 2016 に含まれます。 マイクがオーディオ入力デバイスとして使用されていない場合は、これらのライブラリがなくても、Speech SDK を使用できます。

必要な Speech SDK ファイルは、お使いのアプリケーションと同じディレクトリに展開できます。 この方法で、お使いのアプリケーションはライブラリに直接アクセスできます。 必ずお使いのアプリケーションと一致する正しいバージョン (Win32/x64) を選択してください。

| Name | 関数
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK。ネイティブおよびマネージド展開に必要
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | マネージド展開に必要
| `Microsoft.CognitiveServices.Speech.csharp.dll` | マネージド展開に必要

## <a name="linux"></a>Linux

ネイティブ アプリケーションについては、Speech SDK ライブラリ `libMicrosoft.CognitiveServices.Speech.core.so` を配布する必要があります。
必ずお使いのアプリケーションと一致するバージョン (x86、x64) を選択してください。 Linux バージョンによっては、次の依存関係を追加しなければならない場合もあります。

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
