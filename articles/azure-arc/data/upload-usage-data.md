---
title: 使用状況データを Azure Monitor にアップロードする
description: Azure Arc 対応データ サービスのデータを Azure Monitor にアップロードする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 0c72eda59f375c70274b17796ca53614ef95505b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669510"
---
# <a name="upload-usage-data-to-azure-monitor"></a>使用状況データを Azure Monitor にアップロードする

定期的に、使用方法に関する情報をエクスポートできます。 この情報のエクスポートとアップロードでは、データ コントローラー、SQL マネージド インスタンス、および Azure の PostgreSQL Hyperscale サーバー グループ リソースが作成および更新されます。

> [!NOTE] 
> プレビュー期間中は、Azure Arc 対応データ サービス利用のコストは発生しません。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


> [!NOTE]
> 使用状況データをアップロードする前に、Azure Arc データ コントローラーを作成してから少なくとも 24 時間お待ちください。

## <a name="create-service-principal-and-assign-roles"></a>サービス プリンシパルを作成してロールを割り当てる

続行する前に、必要なサービス プリンシパルを作成して、適切なロールに割り当てていることを確認してください。 詳細については、次の情報を参照してください。
* [サービス プリンシパルを作成する](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)。
* [サービス プリンシパルにロールを割り当てる](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>使用状況データをアップロードする

インベントリやリソースの使用状況などの使用状況情報は、次の 2 段階の方法で Azure にアップロードできます。

1. データ コントローラーにログインします。 プロンプトで次の値を入力します。 

   ```console
   azdata login
   ```

1. 次のように、`azdata arc dc export` コマンドを使用して使用状況データをエクスポートします。

   ```console
   azdata arc dc export --type usage --path usage.json
   ```
 
   このコマンドにより、データ コントローラー上に作成されたすべての Azure Arc 対応データ リソース (SQL マネージド インスタンスや PostgreSQL ハイパースケール インスタンスなど) を含む `usage.json` ファイルが作成されます。

2. ```azdata upload``` コマンドを使用して使用状況データをアップロードする

   ```console
   azdata arc dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>アップロードの自動化 (省略可能)

メトリックとログをスケジュールに基づいてアップロードする場合は、スクリプトを作成して、タイマーで数分ごとに実行できます。 次に、Linux シェル スクリプトを使用してアップロードを自動化する例を示します。

お好きなテキスト エディターまたはコード エディターで、ファイルに次のスクリプトを追加し、`.sh` (Linux/Mac)、`.cmd`、`.bat`、`.ps1` などのスクリプト実行可能ファイルとして保存します。

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

スクリプト ファイルを実行可能にする

```console
chmod +x myuploadscript.sh
```

20 分ごとにスクリプトを実行します。

```console
watch -n 1200 ./myuploadscript.sh
```

Cron や Windows タスク スケジューラのようなジョブ スケジューラを使用することも、Ansible、Puppet、Chef のようなオーケストレーターを使用することもできます。

## <a name="next-steps"></a>次のステップ

[メトリックとログを Azure Monitor にアップロードする](upload-metrics.md)

[ログを Azure Monitor にアップロードする](upload-logs.md)

[課金データを Azure にアップロードして Azure portal に表示する](view-billing-data-in-azure.md)

[Azure portal で Azure Arc データ コントローラー リソースを表示する](view-data-controller-in-azure-portal.md)
