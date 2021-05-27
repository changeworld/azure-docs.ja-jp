---
author: naiteeks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 04/20/2021
ms.author: juliako
ms.openlocfilehash: 1cce5d0f49604ce310e40de037b9a77e04615bc9
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371884"
---
デプロイには約 **20 分** かかります。 完了すると、次のような特定の Azure リソースが Azure サブスクリプションにデプロイされます。

1. **Video Analyzer アカウント** - この [クラウド サービス](../../overview.md)は、Video Analyzer エッジ モジュールの登録、録画されたビデオの再生、およびビデオ分析に使用されます。
1. **ストレージ アカウント** - 録画されたビデオとビデオ分析の格納用です。
1. **マネージド ID** - これは、上記のストレージ アカウントへのアクセスを管理するために使用されるユーザー割り当て[マネージド ID](../../../../active-directory/managed-identities-azure-resources/overview.md) です。
1. **仮想マシン** - これは、シミュレートされたエッジ デバイスとして機能する仮想マシンです。
1. **IoT Hub** - IoT アプリケーションと IoT Edge モジュール、さらにそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。

上で説明したリソースに加え、クイックスタートとチュートリアルで使用するために、次の項目もストレージ アカウントの 'deployment-output' ファイル共有に作成されます。

- **_appsettings.json_** - このファイルには、Visual Studio Code でサンプル アプリケーションを実行するために必要な **デバイス接続文字列** とその他のプロパティが含まれています。
- **_env.txt_** - このファイルには、Visual Studio Code を使用して配置マニフェストを生成するために必要な環境変数が含まれています。
- **_deployment.json_** - これは、シミュレートされたエッジ デバイスにエッジ モジュールをデプロイするためにテンプレートによって使用される、配置マニフェストです。

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

> [!TIP]
> 作成された Azure リソースで問題が発生した場合は、 [トラブルシューティング ガイド](../../troubleshoot.md) を参照して、よく発生する問題を解決してください。
