---
title: 使用状況データを Azure にアップロードする
description: Azure Arc 対応データ サービスのデータを Azure にアップロードする
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 74df592db61e4c9c50f9b199d7803fb8e1481878
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732158"
---
# <a name="upload-usage-data-to-azure"></a>使用状況データを Azure にアップロードする

定期的に、使用方法に関する情報をエクスポートできます。 この情報のエクスポートとアップロードでは、データ コントローラー、SQL マネージド インスタンス、および Azure の PostgreSQL Hyperscale サーバー グループ リソースが作成および更新されます。

> [!NOTE] 
> プレビュー期間中は、Azure Arc 対応データ サービス利用のコストは発生しません。



> [!NOTE]
> 使用状況データをアップロードする前に、Azure Arc データ コントローラーを作成してから少なくとも 24 時間お待ちください。

## <a name="create-service-principal-and-assign-roles"></a>サービス プリンシパルを作成してロールを割り当てる

続行する前に、必要なサービス プリンシパルを作成して、適切なロールに割り当てていることを確認してください。 詳細については、次の情報を参照してください。
* [サービス プリンシパルを作成する](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)。
* [サービス プリンシパルにロールを割り当てる](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>使用状況データをアップロードする

インベントリやリソースの使用状況などの使用状況情報は、次の 2 段階の方法で Azure にアップロードできます。

1. 次のように、`az arcdata dc export` コマンドを使用して使用状況データをエクスポートします。

> [!NOTE]
> コマンド `az arcdata dc export` を使用して使用状況および課金情報、メトリック、ログをエクスポートするには、今のところ SSL 検証をバイパスする必要があります。  SSL 検証をバイパスするように求めるメッセージが表示されます。または、プロンプトを表示しないように環境変数 `AZDATA_VERIFY_SSL=no` を設定できます。  現在、データ コントローラー エクスポート API に対して SSL 証明書を構成する方法はありません。

   ```azurecli
   az arcdata dc export --type usage --path usage.json --k8s-namespace <namespace> --use-k8s
   ```
 
   このコマンドにより、データ コントローラー上に作成されたすべての Azure Arc 対応データ リソース (SQL マネージド インスタンスや PostgreSQL Hyperscale インスタンスなど) を含む `usage.json` ファイルが作成されます。

2. `upload` コマンドを使用して使用状況データをアップロードします。

   ```azurecli
   az arcdata dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>アップロードの自動化 (省略可能)

メトリックとログをスケジュールに基づいてアップロードする場合は、スクリプトを作成して、タイマーで数分ごとに実行できます。 次に、Linux シェル スクリプトを使用してアップロードを自動化する例を示します。

お好きなテキスト エディターまたはコード エディターで、ファイルに次のスクリプトを追加し、`.sh` (Linux/Mac)、`.cmd`、`.bat`、`.ps1` などのスクリプト実行可能ファイルとして保存します。

```azurecli
az arcdata dc export --type usage --path usage.json --force --k8s-namespace <namespace> --use-k8s
az arcdata dc upload --path usage.json
```

スクリプト ファイルを実行可能にする

```console
chmod +x myuploadscript.sh
```

使用状況について毎日次のスクリプトを実行します。

```console
watch -n 1200 ./myuploadscript.sh
```

Cron や Windows タスク スケジューラのようなジョブ スケジューラを使用することも、Ansible、Puppet、Chef のようなオーケストレーターを使用することもできます。

## <a name="next-steps"></a>次のステップ

[メトリックとログを Azure Monitor にアップロードする](upload-metrics.md)

[ログを Azure Monitor にアップロードする](upload-logs.md)

[課金データを Azure にアップロードして Azure portal に表示する](view-billing-data-in-azure.md)

[Azure portal で Azure Arc データ コントローラー リソースを表示する](view-data-controller-in-azure-portal.md)
