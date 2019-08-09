---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640430"
---
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 (アカウントがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を数分で作成できます)。

* **Windows**

    * [Python 2.x または 3.x](https://www.python.org/downloads/)。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 インストール中に求められた場合は、プラットフォーム固有の環境変数に Python を追加します。 Python 2.x を使用している場合は、[*pip* (Python パッケージ管理システム) のインストールまたはアップグレード](https://pip.pypa.io/en/stable/installing/)が必要な場合があります。

    * Windows OS を使用している場合は、Python からネイティブ DLL を使用できる適切なバージョンの [Visual C++ 再配布可能パッケージ](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)があることを確認します。 最新バージョンを使用することをお勧めします。

    * 必要な場合、`pip install azure-iothub-device-client` コマンドを使用して [azure-iothub-device-client](https://pypi.org/project/azure-iothub-device-client/) パッケージをインストールします

    * 必要な場合、`pip install azure-iothub-service-client` コマンドを使用して [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) パッケージをインストールします

* **Mac OS**

    Mac OS の場合、Python 3.7.0 (または 2.7) + libboost-1.67 + curl 7.61.1 (すべて homebrew 経由でインストール) が必要です。 その他のディストリビューション/OS では、異なるバージョンの Boost および依存関係が埋め込まれる可能性があり、これらは動作せずに実行時に ImportError が発生します。

    `azure-iothub-service-client` と `azure-iothub-device-client` の *pip* パッケージは現在、Windows OS でのみ利用できます。 Linux/Mac OS については、[Python 用の開発環境の準備](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)に関する記事で、Linux と Mac OS の各セクションを参照してください。

> [!NOTE]
> サンプルの iothub_client をインポートするときに、いくつかのエラー レポートが表示されています。 **ImportError** の問題に対処する方法の詳細については、「[ImportError の問題の処理](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues)」を参照してください。
