---
title: インクルード ファイル
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92283384"
---
オペレーティング システム (および必要に応じてその他のサービス) が事前に構成されているいずれかの VM を選択した場合、標準の Azure VM サイズが既に選択されています。 事前構成された OS でソリューションを始めるのが、推奨されるアプローチです。 ただし、OS を手動でインストールする場合は、VM イメージ内のプライマリ VHD のサイズを変更する必要があります。 OS ディスクのサイズが Linux または Windows の制限内であることを確認します。

| OS | VHD サイズ |
| --- | --- |
| Linux | 30 ～ 1023 GB |
| Windows | 30 ～ 250 GB |
|

承認済みのベースとして提供されている Windows または SQL Server のベース イメージはこれらの要件を既に満たしているため、VHD の形式やサイズは変更しないでください。

データ ディスクは 1 TB にすることができます。 サイズを決めるときに、お客様はデプロイ時にイメージ内の VHD のサイズを変更できないことに注意してください。 データ ディスク VHD は固定形式の VHD として作成してください。 また、拡張可能 (スパース/容量可変) にする必要もあります。 データ ディスクは最初に空にすることも、データを含むこともできます。