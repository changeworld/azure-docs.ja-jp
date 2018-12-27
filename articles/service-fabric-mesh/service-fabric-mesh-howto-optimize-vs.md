---
title: Visual Studio のパフォーマンスを Azure Service Fabric Mesh プロジェクト用に最適化する | Microsoft Docs
description: Visual Studio のパフォーマンスを Azure Service Fabric Mesh アプリ用に最適化する
services: service-fabric-mesh
keywords: デバッグのパフォーマンスを最適化
author: tylermsft
ms.author: twhitney
ms.date: 11/29/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: 72e900e6e48d18a721be7d2991428f81a81d1303
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891936"
---
# <a name="optimize-visual-studio-performance-for-service-fabric-mesh-projects"></a>Visual Studio のパフォーマンスを Service Fabric Mesh プロジェクト用に最適化する

この記事では、Visual Studio のパフォーマンスを Service Fabric Mesh 用に最適化して、初回のデバッグ実行 (F5) を大幅に高速化する方法について説明します。  

## <a name="change-visual-studio-settings"></a>Visual Studio の設定を変更する
 
Visual Studio では、**[ツール]** > **[オプション]**  > **[Service Fabric Mesh Tools]\(Service Fabric Mesh ツール\)** > **[全般]** から、次の設定を調整することができます。

- **[プロジェクトを開く際に必要な Docker イメージをプルします]**: プロジェクトの読み込み中にイメージのダウンロードを開始することで、初回のデバッグ実行 (F5) を高速化します。  
- **[Deploy application on project open]\(プロジェクトを開く際にアプリケーションをデプロイします\)**: プロジェクトを開いたときにデプロイ プロセスを開始することで、初回のデバッグ実行 (F5) を高速化します。  
- **[Remove application on project close]\(プロジェクトを閉じたときにアプリケーションを削除します\)**: プロジェクトを閉じたときに Mesh アプリを削除することで、アプリに割り当てられたリソース (CPU、RAM) を解放します。  

Service Fabric Tools の出力ウィンドウに、Visual Studio が "イメージをプルしている"、"ウォーム アップしている"、または "アプリケーションを削除している" というメッセージが表示される場合は、上記の設定が参照されています。 これらの設定は無効にすることもできます。

## <a name="next-steps"></a>次の手順

[Mesh アプリのデバッグに関するチュートリアル](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)を読んでください