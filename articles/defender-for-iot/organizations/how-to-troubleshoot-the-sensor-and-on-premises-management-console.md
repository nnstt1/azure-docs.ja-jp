---
title: センサーとオンプレミスの管理コンソールのトラブルシューティング
description: センサーとオンプレミスの管理コンソールをトラブルシューティングして、発生している可能性のある問題を排除します。
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 66e4d9b221176bb8a1413e679656c6df401459dd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278647"
---
# <a name="troubleshoot-the-sensor-and-on-premises-management-console"></a>センサーとオンプレミスの管理コンソールのトラブルシューティング

この記事では、センサーとオンプレミスの管理コンソールの基本的なトラブルシューティング ツールについて説明します。 ここで説明する項目に加えて、次の方法でシステムの正常性を確認できます。

**アラート**:トラフィックを監視するセンサー インターフェイスがダウンしたときにアラートが作成されます。

**SNMP**:センサーの正常性は SNMP によって監視されます。 Microsoft Defender for IoT は、認可済みの監視サーバーから送信された SNMP クエリに応答します。

**システム通知**:管理コンソールでセンサーを制御すると、失敗したセンサー バックアップと切断されたセンサーに関するアラートを転送できます。

## <a name="sensor-troubleshooting-tools"></a>センサーのトラブルシューティング ツール

### <a name="investigate-password-failure-at-initial-sign-in"></a>初回サインイン時にパスワード エラーを調査する

構成済みの Arrow センサーに初めてサインインするときは、パスワードの復元を実行する必要があります。

**パスワードを復元するには、次のようにします**。

1. Defender for IoT のサインイン画面で、**[パスワードの復元]** を選択します。 **[パスワードの復元]** 画面が開きます。

1. **[CyberX]** または **[サポート]** を選択し、一意識別子をコピーします。

1. Azure portal に移動し、 **[Sites and Sensors]\(サイトとセンサー\)** を選択します。  

1. **[その他の操作]** ドロップダウン メニューを選択し、 **[オンプレミス管理コンソールのパスワードを復元]** を選択します。

    :::image type="content" source="media/how-to-create-and-manage-users/recover-password.png" alt-text="オンプレミス管理コンソールのパスワードを復元するオプションのスクリーンショット。":::

1. **[パスワードの復元]** 画面で受信した一意識別子を入力し、 **[復元]** を選択します。 `password_recovery.zip` ファイルがダウンロードされます。

    :::image type="content" source="media/how-to-create-and-manage-users/enter-identifier.png" alt-text="一意識別子を入力し、復元を選択するスクリーンショット。":::

    > [!NOTE]
    > パスワードの復元ファイルは変更しないでください。 これは署名されたファイルであり、改ざんすると機能しません。

1. **[パスワードの復元]** 画面で **[アップロード]** を選択します。 **[Upload Password Recovery File]\(パスワード復元ファイルのアップロード\)** ウィンドウが開きます。

1. **[参照]** を選択して `password_recovery.zip` ファイルを見つけるか、`password_recovery.zip` をウィンドウにドラッグします。

