---
title: Azure Red Hat OpenShift クラスターの管理者ロール | Microsoft Docs
description: Azure Red Hat OpenShift クラスターの管理者ロールの割り当てと使用
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71712838"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Azure Red Hat OpenShift の顧客管理者ロール

OpenShift クラスターの ARO (Azure Red Hat OpenShift) クラスター管理者であるアカウントでアクセス許可が増え、ユーザーが作成したすべてのプロジェクトにアクセスできます。

お使いのアカウントに osa-customer-admins 認可ロールがバインドされているとき、そのアカウントでプロジェクトを自動管理できます。

> [!Note] 
> osa-customer-admin クラスター ロールは cluster-admin クラスター ロールと同じではありません。


たとえば、一連の動詞 (`create`) に関連付けられているアクションを実行し、一連のリソース名 (`templates`) を操作できます。 これらのロールのその一連の動詞とリソースの詳細を表示するには、次のコマンドを実行します。

`$ oc describe clusterrole/osa-customer-admin`

動詞名では必ずしも oc コマンドに直接マッピングされるわけではなく、多くの場合、実行できる CLI 操作の種類に等しくなります。 たとえば、`list` という動詞があると、与えられたリソース名 (`oc get`) のすべてのオブジェクトを一覧表示できます。一方、`get` という動詞があると、その名前 (`oc describe`) を知っている場合、特定のオブジェクトの詳細を表示できます。

## <a name="how-to-configure-customer-administrator-role"></a>顧客管理者ロールを構成する方法

顧客管理者ロールは、フラグ `--customer-admin-group-id` の指定するクラスター作成中にのみ、構成できます。 Azure Active Directory と管理者グループの構成方法については、ハウツー ガイドの「[Azure Red Hat OpenShift の Azure Active Directory 統合](howto-aad-app-configuration.md)」を参照してください。

## <a name="next-steps"></a>次の手順

osa-customer-admin ロールの構成方法:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift の Azure Active Directory 統合](howto-aad-app-configuration.md)
