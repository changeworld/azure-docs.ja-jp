---
title: インクルード ファイル
description: インクルード ファイル
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2f3e4bf640b8da31a7fa4d818b94b0372d3026b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763443"
---
サンプル アプリケーションには、シミュレートされたデバイスが 2 つと、IoT Edge ゲートウェイが 1 つ含まれています。 ゲートウェイの機能を試して理解するために、以降のチュートリアルでは次の 2 つのアプローチを紹介しています。

* IoT Edge ゲートウェイを Azure VM に作成して、シミュレートされたカメラを接続する。
* 実デバイス (Intel NUC など) に IoT Edge ゲートウェイを作成し、実際のカメラを接続する。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure IoT Central のビデオ分析アプリケーション テンプレートを使用して小売店アプリケーションを作成する
> * アプリケーションの設定をカスタマイズする
> * IoT Edge ゲートウェイ デバイス用のデバイス テンプレートを作成する
> * IoT Central アプリケーションにゲートウェイ デバイスを追加する

## <a name="prerequisites"></a>前提条件

このチュートリアル シリーズを完了するには、以下が必要です。

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。
* 実際のカメラを使用している場合、IoT Edge デバイスとカメラとの間に接続が必要です。また、**リアル タイム ストリーミング プロトコル** チャンネルも必要となります。

## <a name="initial-setup"></a>初期セットアップ

これらのチュートリアルでは、いくつかの構成ファイルを更新して使用します。 これらのファイルの初期バージョンは、[LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub リポジトリで入手できます。 このリポジトリには、[scratchpad](https://github.com/Azure/live-video-analytics/blob/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) というテキスト ファイルが含まれています。デプロイするサービスの構成値を記録する際に、ダウンロードしてご利用ください。 このファイルは、チュートリアルの後の手順を完了するのに役立ちます。

これらのファイルのコピーを保存するための *lva-configuration* というフォルダーをローカル コンピューターに作成します。 次の各リンクを右クリックし、 **[名前を付けて保存]** を選択して、*lva-configuration* フォルダーにファイルを保存します。