1. **[次へ]** を選択すると、管理コンソールのユーザーとシステムが生成したパスワードが表示されます。

    > [!NOTE]
    > センサーまたはオンプレミスの管理コンソールに初めてサインインすると、接続先のサブスクリプションにリンクされます。 CyberX またはサポート ユーザーのパスワードをリセットする必要がある場合は、そのサブスクリプションを選択する必要があります。 CyberX またはサポート ユーザーのパスワードを復元する方法の詳細については、「[オンプレミスの管理コンソールまたはセンサーのパスワードをリセットする](how-to-create-and-manage-users.md#recover-the-password-for-the-on-premises-management-console-or-the-sensor)」を参照してください。

### <a name="investigate-a-lack-of-traffic"></a>トラフィックの欠落を調査する

構成されたポートのいずれかにトラフィックがないことがセンサーで認識されると、コンソールの上部にインジケーターが表示されます。 このインジケーターは、すべてのユーザーに表示されます。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/no-traffic-detected.png" alt-text="トラフィックが検出されなかったことを示すアラートのスクリーンショット。":::

このメッセージが表示されたら、トラフィックのない場所を調べることができます。 スパン ケーブルが接続されていて、スパン アーキテクチャに変更がないことを確認します。  

サポートとトラブルシューティングの情報については、[Microsoft サポート](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)にお問い合わせください。

### <a name="check-system-performance"></a>システムのパフォーマンスを確認します。

新しいセンサーがデプロイされた場合や、たとえばセンサーの動作が遅かったりアラートが表示されかったりする場合は、システムのパフォーマンスを確認できます。

**システムのパフォーマンスを確認するには、以下の手順を実行します**。

1. ダッシュボードで、`PPS > 0` であることを確認します。

   :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/dashboard-view-v2.png" alt-text="サンプル ダッシュボードのスクリーンショット。":::

1. サイド メニューから、 **[デバイス]** を選択します。

1. **[デバイス]** ウィンドウで、デバイスが検出されていることを確認します。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/discovered-devices.png" alt-text="検出されたデバイスのスクリーンショット。":::

1. サイド メニューから、 **[データ マイニング]** を選択します。

1. **[データ マイニング]** ウィンドウで、 **[すべて]** を選択し、レポートを生成します。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="データ マイニングを使用して新しいレポートを生成する画面のスクリーンショット。":::

1. レポートにデータが含まれていることを確認します。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/new-report-generated.png" alt-text="レポートにデータが含まれていることを確認する画面のスクリーンショット。":::

1. サイド メニューから、 **[傾向と統計]** を選択します。

1. **[傾向と統計]** ウィンドウで、 **[ウィジェットの追加]** を選択します。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/add-widget.png" alt-text="ウィジェットを選択して追加しているスクリーンショット。":::

1. ウィジェットを追加し、データが表示されていることを確認します。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/widget-data.png" alt-text="データを示すウィジェットのスクリーンショット。":::

1. サイド メニューから、 **[アラート]** を選択します。 **[アラート]** ウィンドウが表示されます。

1. アラートが作成されたことを確認します。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alerts-created.png" alt-text="アラートが作成されたことを示すスクリーンショット。":::

### <a name="investigate-a-lack-of-expected-alerts-on-the-sensor"></a>センサーを使用して予想されるアラートの不足を調査する

**[アラート]** ウィンドウに予想されるアラートが表示されない場合は、次の点を確認します。

- 別のセキュリティ インスタンスへの反応として同じアラートが既に **[アラート]** ウィンドウに表示されているかどうかを確認します。 該当し、このアラートがまだ処理されていない場合は、センサー コンソールに新しいアラートが表示されません。

- 管理コンソールで **[アラートの除外]** ルールを使用してこのアラートを除外していなかったことを確認します。

### <a name="investigate-widgets-that-show-no-data"></a>データが表示されないウィジェットを調査する

**[傾向と統計]** ウィンドウのウィジェットにデータが表示されない場合は、次を実行します。

- [システムのパフォーマンスを確認します](#check-system-performance)。

- 時刻とリージョンの設定が正しく構成されていて、未来の時刻に設定されていないことを確認します。

### <a name="investigate-a-device-map-that-shows-only-broadcasting-devices"></a>ブロードキャスト デバイスのみを表示するデバイス マップを調査する

マップに表示されているデバイスが相互に接続されていない場合は、SPAN ポート構成で問題が発生している可能性があります。 つまり、ブロードキャスト デバイスのみが表示されていて、ユニキャスト トラフィックがない可能性があります。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/broadcasting-devices.png" alt-text="ブロードキャスト デバイスのスクリーンショット。":::

このような場合、ブロードキャスト トラフィックだけが表示されていることを確認し、ユニキャスト トラフィックも表示されるよう、SPAN ポート構成の修正をネットワーク エンジニアに依頼してください。

ブロードキャスト トラフィックのみが表示されていることを確認するには:

- **[データ マイニング]** 画面で、 **[すべて]** オプションを使用してレポートを作成します。 次に、ブロードキャストとマルチキャストのトラフィックのみがレポートに表示される (およびユニキャスト トラフィックがない) かどうかを確認します。

または:

- スイッチから直接 PCAP を記録するか、Wireshark を使用してノート PC を接続します。

### <a name="connect-the-sensor-to-ntp"></a>センサーを NTP に接続する

スタンドアロン センサーと、センサーが関連付けられている管理コンソールを NTP に接続するように構成できます。

スタンドアロン センサーを NTP に接続するには:

- [サポート チームに支援を求めます](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)。

管理コンソールで制御されるセンサーを NTP に接続するには:

- NTP への接続は、管理コンソールで構成されます。 管理コンソールで制御するすべてのセンサーには、NTP 接続が自動的に取得されます。

### <a name="investigate-when-devices-arent-shown-on-the-map-or-you-have-multiple-internet-related-alerts"></a>デバイスがマップに表示されない場合、またはインターネットに関連する複数のアラートがある場合に調査する

ICS デバイスは、外部 IP アドレスを使用して構成される場合があります。 このような ICS デバイスはマップに表示されません。 デバイスではなく、インターネット クラウドがマップに表示されます。 これらのデバイスの IP アドレスは、クラウド画像に含まれます。

また、複数のインターネット関連のアラートが表示される場合も同じ問題が発生します。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/alert-problems.png" alt-text="複数のインターネット関連のスクリーンショット。":::

**構成を修正するには、以下の手順を実行します**。

1. デバイス マップのクラウド アイコンを右クリックし、 **[IP アドレスのエクスポート]** を選択します。 プライベートであるパブリック範囲をコピーし、サブネットの一覧に追加します。 詳細については、「[サブネットを構成する](how-to-control-what-traffic-is-monitored.md#configure-subnets)」を参照してください。

1. インターネット接続の新しいデータマイニング レポートを生成します。

1. データマイニング レポートで、:::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/administrator-mode.png" border="false"::: を選択して管理者モードに移行し、ICS デバイスの IP アドレスを削除します。

### <a name="tweak-the-sensors-quality-of-service-qos"></a>センサーのサービス品質 (QoS) を調整する

ネットワーク リソースを節約するために、センサーが日常的な手順に使用するインターフェイス帯域幅を制限できます。

インターフェイス帯域幅を制限するには、sudo アクセス許可で実行する必要がある `cyberx-xsense-limit-interface` CLI ツールを使用します。 このツールは、次の引数を受け取ります。

- `* -i`: インターフェイス (例: eth0)。

- `* -l`: 制限 (例:30 kbit/1 mbit)。 次の帯域幅の単位を使用できます: kbps、mbps、kbit、mbit、または bps。

- `* -c`: クリア (インターフェイス帯域幅の制限をクリアする場合)。

**サービスの品質 (QoS) を調整するには、以下の手順を実行します**。

1. Defender for IoT ユーザーとしてセンサー CLI にサインインし、「`sudo cyberx-xsense-limit-interface-I eth0 -l value`」と入力します。

   例: `sudo cyberx-xsense-limit-interface -i eth0 -l 30kbit`

   > [!NOTE]
   > 物理アプライアンスの場合は、em1 インターフェイスを使用します。

1. インターフェイス制限をクリアするには、「`sudo cyberx-xsense-limit-interface -i eth0 -l 1mbps -c`」と入力します。

## <a name="on-premises-management-console-troubleshooting-tools"></a>オンプレミスの管理コンソールのトラブルシューティング ツール

### <a name="investigate-a-lack-of-expected-alerts-on-the-management-console"></a>管理コンソールを使用して予想されるアラートの不足を調査する

予想されるアラートが **[アラート]** ウィンドウに表示されない場合は、次の点を確認します。

- 別のセキュリティ インスタンスへの反応として同じアラートが既に **[アラート]** ウィンドウに表示されているかどうかを確認します。 該当し、このアラートがまだ処理されていない場合は、新しいアラートが表示されません。

- オンプレミスの管理コンソールで **[アラートの除外]** ルールを使用してこのアラートを除外していなかったことを確認します。  

### <a name="tweak-the-quality-of-service-qos"></a>サービスの品質 (QoS) を調整する

ネットワーク リソースを節約するため、アプライアンスとオンプレミスの管理コンソールとの間で、1 回の同期操作で外部システム (メールや SIEM など) に送信されるアラートの数を制限できます。

既定値は 50 です。 つまり、アプライアンスとオンプレミスの管理コンソールとの間の 1 回の通信セッションでは、外部システムに対して 50 を超えるアラートは発生しません。 

アラートの数を制限するには、`/var/cyberx/properties/management.properties` で使用できる `notifications.max_number_to_report` プロパティを使用します。 このプロパティを変更した後に再起動は必要ありません。

**サービスの品質 (QoS) を調整するには、以下の手順を実行します**。

1. Defender for IoT ユーザーとしてサインインします。

1. 既定値を確認します。

   ```bash
   grep \"notifications\" /var/cyberx/properties/management.properties
   ```

   次の既定値が表示されます。

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. 既定の設定を編集します。

   ```bash
   sudo nano /var/cyberx/properties/management.properties
   ```

1. 次の行の設定を編集します。

   ```bash
   notifications.max_number_to_report=50
   notifications.max_time_to_report=10 (seconds)
   ```

1. 変更を保存します。 再起動は必要はありません。

## <a name="export-information-from-the-sensor-for-troubleshooting"></a>トラブルシューティングのためにセンサーから情報をエクスポートする

ネットワークを監視および分析するためのツールに加えて、詳細な調査のためにサポート チームに情報を送信することもできます。 ログをエクスポートすると、センサーは、エクスポートされたログに固有のワンタイム パスワード (OTP) を別のテキスト ファイルで自動的に生成します。

**ログをエクスポートするには、以下の手順を実行します**。

1. 左側のペインで、 **[システム設定]** を選択します。

1. **[ログのエクスポート]** を選択します。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/sensor-export-log.png" alt-text="システム サポートへのログのエクスポート画面のスクリーンショット。":::

1. **[ファイル名]** フィールドに、ログのエクスポートに使用するファイル名を入力します。 既定値は現在の日付です。

1. エクスポートするデータを定義するには、データのカテゴリを選択します。  

    | エクスポートのカテゴリ | [説明] |
    |--|--|
    | **オペレーティング システム ログ** | オペレーティング システムの状態に関する情報を取得するには、このオプションを選択します。 |
    | **インストール/アップグレード ログ** | インストールおよびアップグレードの構成パラメーターを調査する場合は、このオプションを選択します。 |
    | **システム サニティ出力** | システムのパフォーマンスを確認するには、このオプションを選択します。 |
    | **解析ログ** | プロトコル解析の詳細検査を許可するには、このオプションを選択します。 |
    | **OS カーネル ダンプ** | カーネル メモリ ダンプをエクスポートするには、このオプションを選択します。 カーネル メモリ ダンプには、このカーネルで発生した問題の時点でカーネルが使用しているすべてのメモリが含まれています。 ダンプ ファイルのサイズは、完全なメモリ ダンプよりも小さくなります。 通常、ダンプ ファイルは、システムの物理メモリのサイズの約 3 分の 1 になります。 |
    | **転送ログ** | 転送ルールを調査する場合は、このオプションを選択します。 |
    | **SNMP ログ** | SNMP 正常性チェックの情報を受信するには、このオプションを選択します。 |
    | **コア アプリケーション ログ** | コア アプリケーションの構成と操作に関するデータをエクスポートするには、このオプションを選択します。 |
    | **CM との通信ログ** | 管理コンソールとの接続に継続的な問題または中断がある場合は、このオプションを選択します。 |
    | **Web アプリケーション ログ** | アプリケーションの Web インターフェイスから送信されたすべての要求に関する情報を取得するには、このオプションを選択します。 |
    | **システム バックアップ** | システムの正確な状態を調査するためにすべてのシステム データのバックアップをエクスポートするには、このオプションを選択します。 |
    | **解析の統計情報** | プロトコル統計情報の詳細検査を許可するには、このオプションを選択します。 |
    | **データベース ログ** | システム データベースからログをエクスポートするには、このオプションを選択します。 システム ログを調査することで、システムの問題を特定するのに役立ちます。 |
    | **構成** | すべてが正しく構成されていることを確認するために構成可能なすべてのパラメーターに関する情報をエクスポートするには、このオプションを選択します。 |

1. すべてのオプションを選択するには、 **[カテゴリの選択]** の横にある **[すべて選択]** を選択します。

1. **[ログのエクスポート]** を選択します。

エクスポートされたログは **[アーカイブ済みログ]** 一覧に追加されます。 エクスポートされたログとは別のメッセージとメディアで、OTP をサポート チームに送信します。 サポート チームは、ログの暗号化に使用される一意の OTP を使用した場合のみ、エクスポートされたログを抽出できます。

アーカイブ済みログの一覧には、最大 5 つの項目を含めることができます。 リスト内の項目数がその数を超えると、最も古い項目が削除されます。

## <a name="export-audit-log-from-the-management-console"></a>管理コンソールから監査ログをエクスポートする

監査ログには、発生した時点のキー情報が記録されます。 監査ログは、どのような変更が加えられたか、およびだれが行ったかを調べるときに役立ちます。 監査ログは、管理コンソールでエクスポートでき、次の情報が含まれています。

| アクション | ログに記録された情報 |
|--|--|
| **アラートの詳細と修復** | アラート ID |
| **パスワードの変更** | ユーザー、ユーザー ID |
| **Login** | ユーザー |
| **ユーザーの作成** | ユーザー、ユーザー ロール |
| **パスワードのリセット** | ユーザー名 |
| **除外ルール**: </br></br>- 作成 </br></br>- 編集中 </br></br>- 削除 | </br></br>ルールの概要 </br></br>ルール ID、ルールの概要 </br></br>ルールの ID |
| **管理コンソールのアップグレード** | 使用されるアップグレード ファイル |
| **センサーのアップグレードの再試行** | センサー ID |
| **アップロードされた TI パッケージ** | 追加で記録される情報はありません。 |

**監査ログをエクスポートするには、以下の手順を実行します。**

1. 管理コンソールの左ペインで **[システム設定]** を選択します。

1. **[エクスポート]** を選択します。

1. ファイル名フィールドに、エクスポートしたログに付けるファイル名を入力します。 名前を入力しなかった場合、既定のファイル名は現在の日付になります。

1. **[監査ログ]** を選択します。

1. **[エクスポート]** を選択します。

    :::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/audit-logs-export.png" alt-text="監査ログを選択し、エクスポートを選択してファイルを作成する画面のスクリーンショット。":::

エクスポートされたログは **[アーカイブ済みログ]** 一覧に追加されます。 :::image type="icon" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/eye-icon.png" border="false"::: ボタンを選択して OTP を表示します。 エクスポートされたログとは別のメッセージで、OTP 文字列をサポート チームに送信します。 サポート チームは、ログの暗号化に使用される一意の OTP を使用した場合のみ、エクスポートされたログを抽出できます。

:::image type="content" source="media/how-to-troubleshoot-the-sensor-and-on-premises-management-console/archived-files.png" alt-text="トラブルシューティング情報のエクスポート ウィンドウのアーカイブされたファイル セクションで作成したファイルのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

- [アラートを表示する](how-to-view-alerts.md)

- [SNMP MIB の監視を設定する](how-to-set-up-snmp-mib-monitoring.md)

- [センサー切断イベントについて](how-to-manage-sensors-from-the-on-premises-management-console.md#understand-sensor-disconnection-events)
