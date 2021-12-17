---
author: naiteeks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5a834a4c33ebbffcf4b335e7116b134164941231
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131861449"
---
デプロイには約 **20 分** かかります。 完了すると、次のような特定の Azure リソースが Azure サブスクリプションにデプロイされます。

1. **Video Analyzer アカウント** - この [クラウド サービス](../../../overview.md)は、Video Analyzer エッジ モジュールの登録、録画されたビデオの再生、およびビデオ分析に使用されます。
1. **ストレージ アカウント** - 録画されたビデオとビデオ分析の格納用です。
1. **マネージド ID** - これは、上記のストレージ アカウントへのアクセスを管理するために使用されるユーザー割り当て [マネージド ID](../../../../../active-directory/managed-identities-azure-resources/overview.md) です。
1. **仮想マシン** - これは、シミュレートされたエッジ デバイスとして機能する仮想マシンです。
1. **IoT Hub** - IoT アプリケーションと IoT Edge モジュール、さらにそれが管理するデバイスの間の双方向通信に対する中央メッセージ ハブとして機能します。

上で説明したリソースに加え、クイックスタートとチュートリアルで使用するために、次の項目もストレージ アカウントの 'deployment-output' ファイル共有に作成されます。

- **_appsettings.json_** - このファイルには、Visual Studio Code でサンプル アプリケーションを実行するために必要な **デバイス接続文字列** とその他のプロパティが含まれています。
- **_env.txt_** - このファイルには、Visual Studio Code を使用して配置マニフェストを生成するために必要な環境変数が含まれています。
- **_deployment.json_** - これは、シミュレートされたエッジ デバイスにエッジ モジュールをデプロイするためにテンプレートによって使用される、配置マニフェストです。

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

> [!TIP]
> 必要な Azure リソースすべてを作成するときに問題が発生する場合は、この[クイックスタート](../../get-started-detect-motion-emit-events-portal.md)の手動ステップに従ってください。
