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
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111396"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールのリリース ノート (プレビュー)

このページでは、新しい機能を提供したり欠陥を解決したりするために、AzAcSnap に加えられた主な変更を示します。

## <a name="march-2021"></a>2021 年 3 月

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v5.0 Preview (Build:20210318.30771)

次の修正と機能強化を含む AcSnap v5.0 Preview (Build:20210318.30771) がリリースされました。

- SAP HANA テナント DＢ に AZACSNAP ユーザーを追加する必要がなくなりました。「[SAP HANA との通信を有効にする](azacsnap-installation.md#enable-communication-with-sap-hana)」セクションを参照してください。
- 手動による QOS で構成されたボリュームを使用した[復元](azacsnap-cmd-ref-restore.md)を許可するように修正しました。
- Azure Large Instances の SSH 接続を抑えるためのミューテックス制御を追加しました。
- スペースを含むパス名およびその他の関連する問題を処理するためにインストーラーを修正しました。
- 他のデータベース サーバーをサポートする準備のために、省略可能なパラメーター '--hanasid' を '--dbsid' に変更しました。

インストーラーの[最新リリース](https://aka.ms/azacsnapdownload)をダウンロードし、[作業の開始](azacsnap-get-started.md)方法を確認してください。

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション整合性スナップショット ツールの使用を開始する](azacsnap-get-started.md)
