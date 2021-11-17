---
title: IP ファイアウォールの構成
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search サービスへのアクセスを特定の IP アドレスに制限するための IP 制御ポリシーを構成します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: e403a71525a8400f47dee01c14ac192c13ab3826
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223190"
---
# <a name="configure-an-ip-firewall-for-azure-cognitive-search"></a>Azure Cognitive Search 用に IP ファイアウォールを構成する

Azure Cognitive Search では、Azure 仮想ネットワーク セキュリティ グループにある IP 規則と同様に、ファイアウォール経由の受信アクセスに関する IP 規則をサポートしています。 IP 規則を利用することで、検索サービスのアクセスを、承認された一連のマシンとクラウド サービスに制限できます。 これらの承認された一連のマシンやサービスから検索サービスに格納されているデータにアクセスするには、引き続き呼び出し側で有効な認可トークンを提示する必要があります。

この記事で説明するように、Azure portal では Basic レベル以上でプロビジョニングされた検索サービスに対して IP 規則を設定できます。 また、[Management REST API バージョン 2020-03-13](/rest/api/searchmanagement/)、[Azure PowerShell](/powershell/module/az.search)、または [Azure CLI](/cli/azure/search) を使用することもできます。

<a id="configure-ip-policy"></a> 

## <a name="set-ip-ranges-in-azure-portal"></a>Azure portal で IP 範囲を設定する

Azure portal で IP アクセス制御ポリシーを設定するには、Azure Cognitive Search サービスのページに移動し、左側のナビゲーション ウィンドウで **[ネットワーク]** を選択します。 エンドポイント ネットワーク接続は **[パブリック アクセス]** である必要があります。 接続が **[プライベート アクセス]** または **[共有プライベート アクセス]** に設定されている場合は、プライベート エンドポイント経由でのみ検索サービスにアクセスできます。

:::image type="content" source="media/service-configure-firewall/azure-portal-firewall.png" alt-text="Azure portal で IP ファイアウォールを構成する方法を示すスクリーンショット" border="true":::

Azure portal では、CIDR 形式で IP アドレスと IP アドレス範囲を指定できます。 CIDR 表記の例として、8.8.8.0/24 があります。これは、8.8.8.0 から 8.8.8.255 の範囲の IP を表しています。

Azure Cognitive Search サービスに対して IP アクセス制御ポリシーを有効にした後は、許可された IP アドレス範囲リストに含まれていないマシンからのデータ プレーンへの要求はすべて拒否されます。 

## <a name="allow-access-from-azure-portal"></a>Azure Portal からのアクセスを許可する

既定では、IP 規則が構成されていると、Azure portal の一部の機能が無効になります。 サービス レベル情報の表示と管理はできますが、ポータルからインデックス、インデクサー、その他のトップ レベル リソースへのアクセスは制限されます。

ポータルのサービス管理機能を維持するには、 **[例外]** の [アクセスを許可する] オプションを選択します。 または、[VS Code 拡張機能](https://aka.ms/vscode-search)を使用してコンテンツを管理します。

## <a name="allow-access-from-your-client"></a>クライアントからのアクセスを許可する

インデックス作成とクエリの要求を検索サービスにプッシュするクライアント アプリケーションは、IP 範囲で表す必要があります。 Azure では通常、サービスの FQDN に ping を実行して IP アドレスを特定できます (たとえば、`ping <your-search-service-name>.search.windows.net` によって検索サービスの IP アドレスが返されます)。 

クライアントの IP アドレスを指定した場合、要求が完全に拒否されることはありませんが、コンテンツや操作に正常にアクセスするには、認可も必要になります。 次のいずれかの方法を使用して要求を認証します。

+ [キーベースの認証](search-security-api-keys.md)。要求に対して管理またはクエリ API キーが提供されます
+ [ロールベースの認可](search-security-rbac.md)。呼び出し元は検索サービスのセキュリティ ロールのメンバーであり、[登録されたアプリで Azure Active Directory から取得した OAuth トークンを提示します](search-howto-aad.md)。

### <a name="rejected-requests"></a>拒否された要求

許可リストに含まれていない IP アドレスから要求が送信された場合は、追加情報を含まない汎用的な **403 Forbidden** 応答が返されます。

## <a name="next-steps"></a>次のステップ

クライアント アプリケーションが Azure 上の静的 Web アプリである場合は、検索サービスの IP ファイアウォール規則に含める IP 範囲を決定する方法を確認します。

> [!div class="nextstepaction"]
> [Azure App Service における受信 IP アドレスと送信 IP アドレス](../app-service/overview-inbound-outbound-ips.md)