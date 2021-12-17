---
title: Azure Percept の既知の問題
description: Azure Percept の既知の問題とその回避策について説明します
author: juniem
ms.author: amiyouss
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: 4d6bbc2396819b1eea7ac17f5c0da87055450927
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223968"
---
# <a name="azure-percept-known-issues"></a>Azure Percept の既知の問題

製品チームが認識している Azure Percept DK、Azure Percept Audio、または Azure Percept Studio に関する問題を次に示します。 可能な場合は、回避策とトラブルシューティングの手順について説明します。 これらの問題が操作の妨げになっている場合は、[Microsoft Q&A](/answers/topics/azure-percept.html) にそれを質問として投稿するか、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) でカスタマー サポート要求を送信してください。 

|領域|現象|問題の説明|回避策|
|-------|---------|---------|---------|
| Azure Percept DK | Azure Percept Studio でサンプルおよびデモ モデルをデプロイできない | azureeyemodule または azureearspeechmodule モジュールの実行が停止する場合があります。 edgeAgent のログに「シンボリック リンクのレベルが多すぎます」というエラーが表示されます。 | デバイスを [USB 経由で更新](./how-to-update-via-usb.md)してリセットします |
| ローカリゼーション | Azure Percept DK のセットアップ エクスペリエンスの一部で、英語を話さないユーザーに対して英語のテキストが表示される場合があります。 | Azure Percept DK のセットアップ エクスペリエンスは完全にはローカライズされていません。 | 修正は 2021 年 7 月に行われる予定です  |
| Azure Percept DK | Mac でセットアップ エクスペリエンスを実行すると、Wi-Fi への接続後にセットアップ エクスペリエンスが突然閉じる場合があります。 | Mac でセットアップ エクスペリエンスを実行すると、最初に Web ブラウザーではなくウィンドウに表示されます。 接続がデバイスのアクセスポイントから Wi-Fi に切り替わると、ウィンドウは持続しません。 | Web ブラウザーを開き、 https://10.1.1.1 にアクセスします。これにより、セットアップ エクスペリエンスを完了できます。 |
| Azure Percept DK | 開発キットでカスタム モデルを実行し、開発キットを再起動すると、既定のサンプル モデルが実行されます。 | カスタム モデルのモジュール ツイン コンテナーは、デバイスの再起動にまたがって保持されません。 再起動後、カスタム モジュールのモジュール ツインを再構築する必要があります。これには 5 分以上かかる場合があります。 そのプロセスが完了するまで、開発キットでは既定のモデルが実行されます。 | 再起動後、カスタム モジュール ツインが再作成されるまで待つ必要があります。 |