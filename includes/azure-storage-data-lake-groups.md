---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017683"
---
ACL エントリでの割り当て済みのプリンシパルとして、常に [Azure AD セキュリティ グループ](../articles/active-directory/fundamentals/active-directory-manage-groups.md)を使用します。 個々のユーザーまたはサービス プリンシパルを直接割り当てることを抑止します。 この構造体を使用すると、ユーザーまたはサービス プリンシパルを追加したり削除したりすることができます。ACL をディレクトリ構造全体に再適用する必要はありません。 代わりに、適切な Azure AD セキュリティ グループからユーザーとサービス プリンシパルを追加または削除することができます。 

グループを設定するには、さまざまな方法があります。 たとえば、サーバーによって生成されたログ データを保持する **/LogData** という名前のディレクトリがあるとします。 Azure Data Factory (ADF) により、そのフォルダーにデータが取り込まれます。 サービス エンジニアリング チームの特定のユーザーは、ログをアップロードし、このフォルダーの他のユーザーを管理します。また、さまざまな Databricks クラスターによって、そのフォルダーのログが分析されます。 

これらのアクティビティを有効にするには、`LogsWriter` グループと `LogsReader` グループを作成します。 その後、次のようにアクセス許可を割り当てることができます。

- `LogsWriter` グループを、`rwx` というアクセス許可で、 **/LogData** ディレクトリの ACL に追加します。
- `LogsReader` グループを、`r-x` というアクセス許可で、 **/LogData** ディレクトリの ACL に追加します。
- ADF に対するサービス プリンシパル オブジェクトまたは管理サービス ID (MSI) を、`LogsWriters` グループに追加します。
- サービス エンジニアリング チームのユーザーを、`LogsWriter` グループに追加します。
- Databricks に対するサービス プリンシパル オブジェクトまたは MSI を、`LogsReader` グループに追加します。

サービス エンジニアリング チームのユーザーが退職した場合は、`LogsWriter` グループから削除するだけで済みます。 そのユーザーをグループに追加せず、代わりにそのユーザーに専用の ACL エントリを追加した場合は、その ACL エントリを **/LogData** ディレクトリから削除する必要があります。 また、 **/LogData** ディレクトリのディレクトリ階層全体のすべてのサブディレクトリとファイルからも、エントリを削除する必要があります。 

グループの作成とメンバーの追加については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。