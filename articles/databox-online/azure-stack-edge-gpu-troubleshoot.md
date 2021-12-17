---
title: 診断を実行し、Azure Stack Edge デバイスのトラブルシューティングのためのログを収集する | Microsoft Docs
description: 診断を実行し、ログを使用して Azure Stack Edge Pro GPU デバイスの問題のトラブルシューティングを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 10/18/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 4d66d325d3484f61ebee70d430e9a81b2da3a974
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130235413"
---
# <a name="run-diagnostics-collect-logs-to-troubleshoot-azure-stack-edge-device-issues"></a>診断を実行し、Azure Stack Edge デバイスの問題のトラブルシューティングを行うためのログを収集する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、診断を実行し、サポート パッケージを収集し、高度なセキュリティ ログを収集し、ログを確認して、Azure Stack Edge デバイスでのデバイスのアップロードと更新に関する問題のトラブルシューティングを行う方法について説明します。


## <a name="run-diagnostics"></a>診断の実行

デバイスのエラーの診断とトラブルシューティングを行うには、診断テストを実行します。 診断テストを実行するには、お客様のデバイスのローカル Web UI で、次の手順を実行します。

1. ローカル Web UI で、 **[トラブルシューティング]、[診断テスト]** の順に移動します。 実行したいテストを選択し、 **[テストの実行]** を選択します。 デバイスでテストを実行中であることが通知されます。 

    ![テストの選択 ](media/azure-stack-edge-gpu-troubleshoot/run-diag-1.png)

    Azure Stack Edge デバイスで実行される各診断テストについて説明した表を次に示します。

    | テスト名                        | 説明        |
    |----------------------------------|---------------------------------------------------------------------------------------------------------|
    | Azure portal の接続        | このテストでは、Azure Stack Edge デバイスから Azure portal への接続が検証されます。      |
    | Azure 整合性の正常性サービス | デバイスで実行される Azure Resource Manager、コンピューティング リソース プロバイダー、ネットワーク リソース プロバイダー、BLOB ストレージ サービスなどのいくつかのサービス。 これらのサービスが組み合されて、Azure 整合性スタックが提供されます。 正常性チェックにより、Azure 整合性サービスが稼働中であることが確認されます。 |
    | 証明書                     | このテストでは、有効期限と、デバイスと DNS ドメインの変更による証明書への影響が検証されます。 正常性チェックによって、すべての証明書がインポートされ、すべてのデバイス ノードに適用されていることが確認されました。                                                                                      |
    | Azure Edge コンピューティング ランタイム       | このテストでは、Azure Stack Edge の Kubernetes サービスが想定どおりに機能しているか検証されます。 これには、Kubernetes VM の正常性と、デバイスによってデプロイされた Kubernetes サービスの状態の確認が含まれます。  |
    | ディスク                            | このテストでは、すべてのデバイス ディスクが接続され、機能しているか検証されます。 これには、ディスクに適切なファームウェアがインストールされているかどうか、および BitLocker が正しく構成されているかどうかに関するチェックが含まれます。 |
    | 電源装置ユニット (PSU)                             | このテストでは、すべての電源が接続されて動作しているか検証されます。  |
    | ネットワーク インターフェイス               | このテストでは、すべてのネットワーク インターフェイスがデバイス上で接続されているか、およびそのシステムのネットワーク トポロジが想定どおりであるかが検証されます。    |
    | 中央処理装置 (CPU)                             | このテストでは、システム上の CPU が適切に構成されているか、およびそれらが稼働して動作しているかが検証されます。    |
    | コンピューティング アクセラレーション             | このテストでは、ハードウェアとソフトウェアの両方の観点から、コンピューティング アクセラレーションが想定どおりに機能しているか検証されます。 デバイス モデルによっては、コンピューティング アクセラレーションは、Graphical Processing Unit (GPU) または Vision Processing Unit (VPU)、あるいは Field Programmable Gate Array (FPGA) の場合があります。   |
    | ネットワーク設定                 | このテストでは、デバイスのネットワーク構成が検証されます。    |
    | インターネット接続            | このテストでは、デバイスのインターネット接続が検証されます。   |
    | システム ソフトウェア                  | このテストでは、システム ストレージとソフトウェア スタックが想定どおりに動作しているか検証されます。   |
    | 時間同期                        | このテストでは、デバイスの時刻設定が検証され、デバイス上に構成されているタイム サーバーが有効でありアクセス可能であることが確認されます。     |
    | ソフトウェア更新プログラムの準備状態        | このテストでは、構成されている更新サーバーが有効でアクセス可能であるか検証されます。   |
 
2. テストが完了すると、結果が表示されます。 

    ![テスト結果を表示する](media/azure-stack-edge-gpu-troubleshoot/run-diag-2.png)

    テストが失敗すると、推奨される操作の URL が表示されます。 URL を選択して、推奨される操作を表示します。
 
    ![失敗したテストの警告の確認](media/azure-stack-edge-j-series-troubleshoot/run-diag-3.png)


## <a name="collect-support-package"></a>サポート パッケージの収集

ログ パッケージは、Microsoft サポートがデバイスの問題のトラブルシューティングを行う際に役立つすべての関連ログで構成されます。 ログ パッケージは、ローカル Web UI を使用して生成することができます。

サポート パッケージを収集するには、次の手順を実行します。 

