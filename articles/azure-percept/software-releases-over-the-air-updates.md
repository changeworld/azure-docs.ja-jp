---
title: Azure Percept DK の OTA 更新のためのソフトウェア リリース
description: Azure Percept DK の無線更新プログラム パッケージの情報とダウンロード リンク
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: 1eb6f41d738f7ab5a2dbd68fab341c2b7c9917ad
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "130001165"
---
# <a name="software-releases-for-ota-updates"></a>OTA 更新プログラムのソフトウェア リリース

OTA 更新は、開発キットを常に最新の状態に保ちたいユーザー向けです。 そのため、ここではハードストップ バージョンと最新バージョンのみが提供されています。 開発キットを特定の (旧) バージョンに変更するには、USB ケーブル経由の更新を使用します。 詳細は、「[USB-C ケーブル接続で Azure Percept DK を更新する](./how-to-update-via-usb.md)」を参照してください。 また、大幅に上位のバージョンにジャンプする場合も USB 経由の更新を使用してください。

>[!CAUTION]
>開発キットでは、OTA による SW バージョンのダウングレードはサポートされていません。 Device Update for IoT Hub フレームワークでは、現在のバージョンより古いバージョンのイメージのデプロイがブロックされません。 ただし、開発キットに対してこれを行うと、データと機能が失われます。

>[!IMPORTANT]
>OTA と USB ケーブル経由のどちらで更新するかを決めるにあたっては、必ず次のドキュメントを確認してください。
>
>[更新戦略を決定する方法](./how-to-determine-your-update-strategy.md)

## <a name="hard-stop-version-of-ota"></a>OTA のハードストップ バージョン

Microsoft は、開発キットの各リリースに OTA パッケージを提供したいと考えています。 しかし、開発キットの OS やファームウェア、または OTA プラットフォームに破壊的変更が発生した場合は、古いバージョンから大幅に上位のバージョンに直接 OTA を実施すると問題が起こる可能性があります。 Microsoft では、破壊的変更が発生した場合には通常、OTA 更新プロセスにより古いシステムが **この破壊的変更が導入または提供された最初のバージョン** にシームレスに移行されるようにしています。 この特定のバージョンが OTA のハードストップ バージョンになります。 例として、既知のハードストップ バージョンである **6 月リリース** を取り上げます。 ユーザーが開発キットを 2104 から 2106 に更新し、2106 から 2107 に更新するのであれば、OTA が機能します。 これに対して、ユーザーがハードストップ (2106) をスキップし、開発キットを 2104 から 2107 に直接更新しようとした場合には、機能しません。

:::image type="content" source="./media/azure-percept-devkit-software-releases-ota-update/hard-stop-illustrate.png" alt-text="OTA のハードストップ バージョン":::

## <a name="recommendations-for-applying-the-ota-update"></a>OTA 更新の適用に関する推奨事項

**シナリオ 1:** 開発キットを常に最新状態に保つために頻繁に (毎月) 更新する

- 常に OTA 更新プログラムを実行して開発キットを前回のリリースから新しくリリースされたバージョンに更新している場合は、問題はありません。

**シナリオ 2:** いくつかのバージョンがスキップされる可能性がある状況で更新を行う。

1. 開発キットの現在のソフトウェア バージョンを特定します。
1. OTA パッケージのリリース一覧で、現在のバージョンとターゲット バージョンの間にハードストップ バージョンがあるかどうかを調べます。
    - ある場合は、最新の更新プログラム パッケージをデプロイできるようになるまで、ハードストップ バージョンを順番にデプロイする必要があります。
    - ない場合は、最新の OTA パッケージを開発キットに直接デプロイできます。

## <a name="identify-the-current-software-version-of-dev-kit"></a>開発キットの現在のソフトウェア バージョンを特定する

**オプション 1:**

1. [Azure Percept Studio](./overview-azure-percept-studio.md) にサインインします。
1. **[デバイス]** で、お使いの開発キット デバイスを選択します。
1. **[全般]** タブで、**モデル** と **SW バージョン** の情報を探します。

**オプション 2:**

1. Microsoft Azure portal から **IoT Hub** サービスの **IoT Edge デバイス** を表示します。
1. デバイスの一覧からお使いの開発キット デバイスを選択します。
1. **[デバイス ツイン]** を選択します。
1. デバイス ツインのプロパティをスクロールし、 **"deviceInformation"** の下にある **"model"** と **"swVersion"** を見つけて、その値をメモします。

## <a name="identify-the-ota-packages-to-be-deployed"></a>デプロイする OTA パッケージを特定する

>[!IMPORTANT]
>お使いの開発キットの現在のバージョンが以下のリリースに含まれていない場合、それは OTA 更新ではサポートされません。 最新バージョンを取得するには、USB ケーブル経由の更新を行ってください。

**最新リリース:**

|Release|適用可能なバージョン|ダウンロード リンク|Note|
|---|---|---|---|
|9 月のサービス リリース (2109)|2021.106.111.115,<br>2021.107.129.116|[2021.109.129.108 OTA 更新プログラム パッケージ](https://go.microsoft.com/fwlink/?linkid=2174634)||

**ハードストップ リリース:**

|Release|適用可能なバージョン|ダウンロード リンク|メモ|
|---|---|---|---|
|6 月のサービス リリース (2106)|2021.102.108.112、2021.104.110.103、2021.105.111.122 |[2021.106.111.115 OTA マニフェスト (PE-101 用)](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/aduimportmanifest_PE-101_2021.106.111.115_v3.json)<br>[2021.106.111.115 OTA マニフェスト (APDK-101 用)](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/aduimportmanifest_Azure-Percept-DK_2021.106.111.115_v3.json) <br>[2021.106.111.115 OTA 更新プログラム パッケージ](https://download.microsoft.com/download/d/f/0/df0f17dc-d2fb-42ff-aaa5-98edf4d6d1e8/Microsoft-Azure-Percept-DK-2021.106.111.115.swu) |"モデル名" (PE-101、APDK-101) に基づいて適切なマニフェストを使用してください|

## <a name="next-steps"></a>次のステップ

[Azure Percept DK を無線 (OTA) で更新する](./how-to-update-over-the-air.md)
