---
title: Azure Stack での API バージョンのプロファイルの管理 | Microsoft Docs
description: Azure Stack での API バージョンのプロファイルについて説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: ba8778bad9ed1b8ec095da084ffcec79658bdea8
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41948136"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Azure Stack での API バージョンのプロファイルの管理

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

API プロファイルでは、Azure リソース プロバイダーと Azure REST エンドポイントの API バージョンが指定されます。 API プロファイルを使用して、さまざまな言語でカスタム クライアントを作成できます。 各クライアントは API プロファイルを使用して、適切なリソース プロバイダーと Azure Stack の API バージョンに連絡します。

各リソース プロバイダー API の正確にどのバージョンが Azure Stack と互換性があるかを選別せずに、Azure リソース プロバイダーと連携するアプリを作成できます。 アプリケーションをプロファイルに整合させるだけで、SDK によって適切な API バージョンに戻されます。

このトピックは、以下に役立ちます。

 - Azure Stack の API プロファイルの理解
 - API プロファイルを使用してソリューションを開発する方法
 - コード固有のガイダンスが提供されている場所

## <a name="summary-of-api-profiles"></a>API プロファイルの概要

- API プロファイルは、一連の Azure リソース プロバイダーとその API バージョンを表すために使用されます。
- API プロファイルは、複数の Azure クラウド間でテンプレートを作成するユーザー向けに作成されました。 プロファイルは、互換性があり、安定したインターフェイスに対するニーズを満たすよう設計されています｡
- プロファイルは、1 年に 4 回リリースされます。
- 次の 3 つのプロファイル名前付け規則が使用されます。
    - **latest**  
        グローバル Azure でリリースされた最新の API バージョンを含みます。
    - **yyyy-mm-dd-hybrid**  
    年 2 回リリースされ、複数のクラウド間での一貫性と安定性に重点が置かれています。 このプロファイルは Azure Stack との最適な互換性を目指しています｡
    - **yyyy-mm-dd-profile** 最適な安定性と最新の機能の中間に位置します。

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API プロファイルと Azure Stack の互換性

最新の API プロファイルは Azure Stack との互換性がありません｡ 次の名前付け規則を使用して、Azure Stack ソリューションにどのプロファイルを使用するか識別できます。

**最新**  
このプロファイルはグローバル Azure にある最新の API バージョンであり､Azure Stack では機能しません｡ **最新** このプロファイルには､最も多くの重大な変更が含まれています｡ このプロファイルでは､安定性および他のクラウドとの互換性は配慮されていません｡ 最新の API バージョンを使用する場合は､**最新**プロファイルを使用してください。

**yyyy-mm-dd-hybrid**  
このプロファイルは､毎年 3月と 9 月にリリースされます｡ このプロファイルには最適な安定性と各種クラウドとの互換性があります｡ **Yyyy-mm-dd-hybrid** プロファイルは､グローバル Azure と Microsoft Azure Stack の両方を対象に設計されています｡ このプロファイルに記載されている Azure API バージョンは､Azure Stack に記載されているものと同じです｡ このプロファイルを利用して､ハイブリッド クラウド ソリューション向けのコードを開発することができます｡

**yyyy-mm-dd-profile**  
このプロファイルは､グローバル Azure 向けに 6 月と 12 月にリリースされます｡ このプロファイルは Azure Stack では機能しません｡一般的に重大な変更が多数含まれています｡ 最適な安定性と最新の機能の間に位置しますが、**最新**プロファイルとこのプロファイルの違いは､API のリリース時期に関係なく、**最新**プロファイルはつねに最新のバージョンで構成されることです｡ たとえば､Compute API 用に新しい API バージョンが明日作成されるとすると､**最新**プロファイルには､その API バージョンが記載されますが､このプロファイルはすでに存在するため、**yyyy-mm-dd-profile** には記載されません｡  **yyyy-mm-dd-profile** は、6 月または 12 月の前にリリースされた最新のバージョンが対応します｡

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager API プロファイル

Azure Stack は､グローバル Azure にある API バージョンのうちの最新バージョンを使用しません｡ 独自のソリューションを作成する場合は、Azure Stack と互換性のある Azure の各リソース プロバイダーの API バージョンを特定する必要があります。

