---
title: Azure Monitor for Azure Virtual Desktop の最新情報
description: Azure Virtual Desktop エージェントの新機能と製品の更新情報。
author: Heidilohr
ms.topic: reference
ms.date: 07/09/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 000fb5a26c34a8fbb9013ad873aa167aa759ae7b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450879"
---
# <a name="whats-new-in-azure-monitor-for-azure-virtual-desktop"></a>Azure Monitor for Azure Virtual Desktop の最新情報

この記事では、Azure Monitor for Azure Virtual Desktop の各新バージョンでの変更点について説明します。

現在使用している Azure Monitor のバージョンがわからない場合は、[分析情報] ページまたは構成ブックの右下で確認できます。 ブックにアクセスするには、[https://aka.ms/azmonwvdi](https://aka.ms/azmonwvdi) に移動します。

## <a name="how-to-read-version-numbers"></a>バージョン番号の読み方

Azure Monitor for Azure Virtual Desktop の各バージョンには、3 つの番号があります。 各番号の意味は次のとおりです。

- 1 つ目の番号はメジャー バージョンであり、通常はメジャー リリースに使用されます。

- 2 つ目の番号は、マイナー バージョンです。 マイナー バージョンは、新機能や非推奨通知などの下位互換性のある変更のためのものです。

- 3 つ目の番号はパッチ バージョンであり、正しくない動作やバグを修正する軽微な変更に使用されます。

たとえば、バージョン番号が 1.2.31 のリリースは、1 つ目のメジャー リリース、2 つ目のマイナー リリース、パッチ番号 31 です。

いずれかの番号を増やす場合、それ以降の番号もすべて変更する必要があります。 1 つのリリースには 1 つのバージョン番号があります。 ただし、すべてのバージョン番号がリリースを追跡しているわけではありません。 たとえば、パッチ番号はいくぶん任意に付けることができます。

## <a name="version-100"></a>バージョン 1.0.0

リリース日: 2021 年 3 月 21 日。

このバージョンでは、次の変更を行いました。

- ホスト診断ページで、Azure Virtual Desktop エージェントのイベント ログから影響の大きいエラーと警告を視覚的に表示する機能を新たに導入しました。

- 既定の構成から、5 つのコストの高いプロセス パフォーマンス カウンターを削除しました。 詳細については、[Azure Monitor for Azure Virtual Desktop の新しいガイダンス](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/updated-guidance-on-azure-monitor-for-wvd/m-p/2236173)のブログ記事を参照してください。

- 構成ブックの Windows イベント ログの設定プロセスが自動化されました。

- 構成ブックは、推奨される Windows イベント ログの自動デプロイをサポートするようになりました。

- 構成ブックで、リソース グループのリージョン以外のセッション ホストに対して、Log Analytics エージェントと設定優先ワークスペースをインストールできるようになりました。

- 構成ブックに、設定プロセスのタブ形式レイアウトが追加されました。

- この更新プログラムでバージョン管理を導入しました。

## <a name="next-steps"></a>次のステップ

一般的な最新情報ページについては、「[Azure Virtual Desktop の最新情報](whats-new.md)」を参照してください。

Azure Monitor for Azure Virtual Desktop の詳細については、「[Azure Monitor for Azure Virtual Desktop を使用してデプロイを監視する](azure-monitor.md)」を参照してください。
