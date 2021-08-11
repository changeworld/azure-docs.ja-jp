---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールのリリース ノート | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合スナップショット ツールのリリース ノートを提供します。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: phjensen
ms.openlocfilehash: fd8d4e1bfed60aa8f3eae4d4d3c033247ab1268d
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579908"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool"></a>Azure アプリケーション整合性スナップショット ツールのリリース ノート

このページでは、新しい機能を提供したり欠陥を解決したりするために、AzAcSnap に加えられた主な変更を示します。

## <a name="may-2021"></a>2021年5月

### <a name="azacsnap-v501-build-2021052414837---patch-update-to-v50"></a>AzAcSnap v 5.0.1 (ビルド: 20210524.14837) ‐ バージョン 5.0 に更新プログラムを適用

AzAcSnap v 5.0.1 (ビルド: 20210524.14837) は、次の修正と機能強化により、v5.0 ブランチに修正プログラムとして提供されています。

- 終了コードの処理が改善されました。  場合によっては、0 以外の実行エラーが発生した場合でも、終了コード 0 (ゼロ) が出力されます。  終了コードは、`azacsnap` が正常に実行された場合は 0 になり、エラーが発生した場合は 0 以外になる必要があります。  また、AzAcSnap の内部エラー処理は、AzAcSnap によって実行される外部コマンド (hdbsql、ssh など) の終了コードをキャプチャして出力するように拡張されています (エラーの原因である場合)。

インストーラーの[最新リリース](https://aka.ms/azacsnapdownload)をダウンロードし、[作業の開始](azacsnap-get-started.md)方法を確認してください。

## <a name="april-2021"></a>2021 年 4 月

### <a name="azacsnap-v50-build-202104216349---ga-released-21-april-2021"></a>AzAcSnap v5.0 (ビルド: 20210421.6349) - GA リリース (2021 年 4 月 21 日)

AzAcSnap v5.0 (ビルド: 20210421.6349) が一般公開となり、このビルドで次の修正と改善が加えられました。

- (SAP HANA からの応答を待機するための) hdbsql の再試行タイムアウトは、競合状態を避けるために、"savePointAbortWaitSeconds" の半分に自動的に設定されます。  "savePointAbortWaitSeconds" の設定は、JSON 構成ファイル内で直接変更できます。この値は 600 秒以上にする必要があります。

## <a name="march-2021"></a>2021 年 3 月

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v5.0 Preview (Build:20210318.30771)

次の修正と機能強化を含む AcSnap v5.0 Preview (Build:20210318.30771) がリリースされました。

- SAP HANA テナント DＢ に AZACSNAP ユーザーを追加する必要がなくなりました。「[SAP HANA との通信を有効にする](azacsnap-installation.md#enable-communication-with-sap-hana)」セクションを参照してください。
- 手動による QOS で構成されたボリュームを使用した[復元](azacsnap-cmd-ref-restore.md)を許可するように修正しました。
- Azure Large Instances の SSH 接続を抑えるためのミューテックス制御を追加しました。
- スペースを含むパス名およびその他の関連する問題を処理するためにインストーラーを修正しました。
- 他のデータベース サーバーをサポートする準備のために、省略可能なパラメーター '--hanasid' を '--dbsid' に変更しました。

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション整合性スナップショット ツールの使用を開始する](azacsnap-get-started.md)
