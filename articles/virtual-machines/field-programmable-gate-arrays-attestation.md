---
title: Azure FPGFA 構成証明サービス
description: NP シリーズ VM の構成証明サービス。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 563155bb6559f8443f1453a65fa0b1574af106f7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555917"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>Azure NP シリーズ VM の FPGA 構成証明 (プレビュー)

FPGA 構成証明サービスは、Xilinx ツールセットによって生成されるデザイン チェックポイント ファイル (“netlist” と呼ばれます) に対して一連の検証を実行し、NP シリーズ VM の Xilinx U250 FPGA カードに読み込むことができる検証済みのイメージ (“ビットストリーム” と呼ばれます) を含むファイルを生成します。  

## <a name="prerequisites"></a>前提条件  

Azure サブスクリプションと Azure Storage アカウントが必要になります。 サブスクリプションで Azure にアクセスできるようになり、ストレージ アカウントは構成証明サービスの netlist と出力ファイルを保持するために使用されます。  

構成証明要求を送信するための PowerShell および Bash スクリプトが用意されています。   スクリプトには Azure CLI を使用します。これは、Windows および Linux で実行できます。 PowerShell は Windows、Linux、および macOS 上で実行できます。  

Azure CLI ダウンロード (必須):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Windows、Linux、macOS 用 PowerShell のダウンロード (PowerShell スクリプトのみ):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

構成証明サービスに送信するには、テナントとサブスクリプション ID が承認されている必要があります。 アクセスを要求するには、 https://aka.ms/AzureFPGAAttestationPreview にアクセスしてください。 

## <a name="building-your-design-for-attestation"></a>構成証明の設計を構築する  

設計を構築するための推奨される Xilinx ツールセットは、Vitis 2020.2 です。 netlist ファイルは、以前のバージョンのツールセットで作成され、引き続き 2020.2 と互換性がある場合、使用できます。 構築するのに適切なシェルが読み込まれていることを確認してください。 現在サポートされているバージョンは xilinx_u250_gen3x16_xdma_2_1_202010_1 です。 サポート ファイルは Xilinx Alveo ラウンジからダウンロードできます。 

ビットストリームの代わりに netlist を含む xclbin ファイルを構築するには、Vitis (v++ コマンド ライン) に次の引数を含める必要があります。   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Azure へのログイン  

Azure で何らかの操作を実行する前に、Azure にログインし、サービスを呼び出すことが許可されているサブスクリプションを設定する必要があります。 その場合は ```az login``` と ```az account set –s <Sub ID or Name>``` コマンドを使用します。 このプロセスの詳細については、次のドキュメントを参照してください:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. コマンドラインで、’対話操作でサインインする’ か、‘資格情報を使用してサインインする‘ オプションのいずれかを使用します。  

## <a name="creating-a-storage-account-and-blob-container"></a>ストレージ アカウントと BLOB コンテナーの作成  

構成証明サービスからアクセスするには、netlist ファイルを Azure ストレージ BLOB コンテナーにアップロードする必要があります。  

アカウントとコンテナーを作成する方法、および netlist を BLOB としてそのコンテナーにアップロードする方法の詳細については、次のページを参照してください: https://docs.microsoft.com/azure/storage/blobs/storage-quickstartblobs-cli 。  

または、Azure portal を使用することもできます。  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>netlist ファイルを Azure BLOB ストレージにアップロードする  

ファイルをコピーするには、いくつかの方法があります。az ストレージ アップロード コマンドレットの使用例を次に示します。 az コマンドは、Linux と Windows の両方で実行できます。 "BLOB" 名には任意の名前を選択できますが、xclbin 拡張子は変更しないようにしてください。 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>構成証明スクリプトのダウンロード  

検証スクリプトは、次の Azure ストレージ BLOB コンテナーからダウンロードできます。  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

