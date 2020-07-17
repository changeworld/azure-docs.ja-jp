---
title: Azure Red Hat OpenShift 4 クラスターのサポート ポリシー
description: Red Hat OpenShift 4 のサポート ポリシー要件について説明します。
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7bdcccee3270f9d2b611682a9a59505158a494d2
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204864"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Azure Red Hat OpenShift のサポート ポリシー

Azure Red Hat OpenShift 4 クラスターの一部の構成は、クラスターのサポートの可否に影響する可能性があります。 Azure Red Hat OpenShift 4 では、クラスター管理者が内部クラスター コンポーネントを変更できますが、すべての変更がサポートされているわけではありません。 以下のサポート ポリシーでは、ポリシーに違反する変更と、Microsoft および Red Hat からのサポートが無効になる変更を共有します。

> [!NOTE]
> OpenShift Container Platform で Technology Preview とマークされている機能は、Azure Red Hat OpenShift ではサポートされていません。

## <a name="cluster-configuration-requirements"></a>クラスター構成の要件

* すべての OpenShift Cluster オペレーターは、管理された状態のままである必要があります。 `oc get clusteroperators` を実行して、クラスター オペレーターの一覧を返すことができます。
* クラスターの Prometheus および Alertmanager サービスを削除または変更しないでください。
* Service Alertmanager ルールを削除しないでください。
* OpenShift クラスターのバージョンを変更しないでください。
* Azure Red Hat OpenShift サービス ログ (mdsd ポッド) を削除または変更しないでください。
* 'arosvc.azurecr.io' クラスターのプル シークレットを削除または変更しないでください。
* すべてのクラスター仮想マシンには、少なくとも Azure Resource Manager (ARM) とサービス ログ (Geneva) エンドポイントへの送信インターネット アクセスが必要です。
* Azure Red Hat OpenShift サービスからはプライベート リンク サービスを介してクラスターにアクセスします。  サービス アクセスを削除または変更しないでください。
* 非 RHCOS コンピューティング ノードはサポートされていません。 たとえば、RHEL コンピューティング ノードを使用することはできません。

## <a name="supported-virtual-machine-sizes"></a>サポートされる仮想マシンのサイズ

Azure Red Hat OpenShift 4 は、次の仮想マシン サイズのワーカー ノード インスタンスをサポートしています。

### <a name="general-purpose"></a>汎用

|系列|サイズ|vCPU|メモリ:GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>メモリ最適化

|系列|サイズ|vCPU|メモリ:GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>コンピューティング最適化

|系列|サイズ|vCPU|メモリ:GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|
