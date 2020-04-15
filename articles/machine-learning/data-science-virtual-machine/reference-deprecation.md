---
title: 'リファレンス: Data Science Virtual Machine イメージの非推奨'
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine に影響する非推奨の詳細
author: lobrien
ms.service: machine-learning
ms.subservice: data-science-vm
ms.author: laobri
ms.date: 04/03/2020
ms.topic: reference
ms.openlocfilehash: 6004ae0c1fd2fa25a07ab84776ab74789f6e7da9
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754774"
---
# <a name="reference-deprecation-of-dsvm-images"></a>リファレンス: DSVM イメージの非推奨

以下では、Azure Data Science Virtual Machine の今後の非推奨化に対処するための推奨事項について説明します。

## <a name="windows-2012-migrating-data-disks"></a>Windows 2012: データ ディスクの移行

Windows 2012 DSVM イメージのサポートは、2019 年 12 月 31 日に終了します。 既存の Windows 2012 DSVM から Windows 2016 DSVM にデータ ディスクを移行するには、次の手順を行います。

1. [こちら](./provision-vm.md#create-your-dsvm)に示されている手順に従って、新しい Windows 2016 DSVM を作成します。
1. [こちらの手順](../../virtual-machines/windows/detach-disk.md)を使用して、Windows 2012 イメージから既存のデータ ディスクをデタッチします。
1. [こちらの手順](../../virtual-machines/windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm)を使用して、前のステップのディスクを Windows 2016 イメージにアタッチします。

## <a name="centos"></a>CentOS

新しいユーザーは、最新の UbuntuまたはWindowsイメージを使用する必要があります。 CentOSは、既存のソリューションテンプレートで引き続き使用できます。