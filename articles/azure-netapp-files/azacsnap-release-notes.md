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
ms.openlocfilehash: a580a135d2158234c84f3378b024997679ad6017
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778326"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool"></a>Azure アプリケーション整合性スナップショット ツールのリリース ノート

このページでは、新しい機能を提供したり欠陥を解決したりするために、AzAcSnap に加えられた主な変更を示します。

## <a name="aug-2021"></a>2021 年 8 月

### <a name="azacsnap-v502-build_2021082719086---patch-update-to-v501"></a>AzAcSnap v5.0.2 (Build_20210827.19086) - v5.0.1 に更新プログラムを適用

AzAcSnap v5.0.2 (Build_20210827.19086) は、次の修正と機能強化により、v5.0 ブランチに修正プログラムとして提供されています。

- `ssh` 255 終了コードを無視します。  場合によって、Azure Large Instances 上のストレージとの通信に使用される `ssh` コマンドによって、エラーまたは実行エラーが発生しなかったときに、255 という終了コードが生成されることがあります (`man ssh` "EXIT STATUS" を参照)。その後、AzAcSnap はこれを失敗としてトラップし、中止します。  この更新により、正しい実行を検証するための追加の検証が行われます。これには、従来の終了コードのチェックに加えて、エラーに対する `ssh` STDOUT と STDERR の解析が含まれます。
- インストーラーの hdbuserstore ソース パス チェックを修正します。  インストーラーは、インストールを実行しているユーザーの hdbuserstore に対し、誤ったソース ディレクトリが存在するかどうかを確認します。これは、`~/.hdb` のチェック対象として修正されています。  これは、`azacsnap` のインストール前に `root` ユーザーに対して hdbuserstore が事前に構成されているシステム (Azure Large Instances など) に適用されます。
- インストーラーによってインストールまたは抽出されるバージョンが、表示されるようになりました (インストーラーが引数なしで実行されている場合)。

インストーラーの[最新リリース](https://aka.ms/azacsnapinstaller)をダウンロードし、[作業の開始](azacsnap-get-started.md)方法を確認してください。

## <a name="may-2021"></a>2021年5月

### <a name="azacsnap-v501-build-2021052414837---patch-update-to-v50"></a>AzAcSnap v 5.0.1 (ビルド: 20210524.14837) ‐ バージョン 5.0 に更新プログラムを適用

AzAcSnap v 5.0.1 (ビルド: 20210524.14837) は、次の修正と機能強化により、v5.0 ブランチに修正プログラムとして提供されています。

- 終了コードの処理が改善されました。  場合によっては、0 以外の実行エラーが発生した場合でも、終了コード 0 (ゼロ) が出力されます。  終了コードは、`azacsnap` が正常に実行された場合は 0 になり、エラーが発生した場合は 0 以外になる必要があります。  また、AzAcSnap の内部エラー処理は、AzAcSnap によって実行される外部コマンド (hdbsql、ssh など) の終了コードをキャプチャして出力するように拡張されています (エラーの原因である場合)。

## <a name="april-2021"></a>2021 年 4 月

### <a name="azacsnap-v50-build-202104216349---ga-released-21-april-2021"></a>AzAcSnap v5.0 (ビルド: 20210421.6349) - GA リリース (2021 年 4 月 21 日)

AzAcSnap v5.0 (ビルド: 20210421.6349) が一般公開となり、このビルドで次の修正と改善が加えられました。

- (SAP HANA からの応答を待機するための) hdbsql の再試行タイムアウトは、競合状態を避けるために、"savePointAbortWaitSeconds" の半分に自動的に設定されます。  "savePointAbortWaitSeconds" の設定は、JSON 構成ファイル内で直接変更できます。この値は 600 秒以上にする必要があります。

## <a name="march-2021"></a>2021 年 3 月

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v5.0 Preview (Build:20210318.30771)

次の修正と機能強化を含む AcSnap v5.0 Preview (Build:20210318.30771) がリリースされました。

- SAP HANA テナント DB に AZACSNAP ユーザーを追加する必要がなくなりました。「[データベースとの通信を有効にする](azacsnap-installation.md#enable-communication-with-database)」セクションを参照してください。
- 手動による QOS で構成されたボリュームを使用した[復元](azacsnap-cmd-ref-restore.md)を許可するように修正しました。
- Azure Large Instances の SSH 接続を抑えるためのミューテックス制御を追加しました。
- スペースを含むパス名およびその他の関連する問題を処理するためにインストーラーを修正しました。
- 他のデータベース サーバーをサポートする準備のために、省略可能なパラメーター '--hanasid' を '--dbsid' に変更しました。

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション整合性スナップショット ツールの使用を開始する](azacsnap-get-started.md)
