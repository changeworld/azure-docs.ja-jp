---
title: Data Science Virtual Machine の新機能
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine のリリース ノート
author: michalmar
ms.service: data-science-vm
ms.author: mimarusa
ms.date: 07/16/2021
ms.topic: reference
ms.openlocfilehash: e256382b4d1e397be6d11a3660a4c4fe73eafb23
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778325"
---
# <a name="azure-data-science-virtual-machine-release-notes"></a>Azure Data Science Virtual Machine リリース ノート

この記事では、Azure Data Science Virtual Machine リリースについて説明します。 含まれるツールの完全一覧とバージョン番号については、[こちらのページ](./tools-included.md)をご覧ください。

バグおよび対処法については、[既知の問題のリスト](reference-known-issues.md)を参照してください。


## <a name="2021-08-11"></a>2021-08-11

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) の新しいイメージ。

バージョン: 21.08.11

主な変更点:

- Windows セキュリティに関する更新
- Nvidia CuDNN の 8.1.0 への更新
- Jupyter Lab の 3.0.16 への更新
- 実験追跡用の MLFLow の追加
- 安定性の向上と軽微なバグ修正 



## <a name="2021-07-12"></a>2021-07-12

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) の新しいイメージ。

主な変更点:

- PyTorch 1.9.0 に更新されました
- Azure CLI が 2.26.1 に更新されました
- Azure CLI Azure Machine Learning 拡張機能が 1.29.0 に更新されました
- VS Code バージョン 1.58.1 の更新
- 安定性の向上と軽微なバグ修正 


## <a name="2021-06-22"></a>2021-06-22

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) の新しいイメージ。

バージョン: 21.06.22

主な変更点:

- PyTorch 1.9.0 に更新されました
- git が利用できないバグを修正しました


## <a name="2021-06-01"></a>2021-06-01

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) の新しいイメージ。

バージョン: 21.06.01

主な変更点は次のとおりです。

- Docker は、既定で有効になっています
- JupyterHub は、既定で JupyterLab を使用しています
- [CVE-2020-15523](https://nvd.nist.gov/vuln/detail/CVE-2020-15523) を修正するために Python のバージョンが更新されました
- [CVE-2021-25758](https://nvd.nist.gov/vuln/detail/CVE-2021-25758) を修正するために IntelliJ IDEA がバージョン 2021.1 に更新されました
- PyCharm Community が 2021.1 に更新されました
- TensorFlow がバージョン 2.5.0 に更新されました

<br/>
デスクトップからいくつかのアイコンが削除されました。

## <a name="2021-05-22"></a>2021-05-22

[Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) の新しいイメージ。

バージョン: 21.05.22

選択されたバージョンの更新は次のとおりです。
- CUDA 11.1
- Python 3.8
- PyTorch 1.8.1
- TensorFlow 2.5.0
- Spark 3.1.1
- Java 11
- R 4.1.0
- Julia 1.0.5
- NodeJS 16.2.0
- Azure ML 拡張機能を含む Visual Studio Code 1.56.2
- PyCharm Community Edition 2021.1.1
- Jupyter Lab 2.2.6
- RStudio 1.4.1106
- Visual Studio Community Edition 2019 (バージョン 16.9.6)
- Azure CLI 2.23.0
- Storage Explorer 1.19.1
- AzCopy 10.10.0
- Power BI Desktop 2.93.641.0 64 ビット (2021 年 5 月)
- Azure Data Studio 1.28.0
- Microsoft Edge ブラウザー

<br/>
Firefox、Apache Drill、Microsoft Integration Runtime が削除されました。

<br/>
ダーク モード、デスクトップ上のアイコンの変更、壁紙の背景の変更。

## <a name="2021-05-12"></a>2021-05-12

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) の新しいイメージ。

選択されたバージョンの更新は次のとおりです。
- CUDA 11.3、cuDNN 8、NCCL2
- Python 3.8
- R 4.0.5
- mmlspark、Blob Storage へのコネクター、Data Lake、CosmosDB を含む Spark 3.1
- Java 11 (OpenJDK)
- Jupyter Lab 3.0.14
- torchaudio torchtext torchvision、torch-tb-profiler を含む PyTorch 1.8.1
- TensorBoard を含む TensorFlow 2.4.1
- dask 2021.01.0
- VS.Code 1.56
- Azure Data Studio 1.22.1
- Azure CLI 2.23.0
- Azure Storage Explorer 1.19.1
- azcopy 10.10
- Microsoft Edge ブラウザー (ベータ)

<br/>
Docker が追加されました。 リソースを節約するために、Docker サービスは既定では開始されません。 Docker サービスを開始するには、次のコマンド ライン コマンドを実行します。

```
sudo systemctl start docker
```

> [!NOTE]
> マシンに GPU が搭載されている場合、Docker コマンドに `--gpus` パラメーターを追加することで、コンテナー内で GPU を使用できます。
>
> たとえば、 を実行すると、
>
> `sudo docker run --gpus all -it --rm -v local_dir:container_dir nvcr.io/nvidia/pytorch:18.04-py3`
>
> PyTorch がプレインストールされ、すべての GPU が有効になっている Ubuntu 18.04 コンテナーが実行されます。 また、*container_dir* の下のコンテナーでローカル フォルダー *local_dir* を使用できるようになります。
>


## <a name="2020-02-24"></a>2020-02-24

[Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview) および [Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview) イメージの Data Science Virtual Machine イメージが使用できるようになりました。

## <a name="2019-10-08"></a>2019-10-08

### <a name="updates-to-software-on-the-windows-dsvm"></a>Windows DSVM のソフトウェアの更新プログラム

- Azure Storage Explorer 1.10.1
- Power BI Desktop 2.73.55xx
- Firefox 69.0.2
- PyCharm 19.2.3
- RStudio 1.2.50xx

### <a name="default-browser-for-windows-updated"></a>Windows の既定のブラウザーの更新

以前は、既定のブラウザーは Internet Explorer に設定されていました。 今後、ユーザーは初めてログインするとき、既定のブラウザーを選択するように求められます。
