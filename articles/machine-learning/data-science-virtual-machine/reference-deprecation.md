---
title: 'リファレンス: Data Science Virtual Machine イメージの提供終了'
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine に影響する提供終了の詳細
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 07/17/2020
ms.topic: reference
ms.openlocfilehash: ac56e2e69cf62c8afae73e0a7616e7e7f80c0493
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816322"
---
# <a name="reference-retirements-of-dsvm-images"></a>リファレンス: DSVM イメージの提供終了

以下では、イメージの提供終了 (非推奨化) と、Azure Data Science Virtual Machine 上で予定されている提供終了に対処するための推奨事項について説明します。

## <a name="why-we-retire-dsvm-images"></a>DSVM イメージを提供終了する理由

現在 Data Science Virtual Machine には、次の 2 つのエディションがあります。

* Ubuntu
* Windows Server

これらのエディションの DSVM イメージは、Ubuntu 18.04 LTS など、特定のオペレーティング システムのバージョンに基づいて構築されています。 時間が経過すると、オペレーティング システムの "_バージョン_" のメンテナンス期間は終了することになり、また、データ サイエンス ツールによって古いバージョンのオペレーティング システムはサポートされなくなります。 最新のオペレーティング システムとデータ サイエンス ツールで DSVM イメージを最新の状態に保つために、Microsoft はより新しいバージョンのオペレーティング システムに基づいて新しい DSVM イメージをリリースします。

## <a name="how-we-retire-dsvm-images"></a>DSVM イメージを提供終了する方法

Microsoft は_提供終了に関するお知らせ_を発行し、予定されている DSVM イメージの提供終了について既存の DSVM ユーザーに (電子メールで) お知らせします。 提供終了に関するお知らせには、"_提供終了日_" (この日付を過ぎるとイメージが利用できなくなります) と、軽減策の推奨事項 (より新しい DSVM イメージへのアップグレード方法など) が詳述されます。

"_お知らせ_" の日に、マーケットプレース内のイメージを非表示にします。これにより、次のようになります。

1. 提供終了となる DSVM イメージを新しいユーザーが誤ってプロビジョニングすることが防止されます。
2. 既存のユーザーは提供終了日まで ARM デプロイを使用できます。

非表示にされたイメージには、_提供終了日_までオペレーティング システムの修正プログラムが届けられますが、データ サイエンス ツールおよびフレームワークに対する更新プログラムは届け__られなくなります__。 "_提供終了日_" には、イメージは ARM デプロイで利用できなくなります。

ご利用のサブスクリプションにプロビジョニングされている DSVM イメージは、提供終了日以降も引き続き動作します。 ただし、最新のオペレーティング システムとデータ サイエンス ツールを使用できるように、最新の DSVM イメージにアップグレードすることをお勧めします。

## <a name="impact"></a>影響

ご利用のサブスクリプション内の既存のプロビジョニングされた DSVM イメージは、提供終了日以降も引き続き動作します。 ただし、Azure portal または ARM テンプレートを使用してユーザーが自分の DSVM イメージをより新しいバージョンにアップグレードすることをお勧めします。

> [!WARNING]
> Virtual Machine Scale Sets を使用してプロビジョニングされているインベントリから削除された DSVM イメージは、提供終了日が過ぎると、スケールアップできなくなります。
>
> 新しい DSVM イメージの詳細で更新されていない ARM テンプレートは、提供終了日が過ぎると、デプロイできなくなります。

## <a name="mitigating-upcoming-retirements"></a>予定されている提供終了に対する軽減策

このセクションでは、予定されている提供終了に対する軽減策について説明します。

### <a name="upgrade-windows-2016-dsvm"></a>Windows 2016 DSVM のアップグレード

既存の Windows 2016 DSVM から Windows 2019 DSVM にデータ ディスクを移行するには、次の手順を行います。

1. [こちら](./provision-vm.md#create-your-dsvm)に示されている手順に従って、新しい Windows 2019 DSVM を作成します。
1. [こちらの手順](../../virtual-machines/windows/detach-disk.md)を使用して、Windows 2016 イメージから既存のデータ ディスクをデタッチします。
1. [こちらの手順](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)を使用して、前のステップのディスクを Windows 2019 イメージにアタッチします。

### <a name="upgrade-ubuntu-1604-dsvm"></a>Ubuntu 16.04 DSVM のアップグレード

既存の Ubuntu 16.04 DSVM を [Ubuntu 18.04 DSVM エディション](./dsvm-ubuntu-intro.md)にアップグレードすることをお勧めします。