すべてのリソース プロバイダーや Azure Stack でサポートされている特定のバージョンを調査する代わりに、API プロファイルを使用できます。 プロファイルは、リソース プロバイダーと API バージョンのセットを指定します。 SDK、または SDK でビルドされたツールによって、プロファイルで指定されたターゲット api-version に戻されます。 API プロファイルを使用すると、テンプレート全体に適用されるプロファイル バージョンを指定でき、AAzure Resource Manager によってリソースの適切なバージョンが選択されます。

API プロファイルは、Azure Resource Manager を使用するツール (PowerShell、Azure CLI、SDK で提供されるコード、Microsoft Visual Studio など) と連携します。 ツールや SDK はプロファイルを使用して、アプリケーションのビルド時に含むモジュールやライブラリのバージョンを読み取ります。

たとえば、PowerShell を使用して **Microsoft.Storage** リソース プロバイダーを使ってストレージ アカウントを作成する場合 (ここでは api-version 2016-03-30 と、Microsoft.Compute リソース プロバイダーと api-version 2015-12-01 を使用した VM がサポートされます)、ストレージ用にどの PowerShell モジュールが 2016-03-30 をサポートしているか、コンピューティング用にどのモジュールが 2015-02-01 をサポートしているか調べてからインストールする必要があります。 その代わりに、プロファイルを使用することができます。 コマンドレット **Install-Profile *profilename*** を使用すると、PowerShell によってモジュールの適切なバージョンが読み込まれます。

同様に、Python SDK を使用して Python ベースのアプリケーションをビルドする場合、プロファイルを指定できます。 SDK によって、スクリプトで指定済みのリソース プロバイダーの適切なモジュールが読み込まれます。

開発者として、ソリューションの作成に集中できます。 api-versions、リソース プロバイダー、連携させるクラウドを調査する代わりに、プロファイルを使用すれば、そのプロファイルに対応するすべてのクラウドにわたってコードが機能することを把握できます。

## <a name="api-profile-code-samples"></a>API プロファイルのコード サンプル

プロファイルを使用して、Azure Stack を使って目的の言語でソリューションを統合するためのコード サンプルが用意されています。 現在、次の言語用のガイダンスとサンプルを使用できます。

- **PowerShell**  
PowerShell ギャラリーから入手できる **AzureRM.Bootstrapper** モジュールを使用して、API バージョンのプロファイルを操作するために必要な PowerShell コマンドレットを取得できます。 詳しくは、「[Use API version profiles for PowerShell (PowerShell 向け API バージョン プロファイルの使用)](azure-stack-version-profiles-powershell.md)」をご覧ください。
- **Azure CLI 2.0**  
Azure Stack 固有の API バージョンのプロファイルを使用するようにお使いの環境の構成を更新できます。 詳しくは、「[Azure Stack での Azure CLI 2.0 による API バージョンのプロファイルの使用](azure-stack-version-profiles-azurecli2.md)」をご覧ください。
- **GO**  
GO SDK では、プロファイルはさまざまな種類のリソースと多様なサービスのさまざまなバージョンの組み合わせが用意されています。 プロファイルは、**YYYY-MM-DD** 形式のバージョンと共に profiles/ path で利用できます。 詳しくは、「[Use API version profiles for GO (GO 向け API バージョン プロファイルの使用)](azure-stack-version-profiles-go.md)」をご覧ください。
- **Ruby**  
Azure Stack Resource Manager 向けの Ruby SDK には､インフラストラクチャの構築と管理に役立つツールが用意されています。 SDK のリソース プロバイダには､Ruby 言語を使用したコンピュートや仮想ネットワーク､ストレージがあります｡ 詳しくは、「[Use API version profiles with Ruby](azure-stack-version-profiles-ruby.md)」をご覧ください。
- **Python**  
- Python SDK では、Azure Stack とグローバル Azure などの異なるクラウド プラットフォームをターゲットとする API バージョン プロファイルをサポートします。 ハイブリッド クラウド向けのソリューションの作成時に API プロファイルを使用できます。 詳しくは、「[Azure Stack での Python による API バージョンのプロファイルの使用](azure-stack-version-profiles-python.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* [PowerShell for Azure Stack のインストール](azure-stack-powershell-install.md)
* [Azure Stack ユーザーの PowerShell 環境の構成](azure-stack-powershell-configure-user.md)
* [プロファイルでサポートされているリソース プロバイダーの API バージョンの詳細を確認します](azure-stack-profiles-azure-resource-manager-versions.md)。