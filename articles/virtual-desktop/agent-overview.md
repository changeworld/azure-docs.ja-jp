---
title: Windows Virtual Desktop エージェントの概要
description: Windows Virtual Desktop エージェントと更新プロセスの概要。
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 371cc78f3ebad638008f4195f164b66a64948c65
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504551"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Windows Virtual Desktop エージェントの概要

Windows Virtual Desktop サービス フレームワークには、リモート デスクトップ クライアント、サービス、仮想マシンという 3 つの主要コンポーネントがあります。 この仮想マシンは、Windows Virtual Desktop エージェントとエージェント ブートローダーがインストールされている顧客サブスクリプションに存在します。 エージェントは、サービスと仮想マシンの間の中間コミュニケーターとして機能し、接続を可能にします。 そのため、エージェントのインストール、更新、または構成で問題が発生した場合、仮想マシンはサービスに接続できません。 エージェント ブートローダーは、エージェントを読み込む実行可能ファイルです。 

この記事では、エージェントのインストールと更新のプロセスの概要を示します。

>[!NOTE]
>このドキュメントは、FSLogix エージェントやリモート デスクトップ クライアント エージェントを対象としたものではありません。


## <a name="initial-installation-process"></a>初期インストール プロセス

Windows Virtual Desktop エージェントは、2 つの方法のいずれかで初期インストールされます。 Azure portal と Azure Marketplace で仮想マシン (VM) をプロビジョニングすると、エージェントとエージェント ブートローダーが自動的にインストールされます。 PowerShell を使用して VM をプロビジョニングする場合、[PowerShell で Windows Virtual Desktop ホスト プールを作成する](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)ときに、エージェントとエージェント ブートローダーの .msi ファイルを手動でダウンロードする必要があります。 エージェントがインストールされると、Windows Virtual Desktop サイドバイサイド スタックと Geneva Monitoring エージェントがインストールされます。 このサイドバイサイド スタック コンポーネントは、ユーザーがサーバーからクライアントへの逆方向の接続を安全に確立するために必要です。 Geneva Monitoring エージェントは、エージェントの正常性を監視します。 この 3 つのコンポーネントは、エンドツーエンドのユーザー接続を正常に機能させるためにどれも不可欠です。

>[!IMPORTANT]
>Windows Virtual Desktop エージェント、サイドバイサイド スタック、Geneva Monitoring エージェントを正常にインストールするために、[[必要な URL リスト]](safe-url-list.md#virtual-machines) にリストされているすべての URL のブロックを解除する必要があります。 これらの URL のブロックの解除は、Windows Virtual Desktop サービスを使用するために必須です。

## <a name="agent-update-process"></a>エージェントの更新プロセス

更新プログラムが利用可能になるたびに、Windows Virtual Desktop サービスによってエージェントが更新されます。 エージェントの更新には、新しい機能や以前の問題の修正を含めることができます。 Windows Virtual Desktop エージェントの初期バージョンがインストールされると、エージェントは Windows Virtual Desktop サービスに対してクエリを定期的に実行して、エージェント、スタック、または監視コンポーネントの使用可能な新しいバージョンがないか確認します。 いずれかのコンポーネントの新しいバージョンが既にデプロイされている場合は、更新されたコンポーネントが自動的にインストールされます。

エージェントの新しいバージョンは、1 週間の期間内に一定間隔ですべての Azure サブスクリプションにデプロイされます。 これらの更新期間は "フライト" と呼ばれます。 フライトが発生した場合、ホスト プール内の VM では異なるタイミングでエージェントの更新を受け取ることがあります。 すべてのサブスクリプション内のすべての VM エージェントは、デプロイ期間の終了までに更新されます。 Windows Virtual Desktop のフライト システムでは、エージェントの更新における安定性と品質を確保することによって、サービスの信頼性を向上させます。


>[!NOTE]
>ホスト プール内の VM は異なるタイミングでエージェントの更新を受け取る可能性があるため、フライトの問題とエージェントの更新の失敗の違いを理解できる必要があります。 **[イベント ビューアー]**  >  **[Windows ログ]**  >  **[アプリケーション]** で VM のイベント ログにアクセスし、"ID 3277" というラベルのイベントが表示される場合は、エージェントの更新が機能しなかったことを意味します。 このイベントが表示されない場合、VM は別のフライトにあり、後で更新されます。
>- Geneva Monitoring エージェントが最新バージョンに更新されると、新しい Monitoring エージェントの新しいタスクを作成する前に、古い Geneva タスクが検出され、無効になります。 最新バージョンの Monitoring エージェントに問題があり、修正のために以前のバージョンに戻す必要がある場合に備えて、以前のバージョンの Monitoring エージェントは削除されません。 最新バージョンに問題がある場合は、古い Monitoring エージェントが再び有効になり、監視データの配信が続行されます。 更新前にインストールした最後のモニターよりも古いすべてのバージョンのモニターが VM から削除されます。
>- VM では、同時に 3 つのバージョンのサイドバイサイド スタックが保持されます。 これにより、更新プログラムで問題が発生した場合にすばやく回復できます。 スタックが更新されるたびに、最も古いバージョンのスタックが VM から削除されます。

この更新プログラムのインストールは、通常、新しい VM では 2 分から 3 分間かかるため、VM が接続を失うことや、シャットダウンされることはありません。 この更新プロセスは、Windows Virtual Desktop (クラシック) と最新バージョンの Windows Virtual Desktop with Azure Resource Manager の両方に適用されます。

## <a name="next-steps"></a>次の手順

Windows Virtual Desktop エージェントについて理解を深めたので、こちらのリソースが役に立ちます。

- エージェントまたは接続に関連する問題が発生した場合は、[Windows Virtual Desktop エージェントの問題のトラブルシューティング ガイド](troubleshoot-agent.md)に関する記事を参照してください。
