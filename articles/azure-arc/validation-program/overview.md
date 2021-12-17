---
title: Azure Arc 対応サービスの検証の概要
description: Arc 対応 Kubernetes、データ サービス、およびクラスター拡張機能に準拠するための Azure Arc 検証プロセスについて説明します。
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 1cf208b9a476f4a2a16df157ebfa0bb7661cfc1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788228"
---
# <a name="overview-of-azure-arc-enabled-service-validation"></a>Azure Arc 対応サービスの検証の概要

Azure Arc 対応サービスは検証済みのプラットフォーム上で実行することをお勧めします。 この記事では、さまざまな Azure Arc 対応コンポーネントを検証する方法について説明するコンテンツを示します。 

現在、検証済みソリューションは、Kubernetes とデータ サービスのパートナーから利用できます。

## <a name="kubernetes"></a>Kubernetes

Azure Arc 対応 Kubernetes は、すべての Cloud Native Computing Foundation (CNCF) 認定 Kubernetes クラスターで動作します。 Azure Arc チームは、主要な業界の Kubernetes オファリング プロバイダーと協力して、それらの [Kubernetes ディストリビューション](../kubernetes/validation-program.md)で Azure Arc 対応 Kubernetes を検証しています。 これらのプロバイダーによって将来リリースされる Kubernetes ディストリビューションのメジャーおよびマイナー バージョンは、Azure Arc 対応 Kubernetes との互換性が検証されます。

## <a name="data-services"></a>データ サービス

また、OEM (相手先ブランド供給) パートナーやストレージ プロバイダーと提携して、[Azure Arc 対応データ サービス](../data/validation-program.md) ソリューションを検証しています。

## <a name="validation-process"></a>検証プロセス

Azure Arc 検証プロセスは、GitHub から入手できます。 Azure Arc、テスト ハーネス、戦略を使用してオファリングを検証する方法の詳細については、GitHub の [Azure Arc 検証プロセス](https://github.com/Azure/azure-arc-validation/)を参照してください。

## <a name="next-steps"></a>次のステップ

* [検証済みの Kubernetes ディストリビューション](../kubernetes/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)

* [データ サービス用の検証済み Kubernetes ディストリビューション](../data/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)
