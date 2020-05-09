---
title: SAP HANA (L インスタンス) で Kdump を有効にするスクリプト | Microsoft Docs
description: SAP HANA (L インスタンス) HLI Type I、HLI Type II で Kdump を有効にするスクリプト
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16dc15b4369904643d0138a4b8e5b94c47868d31
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204780"
---
# <a name="enable-kdump-service"></a>Kdump サービスを有効にする

このドキュメントでは、Azure HANA L インスタンス (**Type I および Type II**) で Kdump サービスを有効にする方法の詳細について説明します

## <a name="supported-skus"></a>サポートされている SKU

|  HANA L インスタンスの種類   |  OS ベンダー   |  OS パッケージ バージョン   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |

## <a name="prerequisites"></a>前提条件

- Kdump サービスでは、ダンプの書き込みに `/var/crash` ディレクトリが使用されます。このディレクトリに対応するパーティションに、ダンプを格納できる十分なスペースがあることを確認してください。

## <a name="setup-details"></a>セットアップの詳細

- Kdump を有効にするスクリプトについては、[こちら](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh)を参照してください。

- 次のコマンドを使用して、HANA L インスタンス上でこのスクリプトを実行します

    > [!NOTE]
    > このコマンドを実行するには、sudo 特権が必要です。

    ```bash
    sudo bash enable-kdump.sh
    ```

- Kdump が正常に有効になったというコマンド出力の場合は、システムを再起動して変更を適用してください。Kdump が有効になります。 システムを再起動して変更を適用します。

- 特定の操作の実行に失敗したというコマンド出力の場合は、Kdump サービスは有効になっていません。 「[サポートの問題](#support-issue)」セクションを参照してください。

## <a name="test-kdump"></a>Kdump をテストする

> [!NOTE]
>  以下の操作によって、カーネルのクラッシュとシステムの再起動がトリガーされます。

- カーネルのクラッシュをトリガーする

    ```bash
    echo 1 > /proc/sys/kernel/sysrq
    echo c > /proc/sysrq-trigger
    ```

- システムが正常に再起動したら、`/var/crash` ディレクトリでカーネルのクラッシュ ログを確認します。

- `/var/crash` に現在の日付のディレクトリがある場合、Kdump は正常に有効になっています。

## <a name="support-issue"></a>サポートの問題

スクリプトがエラーで失敗した場合、または Kdump が有効になっていない場合は、Microsoft サポート チームにサービス要求を提出し、次の詳細を伝えます。

* HLI サブスクリプション ID

* サーバー名

* OS ベンダー

* OS バージョン

* カーネル バージョン
