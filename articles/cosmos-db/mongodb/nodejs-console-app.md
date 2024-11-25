---
title: Azure Cosmos DB の MongoDB 用 API を使用した Node.js アプリの構築
description: Azure Cosmos DB の MongoDB 用 API を使用してオンライン データベースを作成するチュートリアル。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 08/26/2021
author: gahl-levy
ms.author: gahllevy
ms.custom: devx-track-js
ms.openlocfilehash: 9edcb24280db6c3fb102b9d1c28d7b2a0e537294
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123031620"
---
# <a name="build-an-app-using-nodejs-and-azure-cosmos-dbs-api-for-mongodb"></a>Node.js および Azure Cosmos DB の MongoDB 用 API を使用してアプリを構築する 
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](../sql-api-get-started.md)
> * [.NET Core](../sql-api-get-started.md)
> * [Java](../create-sql-api-java.md)
> * [MongoDB 用 Node.js](nodejs-console-app.md)
> * [Node.js](../sql-api-nodejs-get-started.md)
>

次の例は、Node.js および Azure Cosmos DB の MongoDB 用 API を使用してコンソール アプリを構築する方法を示しています。

この例を使用するための前提条件は、以下のとおりです。

* Azure Cosmos DB の MongoDB 用 API を使用するように構成された Cosmos アカウントを[作成](create-mongodb-dotnet.md#create-account)する。
* [接続文字列](connect-mongodb-account.md)の情報を取得する。

## <a name="create-the-app"></a>アプリを作成する

1. *app.js* ファイルを作成し、次のコードをコピーして貼り付けます。

    ```javascript
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **省略可能**: **MongoDB Node.js 2.2 ドライバー** を使用する場合、次のコード スニペットを置き換えてください。

    元のコード:

    ```javascript
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    以下のコードに置き換える必要があります。

    ```javascript
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. アカウント設定に従って、*app.js* ファイル内の次の変数を変更します (接続文字列を確認する方法は [こちら](connect-mongodb-account.md)を参照)。

    > [!IMPORTANT]
    > **MongoDB Node.js 3.0 ドライバー** では、Cosmos DB パスワード内の特殊文字をエンコードする必要があります。 '=' 文字を必ず %3D としてエンコードします。
    >
    > 例: パスワード *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* は、*jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D* にエンコードされます。
    >
    > **MongoDB Node.js 2.2 ドライバー** では、Cosmos DB パスワード内の特殊文字をエンコードする必要はありません。
    >
    >
   
    ```javascript
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. お好きなターミナルを開き、**npm install mongodb --save** を実行してから、**node app.js** でアプリを実行します。

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](connect-using-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](connect-using-robomongo.md)方法を学びます。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](nodejs-console-app.md)を調査します。
- Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
    - 既存のデータベース クラスターの仮想コアとサーバーの数のみを把握している場合は、[仮想コア数または vCPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
    - 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください