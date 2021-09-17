---
title: DevTest Labs のカスタム イメージと数式の比較 | Microsoft Docs
description: 環境に最適なものを判断できるように、VM ベースとして使用するカスタム イメージと数式の違いについて説明します。
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: f1fdb46decda2f72e64eff95cf57dba002168943
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634005"
---
# <a name="compare-custom-images-and-formulas-in-devtest-labs"></a>DevTest ラボのカスタム イメージと数式を比較する
[カスタム イメージ](devtest-lab-create-template.md)と[数式](devtest-lab-manage-formulas.md)の両方を、[新しく作成した VM](devtest-lab-add-vm.md) のベースとして使用することができます。  カスタム イメージが単に VHD に基づくイメージであるのに対して、数式は VM サイズ、仮想ネットワーク、サブネット、アーティファクトなどの構成済み設定 *に加え*、VHD に基づくイメージであるという重要な相違点があります。 これらの構成済みの設定は、VM の作成時にオーバーライド可能な既定値で設定されます。 

この記事では、カスタム イメージを使用する場合と数式を使用する場合の長所と短所について説明します。  また、「[VM からカスタム イメージを作成する方法](devtest-lab-create-custom-image-from-vm-using-portal.md)」と「[FAQ](devtest-lab-faq.yml)」についても説明します。

## <a name="custom-image-benefits"></a>カスタム イメージの利点
カスタム イメージでは、必要な環境から VM を作成するための静的で不変の方法が提供されます。 

|長所|短所|
|----|----|
|<li>イメージからの VM のスピンアップ後は何も変わらないため、カスタム イメージからすばやく VM をプロビジョニングできます。 つまり、カスタム イメージは設定のない単なるイメージであるため、適用する設定はありません。 <li>1 つのカスタム イメージから作成される VM は同じです。|<li>カスタム イメージの一部の要素を更新する必要がある場合、イメージを再作成する必要があります。 |

## <a name="formula-benefits"></a>数式の利点
  
数式では、必要な構成/設定から VM を作成するための動的な方法が提供されます。

|長所|短所|
|----|----|
|<li>環境の変更は、アーティファクトを介してすぐにキャプチャできます。 たとえば、リリース パイプラインから最新ビットの VM をインストールする必要がある場合、またはリポジトリから最新コードを登録する場合は、最新のビットをデプロイするか、ターゲットのベース イメージと共に数式に最新のコードを登録するアーティファクトを <li>単に指定できます。 VM を作成するためにこの数式が使用される場合は常に、最新のビット/コードが VM にデプロイ/登録されます。  <li>数式では、VM サイズや仮想ネットワークの設定など、カスタム イメージで指定できない既定の設定を定義できます。  <li>数式で保存されている設定は既定値として表示されますが、VM の作成時に変更できます。 |<li> 数式から VM を作成すると、カスタム イメージから VM を作成する場合よりも時間がかかることがあります。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
