<properties linkid="manage-linux-howto-service-management-api" urlDisplayName="Service Management API" pageTitle="How to use the service management API for VMs - Azure" metaKeywords="" description="Learn how to use the Azure Service Management API for a Linux virtual machine." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use the Service Management API" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# サービス管理 API の使用方法

## 初期化

NodeJS から Azure IaaS サービス管理 API を呼び出すには、`azure` モジュールを使用します。

    var azure = require('azure');

まず、ServiceManagementService のインスタンスを作成します。API のすべての呼び出しにこのオブジェクトを使用します。サブスクリプション ID、資格情報、その他の接続オプションはこの時点で確立します。複数のサブスクリプションを管理するには、複数の ServiceManagementService を作成します。

    var iaasClient = azure.createServiceManagementService(subscriptionid, auth, options);

-   Subscriptionid は必須の文字列です。アクセス中のアカウントのサブスクリプション ID であることが必要です。
-   Auth は、このアカウントと共に使用される秘密キーと公開証明書を指定する省略可能なオブジェクトです。

    -   keyfile - 秘密キーが保存されている .pem ファイルのファイル パスです。keyvalue を指定した場合は無視されます。
    -   keyvalue - .pem ファイルに保存されている秘密キーの実際の値です。
    -   certfile - 公開証明書が保存されている .pem ファイルのファイル パスです。cervalue を指定した場合は無視されます。
    -   certvalue - .pem ファイルに格納されている公開キーの実際の値です。
    -   これらの値を指定しない場合は、プロセス環境変数値 `CLIENT_AUTH_KEYFILE` と `CLIENT_AUTH_CERTFILE` が読み取られて使用されます。これらの値が設定されていない場合は、priv.pem および pub.pem ファイルの既定の値が試されます。
    -   秘密キーと公開証明書を読み込めない場合は、エラーがスローされます。
-   Options は、クライアントによって使用されるプロパティの制御に使用できる省略可能なオブジェクトです。

    -   host - Azure 管理サーバーのホスト名です。指定しない場合は、既定のホストが使用されます。
    -   apiversion - HTTP ヘッダーで使用されるバージョン文字列です。指定しない場合は、既定のバージョンが使用されます。
    -   serializetype - XML または JSON を指定できます。指定しない場合は、既定のシリアル化が使用されます。

必要に応じて、トンネリング プロキシを使用して HTTPS 要求がプロキシを通過できるようにすることも可能です。IaasClient が作成されると、環境変数 `HTTPS_PROXY` の処理に使用されます。有効な URL が設定された場合、ホスト名とポートが URL の解析により取得され、プロキシを識別する後続の要求に使用されます。

        iaasClient.SetProxyUrl(proxyurl)

SetProxyUrl を呼び出してプロキシのホストとポートを明示的に設定することも可能です。その結果は `HTTPS_PROXY` 環境変数を設定した場合と同じです。この環境設定が上書きされます。

## コールバック

すべての API に callback 引数は必須です。要求の完了は、callback で渡された関数を呼び出すことで通知されます。

    callback(rsp)

-   callback のパラメーターは 1 つであり、応答オブジェクトです。
-   isSuccessful - true または false。
-   statusCode - 応答の HTTP ステータス コードです。
-   response - 解析されて、javascript オブジェクトになる応答です。isSuccessful が true の場合に設定されます。
-   error - isSuccessful が false の場合にエラー情報が格納される javascript オブジェクトです。
-   body - 応答の実際の本文文字列です。
-   headers - 応答の実際の HTTP ヘッダーです。
-   reqopts - Node HTTP 要求のオプションであり、要求を行うために使用されます。

要求の完了という表示は、要求が受け入れられた事実のみを示す可能性があることに注意してください。この場合、**GetOperationStatus** を使用して最終的なステータスを取得します。

## API

**iaasClient.GetOperationStatus(requested, callback)**

-   多くの管理呼び出しは、処理が完了する前に制御を返します。これにより 202 Accepted の値が返され、要求された値が ms-request-id HTPP ヘッダー内に渡されます。要求が完了したことをポーリングするには、この API を使用して、要求した値を渡します。
-   callback は必須です。

