---
title: SAP HANA (Large Instances) で kdump を有効にするスクリプト | Microsoft Docs
description: Azure HANA Large Instances Type I と Type II で kdump サービスを有効にする方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 272ac309629c62ee9fc7d12236aac4b2c3106b8a
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540926"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure Large Instances の kdump

この記事では、Azure HANA Large Instances (HLI) **Type I と Type II** で kdump サービスを有効にする方法について説明します。

明確な原因がないシステム クラッシュのトラブルシューティングを行うためには、kdump を構成して有効にすることが必要です。 時々、ハードウェアやインフラストラクチャの問題ではシステム クラッシュの説明がつけられないことがあります。 そのようなケースでは、オペレーティング システムやアプリケーションが問題の原因である場合があります。 kdump を使用すると、SUSE ではシステム クラッシュの理由を判断できます。

## <a name="supported-skus"></a>サポートされている SKU

|  HANA L インスタンスの種類   |  OS ベンダー   |  OS パッケージ バージョン   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>前提条件

- kdump サービスでは、`/var/crash` ディレクトリを使用してダンプが書き込まれます。 このディレクトリに対応するパーティションに、ダンプを格納できる十分なスペースがあることを確認してください。

## <a name="setup-details"></a>セットアップの詳細

- kdump を有効にするスクリプトは、[GitHub の Azure sap-hana-tools](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh) にあります。

> [!NOTE]
> このスクリプトは、ラボの設定に基づいて作成されています。 さらにチューニングを行う場合は、OS ベンダーに問い合わせる必要があります。
> ダンプを保存する新規と既存のサーバーに対して、別々の論理ユニット番号 (LUN) がプロビジョニングされます。 LUN からのファイル システムの構成は、スクリプトによって行われます。
> Microsoft はダンプを分析する責任を負いません。 OS ベンダーでチケットを開いて、分析してもらう必要があります。

- HANA Large Instance でこのスクリプトを実行するには、次のコマンドを使用します。

    > [!NOTE]
    > このコマンドを実行するには、sudo 特権が必要です。

    ```bash
    sudo bash enable-kdump.sh
    ```

- コマンドの出力に、kdump が正常に有効化されたと表示されたら、システムを再起動して変更を適用します。

- コマンドの出力に、操作が失敗したと表示された場合、kdump サービスは有効になっていません。 「[サポートに関する問題](#support-issues)」セクションを参照してください。

## <a name="test-kdump"></a>kdump をテストする

> [!NOTE]
>  以下の操作によって、カーネルのクラッシュとシステムの再起動がトリガーされます。

- カーネルのクラッシュをトリガーする

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- システムが正常に再起動したら、`/var/crash` ディレクトリでカーネルのクラッシュ ログを確認します。

- `/var/crash` に本日の日付のディレクトリがあれば、kdump は正常に有効化されています。

## <a name="support-issues"></a>サポートに関する問題

スクリプトがエラーで失敗した場合、または kdump が有効になっていない場合は、Microsoft サポート チームにサービス要求を提出してください。 次の詳細を記載してください。

* HLI サブスクリプション ID

* サーバー名

* OS ベンダー

* OS バージョン

* カーネル バージョン

詳細については、[kdump の構成](https://www.suse.com/support/kb/doc/?id=3374462)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

HANA Large Instances でのオペレーティング システムのアップグレードについて説明します。

> [!div class="nextstepaction"]
> [オペレーティング システムのアップグレード](os-upgrade-hana-large-instance.md)
