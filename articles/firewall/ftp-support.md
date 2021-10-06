---
title: Azure Firewall の FTP のサポート
description: 既定では、アクティブ FTP は Azure Firewall で無効になっています。 PowerShell、CLI、および ARM テンプレートを使用して有効にできます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: victorh
ms.openlocfilehash: 7aeb7c596f1b4ca286cc9fce22965418c7c61c85
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084205"
---
# <a name="azure-firewall-ftp-support"></a>Azure Firewall の FTP のサポート

FTP をサポートするには、ファイアウォールで次の重要な側面を考慮する必要があります。
- FTP モード – アクティブまたはパッシブ
- クライアント/サーバーの場所 - インターネットまたはイントラネット
- Flowk 方向 - 受信または送信。 

Azure Firewall は、アクティブ FTP シナリオとパッシブ FTP シナリオの両方をサポートしています。 FTP モードの詳細については、「[Active FTP とパッシブ FTP、明確な説明](https://slacksite.com/other/ftp.html)」を参照してください。 

既定では、パッシブ FTP が有効になり、FTP ポート コマンドを使用して FTP の直帰攻撃から保護するためにアクティブ FTP のサポートが無効になっています。 

ただし、Azure PowerShell、Azure CLI、または Azure ARM テンプレートを使用してデプロイする場合は、アクティブ FTP を有効にすることができます。 Azure Firewall では、アクティブ FTP とパッシブ FTP の両方を同時にサポートできます。 *ActiveFTP* は、Premium SKU と Standard SKU、セキュリティで保護されたハブと VNet ファイアウォールの両方、およびファイアウォール ポリシーとクラシック ルールを使用する場合に有効にできるファイアウォール プロパティです。


## <a name="supported-scenarios"></a>サポートされるシナリオ

次の表には、さまざまな FTP シナリオをサポートするために必要な構成が示されています。


|ファイアウォール シナリオ  |アクティブ FTP モード   |パッシブ FTP モード  |
|---------|---------|---------|
|VNet-VNet     |構成するネットワーク規則:<br>- ソース VNet から Dest IP ポート 21 への許可<br>- Dest IP ポート 20 からソース VNet への許可 |構成するネットワーク規則:<br>- ソース VNet から Dest IP ポート 21 への許可<br>- ソース VNet から Dest IP \<Range of Data Ports> への許可|
|送信 VNet - インターネット<br><br>(VNet の FTP クライアント、インターネット上のサーバー)      |サポート対象外 *|**事前条件**: 異なる送信元 IP アドレスからのデータ チャネルとコントロール チャネルを受け入れるように FTP サーバーを構成します。 または、単一のパブリック IP アドレスを使用して Azure Firewall を構成します。<br><br>構成するネットワーク規則:<br>- ソース VNet から Dest IP ポート 21 への許可<br>- ソース VNet から Dest IP \<Range of Data Ports> への許可 |
|受信 DNAT<br><br>(インターネット上の FTP クライアント、VNet 内のサーバー)      |構成する DNAT ルール:<br>- インターネット ソースから VNet IP ポート 21 への DNAT<br><br>構成するネットワーク規則:<br>- VNet IP ポート 20 からインターネット ソースへの許可 |**事前条件**:<br>異なる送信元 IP アドレスからのデータ チャネルとコントロール チャネルを受け入れるように FTP サーバーを構成します。<br><br>ヒント: Azure Firewall は、限られた数の DNAT ルールをサポートします。 データ チャネルで小さなポート範囲を使用する FTP サーバーを構成することが重要です。<br><br>構成する DNAT ルール:<br>- インターネット ソースから VNet IP ポート 21 への DNAT<br>- インターネット ソースから VNet IP \<Range of Data Ports> への DNAT |

\* FTP クライアントがインターネット上の FTP サーバーに到達する必要がある場合、アクティブ FTP は機能しません。 アクティブ FTP では、FTP クライアントからの PORT コマンドを使用して、データ チャネルに使用する IP アドレスとポートを FTP サーバーに指示します。 この PORT コマンドは、変更できないクライアントのプライベート IP アドレスを使用します。 Azure Firewall を通過するクライアント側のトラフィックはインターネット ベースの通信で NATED されます。そのため、PORT コマンドは FTP サーバーによって無効と見なされます。 これは、クライアント側 NAT と一緒に使用する場合の Active FTP の一般的な制限です。 


## <a name="deploy-using-azure-powershell"></a>Azure PowerShell を使用したデプロイ

Azure PowerShell を使用してデプロイするには、`AllowActiveFTP` パラメーターを使用します。 詳細については、[アクティブ FTP の許可を使用してファイアウォールを作成する](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)方法に関する記事を参照してください。

## <a name="deploy-using-azure-cli"></a>Azure CLI を使用したデプロイ

Azure CLI を使用してデプロイするには、`--allow-active-ftp` パラメーターを使用します。 詳細については、[Az.Network のファイアウォールの作成](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters)に関する記事を参照してください。 

## <a name="deploy-azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM) テンプレートをデプロイする

ARM テンプレートを使用してデプロイするには、`AdditionalProperties` フィールドを使用します。

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
詳細については、[Microsoft.Network の azureFirewalls](/azure/templates/microsoft.network/azurefirewalls) に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Firewall のデプロイ方法については、「[チュートリアル: Azure PowerShell を使用して Azure Firewall のデプロイと構成を行う](deploy-ps.md)」を参照してください。