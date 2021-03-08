---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: d0298bcd675b1b94999dab3a1ad1c40a6feb7438
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135808"
---
:::row:::
    :::column span="3":::
        音声 SDK は、Linux で使用する際、 **Ubuntu 16.04/18.04/20.04** 、 **Debian 9/10** 、 **Red Hat Enterprise Linux (RHEL) 7/8** 、 **CentOS 7/8** のみをサポートしています。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- C++ 開発用の x86 (Debian/Ubuntu)、x64、ARM32 (Debian/Ubuntu)、および ARM64 (Debian/Ubuntu)
- Java 用 x64、ARM32 (Debian/Ubuntu)、および ARM64 (Debian/Ubuntu)
- .NET Core 用 x64、ARM32 (Debian/Ubuntu)、および ARM64 (Debian/Ubuntu)
- Python 用の x64

> [!IMPORTANT]
> Linux ARM64 上の C# については、.NET Core 3.x (dotnet-sdk-3.x パッケージ) が必要です。

### <a name="system-requirements"></a>システム要件

ネイティブアプリケーションの場合、音声SDK は`libMicrosoft.CognitiveServices.Speech.core.so`に依存します。 ターゲットアーキテクチャ (x86、x64) がアプリケーションと一致していることを確認します。 Linux のバージョンによっては、追加の依存関係が必要になる場合があります。

- GNU C ライブラリの共有ライブラリ (POSIX Threads Programming ライブラリ `libpthreads` など)
- OpenSSL ライブラリ (`libssl.so.1.0.0` または `libssl.so.1.0.2`)
- ALSA アプリケーションの共有ライブラリ (`libasound.so.2`)

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

> [!NOTE]
> libssl1.0.x が利用できない場合、代わりに libssl1.1 をインストールしてください。

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

> [!NOTE]
> libssl1.0.x が利用できない場合、代わりに libssl1.1 をインストールしてください。

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 and CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> - RHEL または CentOS 7 の場合、「[Speech SDK 用に RHEL/CentOS 7 を構成する](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md)」の手順に従います。
> - RHEL または CentOS 8 の場合、「[Linux 用 OpenSSL の構成](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md)」の手順に従います。

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