**iaasClient.GetOSImage(imagename, callback)**

-   imagename はイメージの必須の名前文字列です。
-   callback は必須です。
-   成功した場合、応答オブジェクトには指定したイメージのプロパティが格納されます。

**iaasClient.ListOSImages(callback)**

-   callback は必須です。
-   成功した場合、応答オブジェクトにはイメージ オブジェクトの配列が格納されます。

**iaasClient.CreateOSImage(imageName, mediaLink, imageOptions, callback)**

-   imagename はイメージの必須の名前文字列です。
-   mediaLink は使用されるメディア リンクの必須の名前文字列です。
-   imageOptions は省略可能なオブジェクトです。格納できるプロパティは次のとおりです。

    -   カテゴリ
    -   Label - 設定しない場合、既定で imageName が設定されます。
    -   場所
    -   RoleSize
-   callback は必須です。(imageOptions を設定しない場合、これを 3 番目のパラメーターとして指定できます)。
   
-   成功した場合、応答オブジェクトには作成されたイメージのプロパティが格納されます。

**iaasClient.ListHostedServices(callback)**

-   callback は必須です。
-   成功した場合、応答オブジェクトにはホステッド サービス オブジェクトの配列が格納されます。

**iaasClient.GetHostedService(serviceName, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   callback は必須です。
-   成功した場合、応答オブジェクトにはホステッド サービスのプロパティが格納されます。

**iaasClient.CreateHostedService(serviceName, serviceOptions, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   serviceOptions は省略可能なオブジェクトです。格納できるプロパティは次のとおりです。
    -   Description - 既定で 'Service host' が設定されます。
    -   Label - 設定しない場合、既定で serviceName が設定されます。
    -   場所 - サービスが作成されるリージョン。
-   callback は必須です。

**iaasClient.GetStorageAccountKeys(serviceName, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   callback は必須です。
-   成功した場合、応答オブジェクトにはストレージ アクセス キーが格納されます。

**iaasClient.GetDeployment(serviceName, deploymentName, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   callback は必須です。
-   成功した場合、応答オブジェクトには指定したデプロイのプロパティが格納されます。

**iaasClient.GetDeploymentBySlot(serviceName, deploymentSlot, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentSlot はスロット (Staged または Production) の必須の名前文字列です。
-   callback は必須です。
-   成功した場合、応答オブジェクトにはスロット内のデプロイのプロパティが格納されます。

**iaasClient.CreateDeployment(serviceName, deploymentName, VMRole, deployOptions, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   VMRole は、デプロイ用に作成されるロールのプロパティが格納される必須のオブジェクトです。
-   deployOptions は省略可能なオブジェクトです。格納できるプロパティは次のとおりです。
    -   DeploymentSlot - 既定で 'Production' が設定されます。
    -   Label - 設定しない場合、既定で deploymentName が設定されます。
    -   UpgradeDomainCount - 既定値はありません。
-   callback は必須です。

**iaasClient.GetRole(serviceName, deploymentName, roleName, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   roleName はロールの必須の名前文字列です。
-   callback は必須です。
-   成功した場合、応答オブジェクトには指定したロールのプロパティが格納されます。

**iaasClient.AddRole(serviceName, deploymentName, VMRole, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   VMRole は、デプロイ用に追加されるロールのプロパティが格納される必須のオブジェクトです。
-   callback は必須です。

**iaasClient.ModifyRole(serviceName, deploymentName, roleName, VMRole, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   roleName はロールの必須の名前文字列です。
-   VMRole は、変更されるロールのプロパティが格納される必須のオブジェクトです。
-   callback は必須です。

**iaasClient.DeleteRole(serviceName, deploymentName, roleName, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   roleName はロールの必須の名前文字列です。
-   callback は必須です。

**iaasClient.AddDataDisk(serviceName, deploymentName, roleName, datadisk, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   roleName はロールの必須の名前文字列です。
-   Datadisk は、データ ディスクの作成方法の指定に使用される必須のオブジェクトです。
-   callback は必須です。

**iaasClient.ModifyDataDisk(serviceName, deploymentName, roleName, LUN, datadisk, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   roleName はロールの必須の名前文字列です。
-   LUN はデータ ディスクを識別する番号です
-   Datadisk は、データ ディスクの変更方法の指定に使用される必須のオブジェクトです。
-   callback は必須です。

**iaasClient.RemoveDataDisk(serviceName, deploymentName, roleName, LUN, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   roleName はロールの必須の名前文字列です。
-   LUN はデータ ディスクを識別する番号です
-   callback は必須です。

**iaasClient.ShutdownRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   roleInstance はロール インスタンスの必須の名前文字列です。
-   callback は必須です。

**iaasClient.RestartRoleInstance(serviceName, deploymentName, roleInstance, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   roleInstance はロール インスタンスの必須の名前文字列です。
-   callback は必須です。

**iaasClient.CaptureRoleInstance(serviceName, deploymentName, roleInstance, captOptions, callback)**

-   serviceName はホステッド サービスの必須の名前文字列です。
-   deploymentName はデプロイの必須の名前文字列です。
-   roleInstance はロール インスタンスの必須の名前文字列です。
-   captOptions は必須のオブジェクトであり、キャプチャ アクションを定義します。
    -   PostCaptureActions
    -   ProvisioningConfiguration
    -   SupportsStatelessDeployment
    -   TargetImageLabel
    -   TargetImageName
-   callback は必須です。

## データ オブジェクト

API はデプロイ、ロール、またはディスクの作成や変更をするときに、入力としてオブジェクトを受け取ります。Get または List の処理で同様のオブジェクトを返す API もあります。
このセクションでは、オブジェクトのプロパティについて概説します。
デプロイ

-   Name - 文字列
-   DeploymentSlot - 'Staging' または 'Production'
-   Status - 文字列 - 出力のみ
-   PrivateID - 文字列 - 出力のみ
-   Label - 文字列
-   UpgradeDomainCount - 数字
-   SdkVersion - 文字列
-   Locked - true または false
-   RollbackAllowed - true または false
-   RoleInstance - オブジェクト - 出力のみ
-   Role - VMRole オブジェクト
-   InputEndpointList - InputEndpoint の配列

**VMRole**

-   RoleName - 文字列。作成するときは必須です。
-   RoleSize - 文字列。作成するときは省略可能です。
-   RoleType - 文字列。作成するときに指定しない場合、既定で 'PersistentVMRole' に設定されます。
-   OSDisk - オブジェクト。作成するときは必須です。
-   DataDisks - オブジェクトの配列。作成するときは省略可能です。
-   ConfigurationSets - 構成オブジェクトの配列。

**RoleInstance**

-   RoleName - 文字列
-   InstanceName - 文字列
-   InstanceStatus - 文字列
-   InstanceUpgradeDomain - 数字
-   InstanceFaultDomain - 数字
-   InstanceSize - 文字列
-   IpAddress - 文字列

**OSDisk**

-   SourceImageName - 文字列。作成するときは必須です。
-   DisableWriteCache - true または false。
-   DiskName - 文字列。
-   MediaLink - 文字列

**DataDisk**

-   DisableReadCache - true または false
-   EnableWriteCache - true または false
-   DiskName - 文字列
-   MediaLink - 文字列
-   LUN - 数字 (0 ～ 15)
-   LogicalDiskSizeInGB - 数字
-   SourceMediaLink - 文字列
-   作成するときにディスクを指定する方法として、名前、メディア、またはサイズを使用できます。その指定方法によって処理が決まります。詳細については、RDFE API のドキュメントを参照してください。

**ProvisioningConfiguration**

-   ConfigurationSetType - 'ProvisioningConfiguration'
-   AdminPassword - 文字列
-   MachineName - 文字列
-   ResetPasswordOnFirstLogon - true または false

**NetworkConfiguration**

-   ConfigurationSetType - 'NetworkConfiguration'
-   InputEndpoints - ExternalEndpoints の配列

**InputEndpoint**

-   RoleName
-   Vip
-   Port

**ExternalEndpoint**

-   LocalPort
-   名前
-   Port
-   プロトコル

## コード サンプル

ここでは、ホステッド サービスとデプロイを作成し、デプロイの完了ステータスをポーリングするサンプル JavaScript コードを示します。

    var azure = require('azure');

    // specify where certificate files are located
    var auth = {
      keyfile : '../certs/priv.pem',
      certfile : '../certs/pub.pem'
    }

    // names and IDs for subscription, service, deployment
    var subscriptionId = '167a0c69-cb6f-4522-ba3e-d3bdc9c504e1';
    var serviceName = 'sampleService2';
    var deploymentName = 'sampleDeployment';

    // poll for completion every 10 seconds
    var pollPeriod = 10000;

    // data used to define deployment and role

    var deploymentOptions = {
      DeploymentSlot: 'Staging',
      Label: 'Deployment Label'
    }

    var osDisk = {
      SourceImageName : 'Win2K8SP1.110809-2000.201108-01.en.us.30GB.vhd',
    };

    var dataDisk1 = {
      LogicalDiskSizeInGB : 10,
      LUN : '0'
    };

    var provisioningConfigurationSet = {
      ConfigurationSetType: 'ProvisioningConfiguration',
      AdminPassword: 'myAdminPwd1',
      MachineName: 'sampleMach1',
      ResetPasswordOnFirstLogon: false
    };

    var externalEndpoint1 = {
      Name: 'endpname1',
      Protocol: 'tcp',
      Port: '59919',
      LocalPort: '3395'
    };

    var networkConfigurationSet = {
      ConfigurationSetType: 'NetworkConfiguration',
      InputEndpoints: [externalEndpoint1]
    };

    var VMRole = {
      RoleName: 'sampleRole',
      RoleSize: 'Small',
      OSDisk: osDisk,
      DataDisks: [dataDisk1],
      ConfigurationSets: [provisioningConfigurationSet, networkConfigurationSet]
    }


    // function to show error messages if failed
    function showErrorResponse(rsp) {
      console.log('There was an error response from the service');
      console.log('status code=' + rsp.statusCode);
      console.log('Error Code=' + rsp.error.Code);
      console.log('Error Message=' + rsp.error.Message);
    }

    // polling for completion
    function PollComplete(reqid) {
      iaasCli.GetOperationStatus(reqid, function(rspobj) {
        if (rspobj.isSuccessful && rspobj.response) {
          var rsp = rspobj.response;
          if (rsp.Status == 'InProgress') {
            setTimeout(PollComplete(reqid), pollPeriod);
            process.stdout.write('.');
          } else {
            console.log(rsp.Status);
            if (rsp.HttpStatusCode) console.log('HTTP Status: ' + rsp.HttpStatusCode);
            if (rsp.Error) {      
              console.log('Error code: ' + rsp.Error.Code);
              console.log('Error Message: ' + rsp.Error.Message);
            }
          }
        } else {
          showErrorResponse(rspobj);
        }
      });
    }


    // create the client object
    var iaasCli = azure.createServiceManagementService(subscriptionId, auth);

    // create a hosted service.
    // if successful, create deployment
    // if accepted, poll for completion
    iaasCli.CreateHostedService(serviceName, function(rspobj) {
      if (rspobj.isSuccessful) {
        iaasCli.CreateDeployment(serviceName, 
                                 deploymentName,
                                 VMRole, deploymentOptions,
                                  function(rspobj) {
          if (rspobj.isSuccessful) {
          // get request id, and start polling for completion
            var reqid = rspobj.headers['x-ms-request-id'];
            process.stdout.write('Polling');
            setTimeout(PollComplete(reqid), pollPeriod);
          } else {
            showErrorResponse(rspobj);
          }
        });
      } else {
        showErrorResponse(rspobj);
      }
    });
