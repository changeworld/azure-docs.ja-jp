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
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 269ef95835e5284806e7c1eaa76980e635c3d1dd
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/25/2021
ms.locfileid: "107946549"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool"></a>Azure アプリケーション整合性スナップショット ツールのリリース ノート

このページでは、新しい機能を提供したり欠陥を解決したりするために、AzAcSnap に加えられた主な変更を示します。

## <a name="april-2021"></a>2021 年 4 月

### <a name="azacsnap-v50-build-202104216349---ga-released-21-april-2021"></a>AzAcSnap v5.0 (ビルド: 20210421.6349) - GA リリース (2021 年 4 月 21 日)

AzAcSnap v5.0 (ビルド: 20210421.6349) が一般公開となり、このビルドで次の修正と改善が加えられました。

- (SAP HANA からの応答を待機するための) hdbsql の再試行タイムアウトは、競合状態を避けるために、"savePointAbortWaitSeconds" の半分に自動的に設定されます。  "savePointAbortWaitSeconds" の設定は、JSON 構成ファイル内で直接変更できます。この値は 600 秒以上にする必要があります。

インストーラーの[最新リリース](https://aka.ms/azacsnapdownload)をダウンロードし、[作業の開始](azacsnap-get-started.md)方法を確認してください。

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
