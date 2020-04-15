---
title: RHEL/CentOS 7 - 音声サービスの構成方法
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用できるように RHEL/CentOS 7 を構成する方法について説明します。
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639152"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Speech SDK 用に RHEL/CentOS 7 を構成する

Speech SDK バージョン1.10.0 以降では、Red Hat Enterprise Linux (RHEL) 8 x64 と CentOS 8 x64 を正式にサポートしています。 RHEL/CentOS 7 x64 で Speech SDK を使用することもできますが、そのためには、お使いのシステムの C++ コンパイラ (C++ 開発用) と共有 C++ ランタイム ライブラリを更新する必要があります。

C++ コンパイラのバージョンを確認するには、次の手順を実行します。

```bash
g++ --version
```

コンパイラがインストールされている場合、出力は以下のようになります。

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

このメッセージで、GCC メジャー バージョン 4 がインストールされていることがわかります。 このバージョンは、Speech SDK で使用する C++ 11 標準を完全にはサポートしていません。 この GCC バージョンと Speech SDK ヘッダーで C++ プログラムをコンパイルしようとすると、コンパイル エラーが発生します。

また、共有 C++ ランタイム ライブラリ (libstdc + +) のバージョンを確認することも重要です。 ほとんどの Speech SDK は、ネイティブ C++ ライブラリとして実装されているため、アプリケーション開発に使用する言語に関わりなく、libstdc + + に依存しています。

システム上の libstdc + + の場所を検索するには、次の手順を実行します。

```bash
ldconfig -p | grep libstdc++
```

バニラの RHEL/CentOS 7 (x64) での出力は次のとおりです。

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

このメッセージに基づき、次のコマンドを使用してバージョンの定義を確認します。

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

出力は次のようになります。

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Speech SDK には、**CXXABI_1.3.9** と **GLIBCXX_3.4.21** が必要です。 この情報は、Linux パッケージの Speech SDK ライブラリで `ldd libMicrosoft.CognitiveServices.Speech.core.so` を実行することで確認できます。

> [!NOTE]
> システムにインストールされている GCC のバージョンは **5.4.0** 以降を推奨、それに合致するランタイム ライブラリも必要です。

## <a name="example"></a>例

このサンプル コマンドは、Speech SDK 1.10.0 以降を使用して、開発用 (C++, C#, Java, Python) の RHEL/CentOS 7 x64 を構成する方法を示しています。

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Speech SDK について](speech-sdk.md)
