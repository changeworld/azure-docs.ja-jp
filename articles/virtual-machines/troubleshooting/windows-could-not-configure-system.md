---
title: Windows がシステムの構成を完了できなかったことをトラブルシューティングする
titlesuffix: Azure Virtual Machines
description: この記事では、Sysprep プロセスが原因で Azure VM を起動できない問題を解決するための手順を示します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 9c170607d6300c4921285e85ac78db5a8a18ad9b
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078789"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Windows がシステムの構成を完了できなかったことをトラブルシューティングする

この記事では、Sysprep プロセスが原因で Azure 仮想マシン (VM) を起動できない問題を解決するための手順を示します。

## <a name="symptom"></a>症状

[ブート診断](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)を使用して VM のスクリーンショットを表示すると、Windows インストールでエラーが出たこと、Windows セットアップで各種サービスが開始されていることがスクリーンショットに表示されます。 このエラーにより、次のメッセージが表示されます。

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![図 1 からは Windows インストール エラーと次のようなメッセージを確認できます。"Windows could not finish configuring the system (Windows のシステムの構成を完了できませんでした). To attempt to resume configuration, restart the computer (構成を再開するには、コンピューターを再起動します). Setup is starting services (セットアップにより各種サービスが開始されています)"](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>原因

このエラーは、オペレーティング システム (OS) が [Sysprep プロセス](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-process-overview)を完了できない場合に発生します。 このエラーは、一般化された VM を最初に起動しようとしたときに発生します。 この問題が発生した場合、イメージが展開不可能な状態にあり、回復できないため、一般化されたイメージを再作成します。

## <a name="solution"></a>解決策

この問題を解決するには、[イメージの準備やキャプチャに関する Azure のガイダンス](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)の記事に従い、一般化された新しいイメージを準備します。
