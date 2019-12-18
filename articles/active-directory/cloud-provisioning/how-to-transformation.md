---
title: Azure AD Connect クラウド プロビジョニングの変換
description: このドキュメントでは、変換により既定の属性マッピングを変更する方法について説明します。
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 0d37fdb4ad0d385914aecd4ca62be498c5c0e7c5
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795522"
---
# <a name="transformations"></a>変換

変換すると、クラウド プロビジョニングを使用した Azure AD との属性の同期方法の既定の動作を変更できます。  

このタスクを実行するには、スキーマを編集してから、Web 要求を使用して再送信する必要があります。

クラウド プロビジョニング属性の詳細については、[Azure AD スキーマについて](concept-attributes.md)の記事を参照してください。


## <a name="retrieve-the-schema"></a>スキーマを取得する
スキーマを取得するには、[スキーマの表示](concept-attributes.md#viewing-the-schema)方法に関する記事に記載されている手順を使用します。 


## <a name="custom-attribute-mapping"></a>カスタム属性のマッピング
カスタム属性のマッピングを追加するには、次の手順を使用します。

1. スキーマを [Visual Studio Code](https://code.visualstudio.com/) などのテキスト エディターまたはコード エディターにコピーします。  
2. スキーマ ![](media/how-to-transformation/transform1.png) で更新するオブジェクトを見つけます。</br>
3. ユーザー オブジェクトで **ExtensionAttribute3** のコードを見つけます。

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 4.  企業の属性が ExtensionAttribute3 にマップされるようにコードを編集します。
    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([company])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[company]",
                                                "name": "company",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
 5. スキーマを Graph Explorer にコピーして、要求の種類を PUT に変更し、 **[クエリの実行]** を行います。  
 ![](media/how-to-transformation/transform2.png)</br>
 6.  次に Azure portal で、クラウド プロビジョニング構成に移動し、**プロビジョニングを再開する** を押します。
 ![](media/how-to-transformation/transform3.png)</br>
 7.  しばらくしてから、Graph Explorer で次のクエリを実行して、属性が設定されていることを確認します: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}`。
 8.  これで、値が表示されます。
 ![](media/how-to-transformation/transform4.png)</br>

## <a name="custom-attribute-mapping-with-function"></a>関数を使用したカスタム属性マッピング
より高度なマッピングでは、データを操作し、組織のニーズに合わせて属性の値を作成できる関数を使用できます。

このタスクを実行するには、上記の手順に従って、最終的な値の作成に使用する関数を編集します。

式の構文と例の詳細については、「[Azure Active Directory における属性マッピングの式の書き方](reference-expressions.md)」を参照してください。


## <a name="next-steps"></a>次の手順 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)
