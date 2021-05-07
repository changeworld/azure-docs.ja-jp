---
title: StorSimple 8000 シリーズ Update 5.1 リリース ノート
description: StorSimple 8000 シリーズ Update 5.1 における新機能、問題、回避策について説明します。
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: alkohli
ms.openlocfilehash: 735d7c1a25acb767f9d6352f6c8887af4b9464c0
ms.sourcegitcommit: 19dcad80aa7df4d288d40dc28cb0a5157b401ac4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2021
ms.locfileid: "107895921"
---
# <a name="storsimple-8000-series-update-51-release-notes"></a>StorSimple 8000 シリーズ Update 5.1 リリース ノート

## <a name="overview"></a>概要

次のリリース ノートでは、StorSimple 8000 シリーズの Update 5.1 の新機能について説明し、未解決の重要な問題を示します。 また、このリリースに含まれる StorSimple のソフトウェアの更新プログラムの一覧も含まれています。

Update 5.1 は、Update 5 を実行しているすべての StorSimple デバイスに適用できます。 5 より前のバージョンを使用している場合は、最初に Update 5 を適用し、その後 Update 5.1 を適用します。 Update 5.1 に関連付けられているデバイスのバージョンは、6.3.9600.17885 です。

StorSimple ソリューションの更新プログラムをデプロイする前に、リリース ノートに含まれる情報を確認してください。

> [!IMPORTANT]
>
> * Update 5.1 は必須の更新プログラムであり、デバイスの動作を保証するために、直ちにインストールする必要があります。 Update 5.0 が、サポートされる最小バージョンです。
> * Update 5.1 には、インストールに約 30 分かかるセキュリティ更新プログラムがあります。 詳しくは、[Update 5.1 の適用方法](storsimple-8000-install-update-51.md)に関するページをご覧ください。

## <a name="whats-new-in-update-51"></a>Update 5.1 の新機能

Update 5.1 では、主に次の機能強化とバグ修正が行われています。

* **TLS 1.2** - この StorSimple の更新プログラムでは、すべてのクライアントに TLS 1.2 が適用されます。 TLS 1.2 は、すべての StorSimple 8000 シリーズ デバイスの必須の更新プログラムです。

   次の警告が表示された場合は、続行する前にデバイスでソフトウェアを更新する必要があります。

   1 つ以上の StorSimple デバイスで、古いソフトウェア バージョンが実行されています。 TLS 1.2 の最新の利用可能な更新プログラムは、必須の更新プログラムであり、これらのデバイスに直ちにインストールする必要があります。 TLS 1.2 はすべての Azure portal 通信に使用され、この更新プログラムがないと、デバイスは StorSimple サービスと通信できません。

## <a name="known-issues-in-update-51-from-previous-releases"></a>以前のリリースから存在する Update 5.1 の既知の問題

Update 5.1 では、新しい問題は見つかっていません。 以前のリリースから Update 5.1 に持ち越された問題の一覧については、[Update 3 のリリース ノート](storsimple-update3-release-notes.md#known-issues-in-update-3)を参照してください。

## <a name="storsimple-cloud-appliance-updates-in-update-51"></a>Update 5.1 における StorSimple Cloud Appliance の更新プログラム

この更新プログラムはStorSimple Cloud Appliance (仮想デバイスとも呼ばれます) には適用できません。 Update 5.1 イメージを使用して、新しいクラウド アプライアンスを作成する必要があります。 StorSimple Cloud Appliance の作成方法については、[StorSimple Cloud Appliance のデプロイと管理](storsimple-8000-cloud-appliance-u2.md)に関する記事をご覧ください。

## <a name="next-step"></a>次のステップ

StorSimple デバイスに [Update 5.1 をインストールする](storsimple-8000-install-update-51.md)方法を確認します。
