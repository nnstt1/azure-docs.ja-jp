---
title: Azure Database for MariaDB での証明書のローテーション
description: Azure Database for MariaDB に影響するルート証明書の今後の変更について説明します
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 8a8a7fec5b5c5f62fb44f6c0ccf6859b1141295b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646513"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mariadb"></a>Azure Database for MariaDB のルート CA の変更について

Azure Database for MariaDB により、標準のメンテナンスとセキュリティのベスト プラクティスの一環として、**2021 年 2 月 15 日 (2021/02/15)** にルート証明書の変更が正常に完了しました。 この記事では、変更、影響を受けるリソース、アプリケーションでデータベース サーバーへの接続を確実に維持するために必要な手順について、詳しく説明します。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*スレーブ*" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。
>

## <a name="why-root-certificate-update-is-required"></a>ルート証明書の更新が必要な理由

Azure Database for MariaDB ユーザーが Azure Database for MariaDB サーバーに接続するには、[こちら](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)にある定義済みの証明書のみを使用できます。 ただし、[証明機関 (CA) ブラウザー フォーラム](https://cabforum.org/)から最近、CA ベンダーによって発行された複数の証明書が準拠していないことを示すレポートが公開されました。

業界のコンプライアンス要件に従い、CA ベンダーは準拠していない CA の CA 証明書の取り消しを始めており、サーバーでは、準拠している CA で発行され、それらの準拠している CA からの CA 証明書によって署名された証明書が使用される必要があります。 Azure Database for MariaDB にはこれらの準拠していない証明書の 1 つが使用されていたため、MySQL サーバーへの潜在的な脅威を最小限に抑えるために、証明書を準拠バージョンにローテーションする必要がありました。

新しい証明書は 2021 年 2 月 15 日 (2021/02/15) からロールアウトされ、有効になっています。 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>2021 年 2 月 15 日 (2021/02/15) にどのような変更が行われましたか?

2021 年 2 月 15 日、既存のお客様が何も変更する必要がなく、サーバーへの接続に影響が生じないように、[BaltimoreCyberTrustRoot ルート証明書](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)が同じ [BaltimoreCyberTrustRoot ルート証明書](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)の **準拠バージョン** に置き換えられました。 この変更時に、[BaltimoreCyberTrustRoot ルート証明書](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)は [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) に **置き換えられませんでした**。この変更は、お客様が変更を行う時間を確保できるように、延期されました。

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>接続を維持するためにクライアントに変更を加える必要はありますか?

クライアント側での変更は必要ありません。 以下に示す以前の推奨事項に従った場合でも、結合された CA 証明書から **BaltimoreCyberTrustRoot 証明書が削除されない限り**、接続を継続できます。 **接続を維持するための通知があるまで、結合された CA 証明書から BaltimoreCyberTrustRoot を削除しないことをお勧めします。**

### <a name="previous-recommendation"></a>以前の推奨事項

- 以下のリンクから、**BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の CA をダウンロードします。

  - [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  - [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

- **BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の両方の証明書が含まれる、結合された CA 証明書ストアを生成します。

  - Java (MariaDB Connector/J) ユーザーの場合、以下を実行します。

    ```console
    keytool -importcert -alias MariaDBServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MariaDBServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
    ```

    次に、元のキーストア ファイルを、新しく生成されたものに置き換えます。

    - `System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");`
    - `System.setProperty("javax.net.ssl.trustStorePassword","password");`

  - .NET (MariaDB Connector/NET、MariaDBConnector) ユーザーの場合は、**BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の両方が Windows 証明書ストアの信頼されたルート証明機関に存在することを確認します。 いずれかの証明書が存在しない場合は、不足している証明書をインポートします。

    [![Azure Database for MariaDB .net 証明書](media/overview/netconnecter-cert.png)](media/overview/netconnecter-cert.png#lightbox)

  - SSL_CERT_DIR を使用する Linux 上の .NET ユーザーの場合は、**BaltimoreCyberTrustRoot** と **DigiCertGlobalRootG2** の両方が SSL_CERT_DIR によって示されるディレクトリに存在することを確認します。 いずれかの証明書が存在しない場合は、不足している証明書ファイルを作成します。

  - その他の (MariaDB Client、MariaDB Workbench、C、C++、Go、Python、Ruby、PHP、NodeJS、Perl、Swift) ユーザーの場合は、次の形式のような 2 つの CA 証明書ファイルをマージできます

   ```
   -----BEGIN CERTIFICATE-----
   (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
   -----END CERTIFICATE-----
   -----BEGIN CERTIFICATE-----
   (Root CA2: DigiCertGlobalRootG2.crt.pem)
   -----END CERTIFICATE-----
   ```

- 元のルート CA pem ファイルを、結合されたルート CA ファイルに置き換えて、アプリケーションやクライアントを再起動します。
- 将来的に、サーバー側に新しい証明書がデプロイされた後は、CA pem ファイルを DigiCertGlobalRootG2.crt.pem に変更することができます。

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>2021 年 2 月 15 日のこの変更時に、BaltimoreCyberTrustRoot 証明書が DigiCertGlobalRootG2 に置き換えられなかったのはなぜですか?

この変更に対するお客様の準備状況を評価したところ、多くのお客様が、この変更の管理に追加のリード タイムを必要としていることがわかりました。 準備のためにお客様により多くのリード タイムを提供するという目的から、お客様とエンド ユーザーが十分なリード タイムを確保できるように証明書の変更を DigiCertGlobalRootG2 に少なくとも 1 年間延期することを決定しました。 

ユーザーへの推奨事項は、前述の手順を使用して結合証明書を作成し、サーバーに接続しますが、削除するように Microsoft からメッセージが送信されるまで BaltimoreCyberTrustRoot 証明書を削除しないことです。 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>BaltimoreCyberTrustRoot 証明書を削除した場合はどうなりますか?

Azure Database for MariaDB サーバーへの接続時に、接続エラーが発生し始めます。 接続を維持するには、[BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 証明書を使用してもう一度 [SSL を構成する](howto-configure-ssl.md)必要があります。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1.SSL/TLS を使用していない場合でも、ルート CA を更新する必要がありますか?

SSL/TLS を使用していない場合は、何も行う必要は必要ありません。

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2.SSL/TLS を使用している場合、ルート CA を更新するには、データベース サーバーを再起動する必要がありますか?

いいえ。新しい証明書を使い始めるために、データベース サーバーを再起動する必要はありません。 証明書の更新はクライアント側の変更であり、確実にデータベース サーバーに接続できるよう、受信クライアント接続で新しい証明書を使用する必要があります。

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3.ルート証明書の検証で SSL/TLS を使用しているかどうかはどうすればわかりますか?

お使いの接続で、接続文字列を確認するとルート証明書が検証されているかどうかを、確認することができます。

- 接続文字列に `sslmode=verify-ca` または `sslmode=verify-identity` が含まれている場合は、証明書を更新する必要があります。
- 接続文字列に `sslmode=disable`、`sslmode=allow`、`sslmode=prefer`、または `sslmode=require` が含まれている場合は、証明書を更新する必要はありません。
- 接続文字列で sslmode が指定されていない場合は、証明書を更新する必要はありません。

接続文字列が抽象化されるクライアントを使用している場合、証明書が検証されているかどうかを確認するには、クライアントのドキュメントを参照してください。

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mariadb"></a>4.Azure Database for MariaDB で App Service を使用している場合、どのような影響がありますか?

Azure Database for MariaDB へ接続している Azure App Service では、次の 2 つのシナリオが考えられ、それはアプリケーションで SSL をどのように使用しているかによって異なります。

- この新しい証明書は、プラットフォーム レベルで App Service に追加されています。 App Service プラットフォームに含まれる SSL 証明書をアプリケーションで使用している場合は、何もする必要はありません。 これは最も一般的なシナリオです。 
- SSL 証明書ファイルへのパスをコードに明示的に含めている場合は、新しい証明書をダウンロードし、新しい証明書を使用するようにコードを更新する必要があります。このシナリオの良い例は、[App Service ドキュメント](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)で共有されているように、App Service でカスタム コンテナーを使用する場合です。 このシナリオは一般的ではありませんが、使用しているユーザーもいます。

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mariadb"></a>5.Azure Database for MariaDB で Azure Kubernetes Services (AKS) を使用している場合、どのような影響がありますか?

Azure Kubernetes Services (AKS) を使用して Azure Database for MariaDB に接続しようとしている場合は、専用の顧客ホスト環境からのアクセスに似ています。 [こちら](../aks/ingress-own-tls.md)の手順を参照してください。

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mariadb"></a>6.Azure Data Factory を使用して Azure Database for MariaDB に接続している場合、どのような影響がありますか?

Azure Integration Runtime を使用しているコネクタでは、Azure でホストされる環境内の Windows 証明書ストアの証明書が利用されています。 これらの証明書は、新しく適用される証明書と既に互換性があるため、何もする必要はありません。

接続文字列に SSL 証明書ファイルへのパスを明示的に含めるセルフホステッド統合ランタイムを使用しているコネクタでは、[新しい証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)をダウンロードし、それを使用するように接続文字列を更新する必要があります。

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7.この変更では、データベース サーバーのメンテナンス ダウンタイムを計画する必要がありますか?

いいえ。 ここでの変更は、データベース サーバーに接続するためにクライアント側でのみ行うものなので、この変更のためにデータベース サーバーでメンテナンス ダウンタイムは必要ありません。

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8.2021 年 2 月 15 日 (2021/02/15) より後に新しいサーバーを作成する場合、影響はありますか?

2021 年 2 月 15 日 (2021/02/15) より後に作成されたサーバーの場合、SSL を使用して接続するアプリケーションには引き続き [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) が使用されます。

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9.Microsoft はどのくらいの頻度で証明書を更新しますか、または有効期限ポリシーはどのようなものですか?

Azure Database for MariaDB によって使用されるこれらの証明書は、信頼された証明機関 (CA) によって提供されます。 そのため、これらの証明書のサポートは、CA によるこれらの証明書のサポートに関連付けられています。 [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 証明書は 2025 年に有効期限が切れる予定であるため、Microsoft は有効期限が切れる前に証明書の変更を実行する必要があります。 また、これらの事前定義された証明書に予期しないバグがある場合、Microsoft は、サービスが常に安全で準拠していることを確保するために、2021 年 2 月 15 日に実行された変更と同様に、できるだけ早く証明書のローテーションを行う必要があります。

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10。読み取りレプリカを使用している場合、この更新を行う必要があるのはソース サーバーだけですか? または、読み取りレプリカでも必要ですか?

この更新はクライアント側の変更であるため、レプリカ サーバーからデータを読み取るためにクライアントを使用している場合は、それらのクライアントにも変更を適用する必要があります。

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11.データイン レプリケーションを使用している場合は、何かアクションを実行する必要がありますか?

- 仮想マシン (オンプレミスまたは Azure 仮想マシン) から Azure Database for MySQL へのデータ レプリケーションの場合は、レプリカを作成するために SSL が使用されているかどうかを確認する必要があります。 **SHOW SLAVE STATUS** を実行し、次の設定を確認します。

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

[データイン レプリケーション](concepts-data-in-replication.md)を使用して Azure Database for MySQL に接続している場合は、次の 2 つの点を考慮する必要があります。

- 仮想マシン (オンプレミスまたは Azure 仮想マシン) から Azure Database for MySQL へのデータ レプリケーションの場合は、レプリカを作成するために SSL が使用されているかどうかを確認する必要があります。 **SHOW SLAVE STATUS** を実行し、次の設定を確認します。 

  ```azurecli-interactive
  Master_SSL_Allowed            : Yes
  Master_SSL_CA_File            : ~\azure_mysqlservice.pem
  Master_SSL_CA_Path            :
  Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
  Master_SSL_Cipher             :
  Master_SSL_Key                : ~\azure_mysqlclient_key.pem
  ```

  CA_file、SSL_Cert、SSL_Key に対して証明書が提供されていることがわかった場合は、[新しい証明書](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)を追加してファイルを更新し、結合された証明書ファイルを作成する必要があります。

- 2 つの Azure Database for MySQL 間のデータ レプリケーションの場合は、**CALL mysql.az_replication_change_master** を実行し、最後のパラメーター [master_ssl_ca](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) として新しいデュアル ルート証明書を指定することによってレプリカをリセットする必要があります。

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12.SSL が使用されているかどうかを確認するためのサーバー側クエリはありますか?

サーバーへの接続に SSL 接続を使用しているかどうかを確認するには、[SSL の検証](howto-configure-ssl.md#verify-the-ssl-connection)に関する記事を参照してください。

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13.証明書ファイルに DigiCertGlobalRootG2 が既に含まれている場合、必要なアクションはありますか?

いいえ。 証明書ファイルに **DigiCertGlobalRootG2** が既に含まれている場合、必要なアクションはありません。

### <a name="14-what-if-i-have-further-questions"></a>14. さらに質問がある場合はどうすればよいですか?

質問がある場合は、[Microsoft Q&A](mailto:AzureDatabaseformariadb@service.microsoft.com) でコミュニティの専門家から回答を得ることができます。 サポート プランに加入していて技術的な支援が必要な場合は、[お問い合わせください](mailto:AzureDatabaseformariadb@service.microsoft.com)。
