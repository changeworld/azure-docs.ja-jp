---
title: ワークスペースとは - Azure Healthcare API
description: この記事では、Azure Healthcare API ワークスペースの概要について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 07/12/2021
ms.author: ginle
ms.openlocfilehash: 6eb5fa5186b7f28954724f27186b972f7efb08b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787453"
---
# <a name="what-is-healthcare-apis-preview-workspace"></a>医療 API (プレビュー) ワークスペースとは

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

Azure Healthcare API ワークスペースは、高速ヘルスケア相互運用性リソース (FHIR®) サービス、Digital Imaging and Communications in Health (DICOM®) サービス、モノのインターネット (IoT) コネクタなど、すべての医療サービス インスタンスの論理コンテナーです。 ワークスペースでは、保護された正常性情報を移動できるコンプライアンス境界 (HIPAA、HITRUST) も作成されます。

ワークスペース内で複数のデータ サービスをプロビジョニングできます。設計上、それらはシームレスに連携します。 ワークスペースを使用すると、すべての医療 API インスタンスを整理し、該当する基になるすべてのデータセットとサービス間で共有される特定の構成設定を管理できます。

## <a name="workspace-provisioning-process"></a>ワークスペースのプロビジョニング プロセス
 
リソース グループ内に 1 つ以上のワークスペースを作成するには、Azure portalデプロイ スクリプトを使用します。 階層型サービス ツリーの親項目として、1 つ以上の子サービス インスタンスを作成する前に、まず Healthcare API ワークスペースを作成する必要があります。   
 
ワークスペース内のすべての子サービス インスタンスが削除されていない限り、ワークスペースを削除できません。 この機能は、サービス インスタンスが誤って削除されるのを防ぐのに役立ちます。 ただし、ワークスペース リソース グループが削除された場合は、ワークスペース リソース グループ内のすべてのワークスペースと子サービス インスタンスが削除されます。 

## <a name="workspace-and-azure-region-selection"></a>ワークスペースと Azure リージョンの選択 
 
ワークスペースを作成する場合は、Azure リージョンに対してワークスペースを構成する必要があります。これは、リソース グループと同じでも異なる場合があります。 ワークスペースの作成後にリージョンを変更することはできません。 各ワークスペース内では、すべての医療 API サービス (FHIR サービス、DICOM サービス、IoT Connector サービス) をワークスペースのリージョンに作成する必要があります。また、別のワークスペースに移動することはできません。 

## <a name="workspace-and-azure-healthcare-apis-service-instances"></a>ワークスペースと Azure Healthcare API サービス インスタンス 

Azure Healthcare API ワークスペースが作成されると、サービス インスタンスから 1 つ以上のサービス インスタンスを作成Azure portal。 同じ種類または異なる種類の複数のサービス インスタンスを 1 つのワークスペースに作成できます。 ワークスペース内では、ワークスペースと構成設定のセクションで説明されている子サービス インスタンスに共有構成設定を適用できます。

[![Azure リソース グループ ](media/azure-resource-group.png) ](media/azure-resource-group.png#lightbox)

さらに、ワークスペースは、一般にコードとしてのインフラストラクチャ (IaC) と呼ばれるプロセスAzure Resource Managerデプロイ テンプレートを使用して作成できます。 このオプションを使用すると、ARM テンプレートをカスタマイズし、組み合わせた手順でワークスペースの作成とサービス インスタンスの作成を完了できます。 

PowerShell、CLI、Terraform スクリプト、または .NET SDK を使用して、医療 API サービスをデプロイできます。 ワークスペースにサービス インスタンスを作成するには、[作成] (FHIR サービス、DICOM サービス、または IoT コネクタ) を選択し、作成するサービス インスタンスのアカウントの詳細を入力します。


## <a name="fhir-service"></a>FHIR サービス

FHIR サービスには、FHIR データ形式のデータ アクセスとストレージのために、Azure の FHIR API とエンドポイントが含まれています。 FHIR サービスは、セキュリティで保護された準拠のクラウド環境で Protected Health Information (PHI) を管理します。 FHIR サービスをデプロイすると、HL7 によって公開された [FHIR](https://www.hl7.org/fhir/index.html) 相互運用可能なデータ標準に基づいて、複数のシステムから Azure Cloud に医療医療データを統合できます。 詳細については [、「FHIR サービスについて」を参照してください](./fhir/overview.md)。

## <a name="dicom-service"></a>DICOM サービス

DICOM サービスをデプロイして、任意の DICOMwebTM 対応システムから医療画像データをクラウドに取り込む。 詳細については [、「DICOM サービスの概要」を参照してください](dicom/dicom-services-overview.md)。

## <a name="iot-connector"></a>IoT コネクタ

IoT Connectorサービスを使用すると、高頻度の IoT デバイス データをスケーラブルで安全で準拠した方法で FHIR サービスに取り込めます。 詳細については [、IoT コネクタのドキュメント ページを参照してください](./iot/index.yml)。
 
## <a name="workspace-configuration-settings"></a>ワークスペースの構成設定

一部の機能はワークスペース レベルで構成され、そのワークスペース内のすべての子サービスに適用されます。

### <a name="application-monitoring"></a>アプリケーションの監視

Azure Monitor には、アプリケーションとサービスの可用性およびパフォーマンスを最大化する効果があります。 クラウドおよびオンプレミス環境のテレメトリを収集、分析し、対応する包括的なソリューションを提供します。 この情報を使用すると、アプリケーションのパフォーマンスに関する分析情報が得られるので、アプリケーションに影響を与える問題と、アプリケーションが依存するリソースを事前に特定して解決できます。 詳細については、概要Azure Monitorドキュメント [Azure Monitor参照](../azure-monitor/index.yml) してください。

### <a name="role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC)

Azure のロールベースのアクセス制御 (Azure RBAC) は、Azure リソースのアクセス権を詳細に管理できるシステムです。 Azure RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。 さらに、Azure リソースにアクセスできるユーザー、それらのリソースで実行できる操作、アクセスできる領域を管理するのに役立ちます。 詳細については [、Azure RBAC のドキュメントを参照](../role-based-access-control/index.yml) してください。


## <a name="next-steps"></a>次の手順

Azure Healthcare API の使用を開始するには、5 分間のクイック スタートに従ってワークスペースをデプロイします。

>[!div class="nextstepaction"]
>[ワークスペースをワークスペースにデプロイAzure portal](healthcare-apis-quickstart.md)