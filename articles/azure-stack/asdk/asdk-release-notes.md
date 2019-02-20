---
title: Microsoft Azure Stack Development Kit のリリース ノート | Microsoft Docs
description: Azure Stack Development Kit の機能強化、修正、既知の問題。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 485d88a4765d7cedcb171a5b325fe5f366fff1f9
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004765"
---
# <a name="asdk-release-notes"></a>ASDK リリース ノート

この記事では、Azure Stack Development Kit (ASDK) の変更、修正、および既知の問題に関する情報を提供します。 実行しているバージョンが不明な場合は、[ポータルを使用して確認](../azure-stack-updates.md#determine-the-current-version)できます。

[![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [フィード](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#)をサブスクライブして、ASDK の新着情報を常に把握するようにしてください。

## <a name="build-11901095"></a>ビルド 1.1901.0.95

### <a name="changes"></a>変更点

このビルドには、Azure Stack に対する次の機能強化が含まれています。

- 物理ホストに BGP および NAT コンポーネントがデプロイされるようになりました。 これにより、ASDK をデプロイするために 2 つのパブリックまたは企業 IP アドレスを持つ必要がなくなり、デプロイも簡略化されます。
- **asdk-installer.ps1** PowerShell スクリプトを使用して、Azure Stack 統合システムのバックアップを[検証](asdk-validate-backup.md)できるようになりました。

### <a name="new-features"></a>新機能

- このリリースでの新機能の一覧については、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1901.md#new-features)を参照してください。

### <a name="fixed-and-known-issues"></a>修正された問題と既知の問題

- このリリースで修正された問題の一覧については、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1901.md#fixed-issues)を参照してください。 既知の問題の一覧については、[このセクション](../azure-stack-update-1901.md#known-issues-post-installation)を参照してください。
- [使用可能な Azure Stack 修正プログラム](../azure-stack-update-1901.md#azure-stack-hotfixes)が Azure Stack ASDK には適用できないことに注意してください。

## <a name="build-118110101"></a>ビルド 1.1811.0.101

### <a name="changes"></a>変更点

このビルドには、Azure Stack に対する次の機能強化が含まれています。  

- ASDK に使用するハードウェアとソフトウェアに関して、新しい最小要件と推奨要件が存在します。 そうした新しい推奨仕様は、「[Azure Stack のデプロイ計画に関する考慮事項](asdk-deploy-considerations.md)」に記載されています。 Azure Stack プラットフォームの進化に合わせて、提供されるサービスが増え、必要なリソースも増えていくと考えられます。 推奨情報の改訂は、こうした仕様の高まりを反映したものです。

### <a name="new-features"></a>新機能

このリリースでの新機能の一覧については、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1811.md#new-features)を参照してください。

### <a name="fixed-and-known-issues"></a>修正された問題と既知の問題

このリリースで修正された問題の一覧については、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1811.md#fixed-issues)を参照してください。 このリリースでの既知の問題の一覧については、[このセクション](../azure-stack-update-1811.md#known-issues-post-installation)を参照してください。

## <a name="build-11809090"></a>ビルド 1.1809.0.90

### <a name="new-features"></a>新機能

このリリースでの新機能の一覧については、Azure Stack リリース ノートの[このセクション](../azure-stack-update-1809.md#new-features)を参照してください。

### <a name="fixed-issues"></a>修正された問題

このリリースで修正された問題の一覧については、[このセクション](../azure-stack-update-1809.md#fixed-issues)を参照してください。

### <a name="known-issues"></a>既知の問題

このリリースでの既知の問題の一覧については、[このセクション](../azure-stack-update-1809.md#known-issues-post-installation)を参照してください。