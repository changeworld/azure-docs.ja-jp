---
title: Microsoft Threat Modeling Tool のリリース 2020 年 3 月 22 日 - Azure
description: Threat Modeling Tool のリリース ノートです
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 03/22/2020
ms.openlocfilehash: 6fea4d48b62d6ea429d37924ffd15855db6294cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80137643"
---
# <a name="threat-modeling-tool-update-release-73003161---03222020"></a>Threat Modeling Tool 更新プログラム リリース 7.3.00316.1 - 2020 年 3 月 22 日

バージョン 7.3.00316.1 の Microsoft Threat Modeling Tool (TMT) が 2020 年 3 月 22 日にリリースされました。変更点は以下のとおりです。

- アクセシビリティの向上
- バグの修正
- 新しい DiagramReader 機能

## <a name="notable-bug-fixes"></a>注目すべきバグの修正

### <a name="exporting-the-threat-list-to-csv"></a>脅威リストの CSV へのエクスポート

CSV へのエクスポート機能では、脅威リストからエクスポートするフィールドの選択に一貫性がありませんでした。 現在は、脅威リストのすべてのフィールドが CSV ファイルにエクスポートされます。 

### <a name="ux-bugs"></a>UX バグ

- プライマリ ワークフロー (作成、オープン、分析) とテンプレート エディター エクスペリエンスのヘルプ メニューで、一貫したメニュー オプションが使用できるようになりました。
- ステンシル ペインの検索バーに標準カーソルが追加され、適切なラベルが追加されました。

## <a name="new-features"></a>新機能

### <a name="diagramreader-feature-has-been-added"></a>DiagramReader 機能が追加されました

モデルが開いている間のメイン メニューに新しい DiagramReader 機能が追加されました。 この機能により、モデルのグラフィック表示がテキストベースのナレーションに変換されます。 

## <a name="system-requirements"></a>システム要件

- サポートされているオペレーティング システム:
  - [Microsoft Windows 10 Anniversary Update](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 以降
- 必要な .NET のバージョン:
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 以降
- その他の要件:
  - ツールとテンプレートの更新プログラムを受け取るためのインターネット接続

## <a name="documentation-and-feedback"></a>ドキュメントとフィードバック

- Threat Modeling Tool のドキュメントは [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool) にあり、[ツールの使用について](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)の情報が含まれます。

## <a name="next-steps"></a>次のステップ

最新バージョンの [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool) をダウンロードしてください。
