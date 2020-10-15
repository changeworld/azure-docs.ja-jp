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
ms.openlocfilehash: 7246375468b3419c3d52ee3d5a51a95aa20050a9
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876659"
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

これらのチュートリアルでは、いくつかの構成ファイルを更新して使用します。 これらのファイルの初期バージョンは、[LVA-gateway](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub リポジトリで入手できます。 このリポジトリには、scratchpad というテキスト ファイルも用意されています。デプロイするサービスの構成値を記録する際に、ダウンロードしてご利用ください。

これらのファイルのコピーを保存するための *lva-configuration* というフォルダーをローカル コンピューターに作成します。 次の各リンクを右クリックし、 **[名前を付けて保存]** を選択して、*lva-configuration* フォルダーにファイルを保存します。
