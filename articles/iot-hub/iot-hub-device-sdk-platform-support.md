---
title: Azure IoT device SDK プラットフォームのサポート | Microsoft Docs
description: 概念 - Azure IoT device SDK でサポートされているプラットフォームの一覧
author: yzhong94
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: cf3c80424c4626b62317bda537f9491cafc8198c
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043601"
---
# <a name="azure-iot-sdks-platform-support"></a>Azure IoT SDK プラットフォームのサポート

[Azure IoT SDK](iot-hub-devguide-sdks.md) は、さまざまな言語およびプラットフォーム サポートによって、IoT Hub および Device Provisioning Service とやり取りするためのライブラリのセットです。  この SDK は最も一般的なプラットフォームで実行され、C SDK は、開発者が[移植ガイダンス](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)に従って特定のプラットフォームに移植できます。 

Microsoft はさまざまなオペレーティング システム/プラットフォーム/フレームワークをサポートしており、Azure IoT C SDK を使用して拡張できます。  その一部はチームによって正式にサポートされており、ユーザーが受けられるサポートのレベルを表す階層別にグループ化されています。  完全にサポートされているプラットフォームで Microsoft が提供するサービス:
    - 継続的なビルドと、マスターおよび LTS サポートされているバージョンに対するエンド ツー エンド テスト
    - インストール ガイドまたはパッケージの提供 (該当する場合)
    - GitHub の完全サポート

さらに、複数のパートナーが Microsoft の C SDK をその他のプラットフォームに移植して、プラットフォーム抽象化レイヤー (PAL) を保持しています。  [Azure Certified for IoT デバイス カタログ](https://catalog.azureiotsolutions.com/)にも、さまざまな SDK がテストされた OS プラットフォームの一覧が示されています。  また、その SDK は、次のプラットフォーム上にも、限定的なテストとサポート付きで定期的に構築されます。
- MBED2
- Arduino
- Windows CE 2013 (2018 年 10 月に非推奨になります)
- .NET Standard 1.3 と .NET Core 2.1 および .NET Framework 4.7
- Xamarin iOS、Android、UWP
- Java を使用した Android

## <a name="supported-platforms"></a>サポートされるプラットフォーム
### <a name="c-sdk"></a>C SDK
| OS                  | Arch | コンパイラ             | TLS ライブラリ       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | openssl  - 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | gcc-7.3              | WolfSSL – 1.13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | Openssl – 1.1.0g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | ネイティブ OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc-7.3              | Openssl – 1.1.0f  |

### <a name="python-sdk"></a>Python SDK
| OS                  | Arch | コンパイラ   | TLS ライブラリ |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | openssl     |
| Windows Server 2016 | x64  | Python 2.7 | openssl     |
| Windows Server 2016 | x86  | Python 3.5 | openssl     |
| Windows Server 2016 | x64  | Python 3.5 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 2.7 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 3.4 | openssl     |
| MacOS High Sierra   | x64  | Python 2.7 | openssl     |

### <a name="net-sdk"></a>.NET SDK
| OS                  | Arch | フレームワーク            | 標準          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.7   | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | 該当なし               |

### <a name="nodejs-sdk"></a>Node.js SDK
| OS                                           | Arch | Node バージョン |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS (Node 6 Docker イメージを使用) | X64  | Node 6       |
| Windows Server 2016                          | X64  | Node 6       |

### <a name="java-sdk"></a>Java SDK
| OS                  | Arch | Java バージョン |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |

## <a name="partner-supported-platforms"></a>パートナーによってサポートされているプラットフォーム
| パートナー             | デバイス                            | Link                     | サポート |
|---------------------|------------------------------------|--------------------------|---------|
| Qualcomm            | Qualcomm MDM9206 LTE IoT モデム     | [Qualcomm LTE for IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [フォーラム](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | STM32L4 シリーズ STM32F4 シリーズ      | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html)             | [サポート](https://www.st.com/content/st_com/en/support/support-home.html) |
|                     | STM32F7 シリーズ                     | [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html)             |         |
|                     | STM32L4 Discovery Kit for IoT ノード | [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html)          |         |
|                     |                                    | [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            |         |
| Espressif           | ESP32                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  |

## <a name="next-steps"></a>次の手順
- [デバイス SDK とサービス SDK](iot-hub-devguide-sdks.md)
- [移植ガイダンス](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)