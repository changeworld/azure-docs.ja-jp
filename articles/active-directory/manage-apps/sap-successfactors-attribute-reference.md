---
title: SAP SuccessFactors 属性のリファレンス | Microsoft Docs
description: SuccessFactors-HR ドリブン プロビジョニングによってサポートされる SuccessFactors の属性について説明します。
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: afb77f2d-5ddd-4c2e-a840-09021b0efef1
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 6f7497e62be0036c13d5c33fa82301469df16f26
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971113"
---
# <a name="sap-successfactors-attribute-reference"></a>SAP SuccessFactors 属性のリファレンス

## <a name="supported-successfactors-entities-and-attributes"></a>サポートされている SuccessFactors エンティティと属性

以下の表は、次の 2 つのプロビジョニング アプリによってサポートされる SuccessFactors 属性の一覧を示しています。 
* [Active Directory ユーザー プロビジョニングに対する SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
* [Azure AD ユーザー プロビジョニングに対する SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 

| \# | SuccessFactors エンティティ                  | SuccessFactors 属性     | 操作の種類 |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | 読み取り           |
| 2  | PerPerson                              | personId                     | 読み取り           |
| 3  | PerPerson                              | perPersonUuid                | 読み取り           |
| 4  | PerPersonal                            | displayName                  | 読み取り           |
| 5  | PerPersonal                            | firstName                    | 読み取り           |
| 6  | PerPersonal                            | gender                       | 読み取り           |
| 7  | PerPersonal                            | lastName                     | 読み取り           |
| 8  | PerPersonal                            | middleName                   | 読み取り           |
| 9  | PerPersonal                            | preferredName                | 読み取り           |
| 10 | User                                   | addressLine1                 | 読み取り           |
| 11 | User                                   | addressLine2                 | 読み取り           |
| 12 | User                                   | addressLIne3                 | 読み取り           |
| 13 | User                                   | businessPhone                | 読み取り           |
| 14 | User                                   | cellPhone                    | 読み取り           |
| 15 | User                                   | city                         | 読み取り           |
| 16 | User                                   | country                      | 読み取り           |
| 17 | User                                   | custom01                     | 読み取り           |
| 18 | User                                   | custom02                     | 読み取り           |
| 19 | User                                   | custom03                     | 読み取り           |
| 20 | User                                   | custom04                     | 読み取り           |
| 21 | User                                   | custom05                     | 読み取り           |
| 22 | User                                   | custom06                     | 読み取り           |
| 23 | User                                   | custom07                     | 読み取り           |
| 24 | User                                   | custom08                     | 読み取り           |
| 25 | User                                   | custom09                     | 読み取り           |
| 26 | User                                   | custom10                     | 読み取り           |
| 27 | User                                   | custom11                     | 読み取り           |
| 28 | User                                   | custom12                     | 読み取り           |
| 29 | User                                   | custom13                     | 読み取り           |
| 30 | User                                   | custom14                     | 読み取り           |
| 31 | User                                   | empId                        | 読み取り           |
| 32 | User                                   | homePhone                    | 読み取り           |
| 33 | User                                   | jobFamily                    | 読み取り           |
| 34 | User                                   | nickname                     | 読み取り           |
| 35 | User                                   | state                        | 読み取り           |
| 36 | User                                   | timeZone                     | 読み取り           |
| 37 | User                                   | username                     | 読み取り           |
| 38 | User                                   | zipCode                      | 読み取り           |
| 39 | PerPhone                               | areaCode                     | 読み取り           |
| 40 | PerPhone                               | countryCode                  | 読み取り           |
| 41 | PerPhone                               | 拡張機能                    | 読み取り           |
| 42 | PerPhone                               | phoneNumber                  | 読み取り           |
| 43 | PerPhone                               | phoneType                    | 読み取り           |
| 44 | PerEmail                               | emailAddress                 | 読み取り、書き込み    |
| 45 | PerEmail                               | emailType                    | 読み取り           |
| 46 | EmpEmployment                          | firstDateWorked              | 読み取り           |
| 47 | EmpEmployment                          | lastDateWorked               | 読み取り           |
| 48 | EmpEmployment                          | userId                       | 読み取り           |
| 49 | EmpEmployment                          | isContingentWorker           | 読み取り           |
| 50 | EmpJob                                 | countryOfCompany             | 読み取り           |
| 51 | EmpJob                                 | emplStatus                   | 読み取り           |
| 52 | EmpJob                                 | endDate                      | 読み取り           |
| 53 | EmpJob                                 | startDate                    | 読み取り           |
| 54 | EmpJob                                 | jobTitle                     | 読み取り           |
| 55 | EmpJob                                 | position                     | 読み取り           |
| 65 | EmpJob                                 | customString13               | 読み取り           |
| 56 | EmpJob                                 | managerId                    | 読み取り           |
| 57 | EmpJob\.BusinessUnit                   | businessUnit                 | 読み取り           |
| 58 | EmpJob\.BusinessUnit                   | businessUnitId               | 読み取り           |
| 59 | EmpJob\.Company                        | company                      | 読み取り           |
| 60 | EmpJob\.Company                        | companyId                    | 読み取り           |
| 61 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode           | 読み取り           |
| 62 | EmpJob\.CostCenter                     | costCenter                   | 読み取り           |
| 63 | EmpJob\.CostCenter                     | costCenterId                 | 読み取り           |
| 64 | EmpJob\.CostCenter                     | costCenterDescription        | 読み取り           |
| 65 | EmpJob\.Department                     | department                   | 読み取り           |
| 66 | EmpJob\.Department                     | departmentId                 | 読み取り           |
| 67 | EmpJob\.Division                       | division                     | 読み取り           |
| 68 | EmpJob\.Division                       | divisionId                   | 読み取り           |
| 69 | EmpJob\.JobCode                        | jobCode                      | 読み取り           |
| 70 | EmpJob\.JobCode                        | jobCodeId                    | 読み取り           |
| 71 | EmpJob\.Location                       | LocationName                 | 読み取り           |
| 72 | EmpJob\.Location                       | officeLocationAddress        | 読み取り           |
| 73 | EmpJob\.Location                       | officeLocationCity           | 読み取り           |
| 74 | EmpJob\.Location                       | officeLocationCustomString4  | 読み取り           |
| 75 | EmpJob\.Location                       | officeLocationZipCode        | 読み取り           |
| 76 | EmpJob\.PayGrade                       | payGrade                     | 読み取り           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | 読み取り           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | 読み取り           |


## <a name="default-attribute-mapping"></a>既定の属性マッピング

次の表は、上に示した SuccessFactors 属性と AD/Azure AD 属性の間の既定の属性マッピングを示しています。 Azure AD プロビジョニング アプリの [マッピング] ブレードでは、この既定のマッピングを変更して、上記の一覧の属性を含めることができます。 

| \# | SuccessFactors エンティティ                  | SuccessFactors 属性 | 既定の AD/Azure AD 属性マッピング   | 処理に関する注釈                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | 一致する属性として使用されます                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[マップされない\- ソース アンカーとして使用される\] | 初期同期中、プロビジョニング サービスは、personUuid を既存の objectGuid\ にリンクします。  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | firstName                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | lastName                 | sn                                      | NA                                                                                           |
| 6  | User                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | User                                   | city                     | l                                       | NA                                                                                           |
| 8  | User                                   | country                  | co                                      | NA                                                                                           |
| 9  | User                                   | state                    | st                                      | NA                                                                                           |
| 10 | User                                   | username                 | samAccountName                          | NA                                                                                           |
| 11 | User                                   | zipCode                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | title                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob\.Company\.CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob\.Department                     | department               | department                              | NA                                                                                           |
| 17 | EmpJob\.Division                       | division                 | company                                 | NA                                                                                           |
| 18 | EmpJob\.Location                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | EmpJob\.Location                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | activeEmploymentsCount=0 の場合、account\ を無効にします。                                           |

