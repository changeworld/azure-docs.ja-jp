---
title: Azure 上の PyTorch Enterprise のサポート範囲
description: この記事では、PyTorch Enterprise のサポート範囲を定義します。
author: alonbochman
ms.author: alonbochman
ms.service: pytorch-enterprise
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: ea2f70af6611f6bed5a36f72324874e0461182f3
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598703"
---
# <a name="support-boundaries-for-pytorch-enterprise"></a>PyTorch Enterprise のサポート範囲

この簡潔なドキュメントで、Pytorch Enterprise でサポートされるモジュールとコンポーネントについて説明します。


|領域|サポートされているバージョン|メモ|
|----|----|----|
|OS|Windows 10、Debian 9、Debian 10、Ubuntu 16.04.7 LTS、Ubuntu 18.04.5 LTS|Debian および Ubuntu ディストリビューションの LTS バージョン (x86_64 アーキテクチャのみ) をサポートしています。|
|Python|3.6 以降||
|PyTorch|1.8.1 以降||
|CUDA Toolkit|10.2、11.1||
|ONNX Runtime|1.7 以降||
|torchtext、torchvision、torch-tb-profiler、torchaudio| - |1\.0 リリースがないライブラリの場合、対応する PyTorch のサポート対象バージョンと互換性のある特定のバージョンをサポートします。 たとえば、[TorchVision](https://github.com/pytorch/vision#installation)、[TorchText](https://github.com/pytorch/text#installation)、[TorchAudio](https://github.com/pytorch/audio/#dependencies) の一覧を参照してください。|
|torchserve|0.4.0 以降||
