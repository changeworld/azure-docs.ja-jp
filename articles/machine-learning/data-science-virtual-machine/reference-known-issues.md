---
title: 'リファレンス: 既知の問題とトラブルシューティング'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Data Science Virtual Machine に関する既知の問題、回避策、トラブルシューティングの一覧を示します
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: d022b1124146a1e506401e6cee257805e3a38fd3
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526551"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine の既知の問題とトラブルシューティング

この記事は、Azure Data Science Virtual Machine の使用時に発生したエラーや障害を見つけて修正するのに役立ちます。

## <a name="python-package-installation-issues"></a>Python パッケージのインストールに関する問題

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>pip を使用してパッケージをインストールすると、Linux 上の依存関係が壊れる

パッケージをインストールする際、`pip install` ではなく、`sudo pip install` を使用してください。

## <a name="disk-encryption-issues"></a>ディスクの暗号化の問題

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM でディスクの暗号化が失敗する

Azure Disk Encryption (ADE) は、現在 Ubuntu DSVM ではサポートされていません。 回避策として、[カスタマー マネージド キーを使用して Azure Storage 暗号化](../../storage/common/storage-encryption-keys-portal.md)を構成することを検討してください。

## <a name="tool-appears-disabled"></a>ツールが無効に見える

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V が Windows DSVM で動作しない

起動パフォーマンスのために一部のサービスを無効にしているので、これは予期される動作です。 再度有効にするには、Windows DSVM の検索バーを開き、"サービス" と入力します。次に、すべての Hyper-V サービスを [手動] に設定し、[Hyper-V Virtual Machine Management] を [自動] に設定します。

最後の画面は、次のようになります。

   ![Hyper-V の有効化](./media/workaround/hyperv-enable-dsvm.png)