1. ローカル Web UI で、 **[トラブルシューティング]、[サポート]** の順に移動します。 **[サポートパッケージの作成]** を選択します。 システムでサポート パッケージの収集が開始されます。 パッケージの収集には数分かかる場合があります。

    ![[ユーザーの追加] の選択](media/azure-stack-edge-gpu-troubleshoot/collect-logs-1.png)
 
2. サポート パッケージが作成されたら、 **[サポート パッケージのダウンロード]** を選択します。 圧縮されたパッケージが、お客様が選択したパスにダウンロードされます。 パッケージを解凍して、システム ログ ファイルを表示できます。

    ![[ユーザーの追加] の選択 2](media/azure-stack-edge-gpu-troubleshoot/collect-logs-2.png)

## <a name="gather-advanced-security-logs"></a>高度なセキュリティ ログの収集

高度なセキュリティ ログには、Azure Stack Edge Pro デバイスへのソフトウェアまたはハードウェア侵入のログが記録されることがあります。

### <a name="software-intrusion-logs"></a>ソフトウェア侵入ログ

ソフトウェア侵入ログまたは既定のファイアウォール ログは、受信および送信トラフィックを対象に収集されます。 

- 工場でデバイスがイメージ化されると、既定のファイアウォール ログが有効になります。 これらのログは、ローカル UI またはデバイスの Windows PowerShell インターフェイスからサポート パッケージを作成すると、既定でサポート パッケージにバンドルされます。

- デバイスへのソフトウェア (ネットワーク) 侵入を確認するために、ファイアウォール ログのみがサポート パッケージに必要な場合は、サポート パッケージの作成時に `-Include FirewallLog` オプションを使用します。 

- 特定のインクルード オプションを指定しない場合、ファイアウォール ログは既定でサポートパッケージに含まれます。

- サポート パッケージで、ファイアウォール ログは `pfirewall.log` であり、ルート フォルダーにあります。 Azure Stack Edge Pro デバイスへのソフトウェア侵入ログの例を次に示します。 

    ```
    #Version: 1.5
    #Software: Microsoft Windows Firewall
    #Time Format: Local
    #Fields: date time action protocol src-ip dst-ip src-port dst-port size tcpflags tcpsyn tcpack tcpwin icmptype icmpcode info path
    
    2019-11-06 12:35:19 DROP UDP 5.5.3.197 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e88 ff02::fb 5353 5353 89 - - - - - - 
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9d87 ff02::fb 5353 5353 79 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.193 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe08:20d5 ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP 5.5.3.33 224.0.0.251 5353 5353 59 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8a ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    2019-11-06 12:35:19 DROP UDP fe80::3680:dff:fe01:9e8b ff02::fb 5353 5353 89 - - - - - - - RECEIVE
    ```

### <a name="hardware-intrusion-logs"></a>ハードウェア侵入ログ

デバイスへのハードウェア侵入を検出するために、現時点では、シャーシの開閉などのすべてのシャーシ イベントがログに記録されます。 

- デバイスからのシステム イベント ログは `racadm` コマンドレットを使用して読み取られます。 その後、これらのイベントをシャーシ関連のイベントに絞り込んだものが `HWIntrusion.txt` ファイルに保存されます。

- サポート パッケージでハードウェア侵入ログのみを取得するには、サポート パッケージの作成時に `-Include HWSelLog` オプションを使用します。 

- 特定のインクルード オプションを指定しない場合、ハードウェア侵入ログは既定でサポートパッケージに含まれます。

- サポート パッケージで、ハードウェア侵入ログは `HWIntrusion.txt` であり、ルート フォルダーにあります。 Azure Stack Edge Pro デバイスへのハードウェア侵入ログの例を次に示します。 

    ```
    09/04/2019 15:51:23 system Critical The chassis is open while the power is off.
    09/04/2019 15:51:30 system Ok The chassis is closed while the power is off.
    ```

## <a name="troubleshoot-device-upload-and-refresh-errors"></a>デバイスのアップロードと更新に関するエラーのトラブルシューティング

アップロード プロセス中および更新プロセス中に発生したすべてのエラーが、それぞれのエラー ファイルに含まれています。

1. エラー ファイルを表示するには、共有に移動し、内容を表示する共有を選択します。 


2. "_Microsoft Data Box Edge フォルダー_" を選択します。 このフォルダーには、次の 2 つのサブフォルダーがあります。

    - アップロードのエラー用のログ ファイルがある Upload フォルダー。
    - 更新中のエラー用の Refresh フォルダー。

    以下に更新用のログ ファイルのサンプルを示します。

    ```
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ``` 

3. このファイル内にエラー (サンプルの強調表示されている部分) がある場合は、エラー コードを書き留めておきます。この例では 16001 です。 以下のエラー リファレンスで、このエラー コードの説明を参照します。

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]


## <a name="next-steps"></a>次のステップ

- [デバイスのアクティブ化に関する問題をトラブルシューティングする](azure-stack-edge-gpu-troubleshoot-activation.md)。
- [Azure Resource Manager に関する問題のトラブルシューティング](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)。
- [Blob Storage に関する問題のトラブルシューティング](azure-stack-edge-gpu-troubleshoot-blob-storage.md)。
- [IoT Edge でのコンピューティングに関する問題のトラブルシューティング](azure-stack-edge-gpu-troubleshoot-iot-edge.md)。
