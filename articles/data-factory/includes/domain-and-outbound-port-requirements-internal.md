---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 45c6bbb88ef1f01f729451af27ecc73244483216
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121676"
---
| ドメイン名                                          | 送信ポート | 説明                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| パブリック クラウド: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> 中国: `*.servicebus.chinacloudapi.cn`   | 443            | セルフホステッド統合ランタイムがインタラクティブな作成を行うために必要です。 |
| パブリック クラウド: `{datafactory}.{region}.datafactory.azure.net`<br> または `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> 中国: `{datafactory}.{region}.datafactory.azure.cn` | 443            | セルフホステッド統合ランタイムが Data Factory サービスに接続するために必要です。 <br>パブリック クラウドに新しく作成された Data Factory の場合は、{datafactory}.{region}.datafactory.azure.net という形式のセルフホステッド統合ランタイム キーから FQDN を見つけてください。 古いデータ ファクトリの場合、セルフホステッド統合キーに FQDN が表示されないときは、代わりに *.frontend.clouddatahub.net を使用してください。 |
| `download.microsoft.com`    | 443            | セルフホステッド統合ランタイムが更新プログラムをダウンロードするために必要です。 自動更新を無効にしている場合は、このドメインの構成をスキップできます。 |
| Key Vault の URL | 443           | Key Vault に資格情報を格納する場合は、Azure Key Vault に必要です。 |
