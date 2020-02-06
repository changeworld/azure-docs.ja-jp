---
title: Azure SDK for Node.js を使用して Azure Data Lake Analytics を管理する
description: この記事では、Azure SDK for Node.js を使用して、Data Lake Analytics のアカウント、データ ソース、ジョブおよびユーザーを管理する方法について説明します。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 21d59f6543df7fe9d1fad47a006260bff9c93e2b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988058"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Azure SDK for Node.js を使用して Azure Data Lake Analytics を管理する
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

この記事では、Azure SDK for Node.js を使用して記述されたアプリを使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、ジョブを管理する方法について説明します。 

次のバージョンがサポートされています。
* **Node.js バージョン:0.10.0 以降**
* **アカウント用の REST API のバージョン:2015-10-01-preview**
* **カタログ用の REST API のバージョン:2015-10-01-preview**
* **ジョブ用の REST API のバージョン:2016-03-20-preview**

## <a name="features"></a>[機能]
* アカウント管理: 作成、取得、一覧表示、更新、および削除。
* ジョブ管理: 送信、取得、一覧表示、および取り消し。
* カタログ管理: 取得および一覧表示。

## <a name="how-to-install"></a>インストール方法
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Azure Active Directory を使用して認証する
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Data Lake Analytics クライアントを作成する
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var accountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>ジョブの一覧を取得する
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Data Lake Analytics カタログ内のデータベースの一覧を取得する
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>参照
* [Microsoft Azure SDK for Node.js](https://github.com/azure/azure-sdk-for-node)