zip ファイルには、送信用と監視用の 2 つの PowerShell スクリプトが含まれています。3 番目のファイルは、両方の機能を実行するバッシュ スクリプトです。  

## <a name="running-the-attestation-scripts"></a>構成証明スクリプトの実行  

スクリプトを実行するには、ストレージ アカウント名、netlist ファイルが格納されている BLOB コンテナー名、および netlist ファイル名を指定する必要があります。 また、netlist ではなく、コンテナーへの読み取り/書き込みアクセスを許可する サービス Shared Access Signature (SAS) を作成する必要があります。 この SAS は、構成証明サービスで netlist ファイルのローカル コピーを作成し、結果として得られた検証プロセスの出力ファイルをコンテナーに書き戻すために使用されます。  

Shared Access Signature の概要については、こちらを参照してください。Service SAS についての詳細も確認することができます。 Service SAS のページには、生成された SAS の保護に関する重要な注意事項が含まれています。  なぜ SAS を悪意のある、または意図しない使用から保護する必要があるのかをご理解いただくために、注意事項をお読みください。  

コンテナーの SAS は、az ストレージ コンテナー で生成された SAS コマンドレットを使用して生成できます。 送信時から少なくとも数時間経過した有効期限を UTC 形式で指定してください。送信してから 6 時間後に設定すれば十分です。  

仮想ディレクトリを使用する場合は、コンテナー引数の一部としてディレクトリ階層を含める必要があります。 たとえば、"netlists" という名前のコンテナーがあり、netlists BLOB を含む "image1" という名前の仮想ディレクトリがある場合、コンテナー名として "netlists/image1" を指定します。 さらにディレクトリ名を追加して、より深い階層を指定します。 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>送信状態の確認  

構成証明サービスから、送信のオーケストレーション ID が返されます。 送信スクリプトによって完了状態がポーリングされ、その後送信のモニタリングが自動的に開始されます。 オーケストレーション ID は、送信に何が起こったかを確認するための主な方法であるため、問題が発生した場合に備えて保存しておいてください。 参考までに、構成証明は小さい netlist ファイル (300MB) の場合は完了までに約 30 分かかり、1.6 GB のファイルには 1 時間かかります。 

Monitor-Validation.ps1 スクリプトを呼び出せば、いつでも構成証明の状態と結果を取得できます。ここでは、オーケストレーション ID を引数として指定します。  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

または、HTTP 投稿要求を構成証明サービス エンドポイントに送信することもできます。  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

要求本文には、構成証明要求のサブスクリプション ID、テナント ID、およびオーケストレーション ID を含めてください。  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>検証後のステップ

出力がサービスからコンテナーに書き込まれます。 検証パスが成功した場合、コンテナーには元の netlist ファイル (abc.xclbin)、ビットストリーム (abc.bit.xclbin) を含むファイル、格納されているビットストリームの非公開の保存先を特定するファイル (abc. azure.xclbin) と 4 つのログ ファイルが格納されます。4 つのログ ファイルのうち 1 つはスタートアップ プロセス用 (abc-log.txt) で、残りは検証を実行する 3 つの並列フェーズ用です。 これらの名前は *logPhaseX.txt となり、X はフェーズの番号です。 U250 への検証済みイメージのアップロードが通知するようにするために、VM では azure.xclbin が使用されます。 

検証が失敗した場合は、手順が失敗したことを示す error-*.txt ファイルが書き込まれます。 また、エラー ログに構成証明に失敗したことが示されている場合は、ログ ファイルを確認してください。 サポートが必要な場合は、サポート要求にオーケストレーション ID と共に、これらすべてのファイルを含めるようにしてください。  

Azure portal を使用して、コンテナーを作成するだけでなく、netlist をアップロードし、ビットストリームおよびログ ファイルをダウンロードすることもできます。 現時点では、構成証明要求を送信し、その進行状況を監視することはサポートされていませんので、前述のように、スクリプトを使用して実行してださい。 

