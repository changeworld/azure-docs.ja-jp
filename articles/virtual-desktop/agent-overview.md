---
title: Windows Virtual Desktop エージェントの概要
description: Windows Virtual Desktop エージェントと更新プロセスの概要。
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 325502255e84e38a39ca5b90ee4126354c0d425b
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601233"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Windows Virtual Desktop エージェントの概要

Windows Virtual Desktop サービス フレームワークには、リモート デスクトップ クライアント、サービス、仮想マシンという 3 つの主要コンポーネントがあります。 この仮想マシンは、Windows Virtual Desktop エージェントとエージェント ブートローダーがインストールされている顧客サブスクリプションに存在します。 エージェントは、サービスと仮想マシンの間の中間コミュニケーターとして機能し、接続を可能にします。 そのため、エージェントのインストール、更新、または構成で問題が発生した場合、仮想マシンはサービスに接続できません。 エージェント ブートローダーは、エージェントを読み込む実行可能ファイルです。 

この記事では、エージェントのインストールと更新のプロセスの概要を示します。

>[!NOTE]
>このドキュメントは、FSLogix エージェントやリモート デスクトップ クライアント エージェントを対象としたものではありません。


## <a name="initial-installation-process"></a>初期インストール プロセス

Windows Virtual Desktop エージェントは、2 つの方法のいずれかで初期インストールされます。 Azure portal と Azure Marketplace で仮想マシン (VM) をプロビジョニングすると、エージェントとエージェント ブートローダーが自動的にインストールされます。 PowerShell を使用して VM をプロビジョニングする場合、[PowerShell で Windows Virtual Desktop ホスト プールを作成する](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)ときに、エージェントとエージェント ブートローダーの .msi ファイルを手動でダウンロードする必要があります。 エージェントがインストールされると、Windows Virtual Desktop サイドバイサイド スタックと Geneva Monitoring エージェントも同時にインストールされます。 このサイドバイサイド スタック コンポーネントは、ユーザーがサーバーからクライアントへの逆方向の接続を安全に確立するために必要です。 Geneva Monitoring エージェントは、エージェントの正常性を監視します。 この 3 つのコンポーネントは、エンドツーエンドのユーザー接続を正常に機能させるためにどれも不可欠です。

>[!IMPORTANT]
>Windows Virtual Desktop エージェント、サイドバイサイド スタック、Geneva Monitoring エージェントを正常にインストールするために、[[必要な URL リスト]](safe-url-list.md#virtual-machines) にリストされているすべての URL のブロックを解除する必要があります。 これらの URL のブロックの解除は、Windows Virtual Desktop サービスを使用するために必須です。

## <a name="agent-update-process"></a>エージェントの更新プロセス

更新プログラムが利用可能になるたびに、Windows Virtual Desktop サービスによってエージェントが自動的に更新されます。 エージェントの更新によって、新しい機能を組み込むことや、以前の問題を修正することができます。 Windows Virtual Desktop エージェントの初期バージョンがインストールされると、そのエージェントは Windows Virtual Desktop サービスに対してクエリを定期的に実行して、エージェントとそのコンポーネントの使用可能な新しいバージョンがないか確認します。 新しいバージョンがある場合は、エージェント ブートローダーによって、エージェント、サイドバイサイド スタック、Geneva Monitoring エージェントの最新バージョンが自動的にダウンロードされます。

>[!NOTE]
>- Geneva Monitoring エージェントが最新バージョンに更新されると、新しい Monitoring エージェントの新しいタスクを作成する前に、古い Geneva タスクが検出され、無効になります。 最新バージョンの Monitoring エージェントに問題があり、修正のために以前のバージョンに戻す必要がある場合に備えて、以前のバージョンの Monitoring エージェントは削除されません。 最新バージョンに問題がある場合は、古い Monitoring エージェントが再び有効になり、監視データの配信が続行されます。 更新前にインストールした最後のモニターよりも古いすべてのバージョンのモニターが VM から削除されます。
>- VM では、同時に 3 つのバージョンのサイドバイサイド スタックが保持されます。 これにより、更新プログラムで問題が発生した場合にすばやく回復できます。 スタックが更新されるたびに、最も古いバージョンのスタックが VM から削除されます。

この更新プログラムのインストールは、通常、新しい VM では 2 分から 3 分間かかるため、VM が接続を失うことや、シャットダウンされることはありません。 この更新プロセスは、Windows Virtual Desktop (クラシック) と最新バージョンの Windows Virtual Desktop with Azure Resource Manager の両方に適用されます。

## <a name="next-steps"></a>次の手順

Windows Virtual Desktop エージェントについて理解を深めたので、こちらのリソースが役に立ちます。

- エージェントまたは接続に関連する問題が発生した場合は、[Windows Virtual Desktop エージェントの問題のトラブルシューティング ガイド](troubleshoot-agent.md)に関する記事を参照してください。