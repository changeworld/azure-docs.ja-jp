---
title: Azure Percept DK ソフトウェアのリリース ノート
description: Azure Percept DK ソフトウェアに加えられた変更に関する情報。
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: f957ad88d89b9d31ab8e073214e765dbbdb13094
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224924"
---
# <a name="azure-percept-dk-software-release-notes"></a>Azure Percept DK ソフトウェアのリリース ノート

このページでは、Azure Percept DK OS とファームウェアの各リリースの変更点と修正内容について説明します。

更新プログラムのイメージをダウンロードするには、[USB ケーブル経由の更新用 Azure Percept DK ソフトウェア リリース](./software-releases-usb-cable-updates.md)または [OTA 経由の更新用 Azure Percept DK ソフトウェア リリース](./software-releases-over-the-air-updates.md)に関するページを参照してください。

## <a name="july-2107-release"></a>7 月 (2107) リリース

> [!IMPORTANT]
> コード署名の変更のため、このリリースの OTA (無線) パッケージと互換性があるのは、2106 リリースを実行している Azure Percept DK のみです。 それよりも前の SW リリース バージョンを現在実行している Azure Percept DK ユーザーについては、USB ケーブル経由で更新するか、または最初に OTA でリリース 2106 に更新してから 2107 に更新することをお勧めします。

- Wi-Fi:
  - セットアップの完了後は確実に Wi-Fi アクセス ポイントがシャットダウンされるようセキュリティを強化しました。
  - 開発キットの **[Setup]\(セットアップ\)** ボタンを押すと開発キットの Wi-Fi アクセス ポイントとセットアップ エクスペリエンス Web サービスとの同期が失われる問題を修正しました。
  - Wi-Fi アクセス ポイントの iptable ルールを強化して回復性を高め、不要なルールは削除しました。
  - 接続された複数の Wi-Fi ネットワークが適切に優先順位付けされない問題を修正しました。
- セットアップ エクスペリエンス:
  - サポート対象リージョンのローカライズを追加し、テキストを更新して読みやすくしました。
  - 読み込み中のページでセットアップ エクスペリエンスが固まることがある問題を修正しました。
- ネットワーク全般:
  - IPv6 で有効な DHCP リースが取得されない問題を解決しました。
- オペレーティング システム:
  - セキュリティの修正。

## <a name="june-2106-release"></a>6 月 (2106) リリース

- OTA エージェントのイメージ検証メカニズムを更新しました。
- UI を改良し、セットアップ エクスペリエンスのバグを修正しました。

## <a name="may-2105-release"></a>5 月 (2105) リリース

- CBL-Mariner OS のセキュリティ更新プログラム。

## <a name="april-2104-release"></a>4 月 (2104) リリース

- Azure Percept DK ストレージがいっぱいになるログのローテーションの問題を修正しました。
- セットアップ エクスペリエンスでの Azure への TPM ベースのプロビジョニングを有効にしました。
- セットアップ エクスペリエンスおよび Wi-Fi アクセスポイントに自動タイムアウトを追加しました (30 分後またはセットアップ エクスペリエンス完了後)。
- Wi-Fi アクセス ポイントの SSID を "**scz-[xxx]** " から "**apd-[xxx]** " に変更しました。

## <a name="next-steps"></a>次のステップ

- [更新戦略を決定する方法](./how-to-determine-your-update-strategy.md)
