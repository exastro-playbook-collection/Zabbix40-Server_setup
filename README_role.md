# Ansible Role: Zabbix40-Server_setup

# Trademarks
-----------
* Linuxは、Linus Torvalds氏の米国およびその他の国における登録商標または商標です。
* RedHat、RHEL、CentOSは、Red Hat, Inc.の米国およびその他の国における登録商標または商標です。
* Windows、PowerShellは、Microsoft Corporation の米国およびその他の国における登録商標または商標です。
* Ansibleは、Red Hat, Inc.の米国およびその他の国における登録商標または商標です。
* pythonは、Python Software Foundationの登録商標または商標です。
* Zabbixは、ラトビア共和国にあるZabbix LLCの商標です。
* Oracleは、Oracle International Corporation の米国およびその他の国における登録商標または商標です。
* NECは、日本電気株式会社の登録商標または商標です。
* その他、本ロールのコード、ファイルに記載されている会社名および製品名は、各社の登録商標または商標です。

## Description
本Ansible Roleは統合監視ソフトウェアである"Zabbix Server"の初期設定を行います。
対象バージョンは以下のバージョンです。

- Zabbix 4.0

以下のファイルを設定します。
  - /etc/my.cnf
  - /etc/zabbix/zabbix_server.conf
  - /etc/zabbix/web/zabbix.conf.php
  - /etc/httpd/conf.d/zabbix.conf
  - /etc/logrotate.d/zabbix-server

以下の設定を行います。
  - データベース操作
    * Zabbix用データベースの作成
    * Zabbix用データベースユーザの作成
    * Zabbix用スキーマの登録

## Supports

- 管理マシン(Ansibleサーバ)
  * Linux系OS（RHEL/CentOS）
  * Ansible バージョン 2.5以上 (動作確認済み：2.5、2.6)
  * Python バージョン 2.6 または2.7

- 管理対象マシン(構築対象マシン)
  * RHEL7

## Requirements

- 管理マシン(Ansibleサーバ)
  * 管理対象マシンとroot権限でSSH通信可能であること。

- 管理対象マシン(インストール対象マシン)
  * Zabbix4.0がインストールされていること
  * SELinuxが無効に設定されていること
  * firewalldが適切に設定されていること

## Role Variables

Role の変数値について説明します。

### Mandatory variables

実行時には、必ず指定しないといけない変数はありません。

### Optional variables

以下の変数は任意で指定します。

- MySQL(MariaDB)設定ファイル「/etc/my.cnf」
  * ''VAR_Zabbix40SV_skip_character_set_client_handshake'': skip-character-set-client-handshake(on|off, default: "on")
    + on を指定すると、項目が設定されます。
    + off を指定すると、項目が設定されません。
  * ''VAR_Zabbix40SV_innodb_file_per_table'': innodb_file_per_table(on|off, default: "on")
    + on を指定すると、項目が設定されます。
    + off を指定すると、項目が設定されません。
  * ''VAR_Zabbix40SV_character_set_server'': character-set-server(string, default: "utf8")
  * ''VAR_Zabbix40SV_innodb_log_buffer_size'': innodb_log_buffer_size(string, default: "16M")
  * ''VAR_Zabbix40SV_innodb_buffer_pool_size'': innodb_buffer_pool_size(string, default: "1024M")
  * ''VAR_Zabbix40SV_innodb_log_file_size'': innodb_log_file_size(string, default: "256M")
  * ''VAR_Zabbix40SV_innodb_log_files_in_group'': innodb_log_files_in_group(string, default: "2")
  * ''VAR_Zabbix40SV_key_buffer_size'': key_buffer_size(string, default: "200M")
  * ''VAR_Zabbix40SV_max_allowed_packet'': max_allowed_packet(string, default: "16M")

- データベース操作
  * ''VAR_Zabbix40SV_DB_Root_Password'': データベースのrootユーザのパスワード(string, default: "")
    + role「Zabbix-Server\_install」を利用してインストールする場合は、rootユーザのパスワードは設定されていないため、明示的に設定する必要はありません。
    + 予めデータベースをインストールしてある環境に対して実行する場合は、設定してください。

- Zabbix設定ファイル「/etc/zabbix/zabbix_server.conf」
  * ''VAR_Zabbix40SV_DBName'': DBName(string, default: "zabbix")
  * ''VAR_Zabbix40SV_DBUser'': DBUser(string, default: "zabbix")
  * ''VAR_Zabbix40SV_DBPassword'': DBPassword(string, default: "zabbixpass")
  * ''VAR_Zabbix40SV_DBSocket'': DBSocket(string, default: "/var/lib/mysql/mysql.sock")
  * ''VAR_Zabbix40SV_LogFile'': LogFile(string, default: "/var/log/zabbix/zabbix_server.log")
  * ''VAR_Zabbix40SV_LogFileSize'': LogFileSize(string, default: "0")
  * ''VAR_Zabbix40SV_PidFile'': PidFile(number, default: "/var/run/zabbix/zabbix_server.pid")
  * ''VAR_Zabbix40SV_SocketDir'': SocketDir(string, default: "/var/run/zabbix")
  * ''VAR_Zabbix40SV_AlertScriptsPath'': AlertScriptsPath(string, default: "/usr/lib/zabbix/alertscripts")
  * ''VAR_Zabbix40SV_ExternalScripts'': ExternalScripts(string, default: "/usr/lib/zabbix/externalscripts")
  * ''VAR_Zabbix40SV_LogSlowQueries'': LogSlowQueries(number, default: "3000")
  * ''VAR_Zabbix40SV_SourceIP'': SourceIP(string, default: なし)
  * ''VAR_Zabbix40SV_LogType'': LogType(string, default: なし)
  * ''VAR_Zabbix40SV_DebugLevel'': DebugLevel(string, default: なし)
  * ''VAR_Zabbix40SV_DBHost'': DBHost(string, default: なし)
  * ''VAR_Zabbix40SV_DBSchema'': DBSchema(string, default: なし)
  * ''VAR_Zabbix40SV_DBPort'': DBPort(string, default: なし)
  * ''VAR_Zabbix40SV_HistoryStorageURL'': HistoryStorageURL(string, default: なし)
  * ''VAR_Zabbix40SV_HistoryStorageTypes'': HistoryStorageTypes(string, default: なし)
  * ''VAR_Zabbix40SV_HistoryStorageDateIndex'': HistoryStorageDateIndex(string, default: なし)
  * ''VAR_Zabbix40SV_ExportDir'': ExportDir(string, default: なし)
  * ''VAR_Zabbix40SV_ExportFileSize'': ExportFileSize(string, default: なし)
  * ''VAR_Zabbix40SV_StartPollers'': StartPollers(string, default: なし)
  * ''VAR_Zabbix40SV_StartIPMIPollers'': StartIPMIPollers(string, default: なし)
  * ''VAR_Zabbix40SV_StartPreprocessors'': StartPreprocessors(number, default: なし)
  * ''VAR_Zabbix40SV_StartPollersUnreachable'': StartPollersUnreachable(string, default: なし)
  * ''VAR_Zabbix40SV_StartTrappers'': StartTrappers(string, default: なし)
  * ''VAR_Zabbix40SV_StartPingers'': StartPingers(string, default: なし)
  * ''VAR_Zabbix40SV_StartDiscoverers'': StartDiscoverers(string, default: なし)
  * ''VAR_Zabbix40SV_StartHTTPPollers'': StartHTTPPollers(string, default: なし)
  * ''VAR_Zabbix40SV_StartTimers'': StartTimers(string, default: なし)
  * ''VAR_Zabbix40SV_StartEscalators'': StartEscalators(string, default: なし)
  * ''VAR_Zabbix40SV_StartAlerters'': StartAlerters(number, default: なし)
  * ''VAR_Zabbix40SV_JavaGateway'': JavaGateway(string, default: なし)
  * ''VAR_Zabbix40SV_JavaGatewayPort'': JavaGatewayPort(string, default: なし)
  * ''VAR_Zabbix40SV_StartJavaPollers'': StartJavaPollers(string, default: なし)
  * ''VAR_Zabbix40SV_StartVMwareCollectors'': StartVMwareCollectors(string, default: なし)
  * ''VAR_Zabbix40SV_VMwareFrequency'': VMwareFrequency(string, default: なし)
  * ''VAR_Zabbix40SV_VMwarePerfFrequency'': VMwarePerfFrequency(string, default: なし)
  * ''VAR_Zabbix40SV_VMwareCacheSize'': VMwareCacheSize(string, default: なし)
  * ''VAR_Zabbix40SV_VMwareTimeout'': VMwareTimeout(string, default: なし)
  * ''VAR_Zabbix40SV_SNMPTrapperFile'': SNMPTrapperFile(string, default: なし)
  * ''VAR_Zabbix40SV_StartSNMPTrapper'': StartSNMPTrapper(string, default: なし)
  * ''VAR_Zabbix40SV_ListenIP'': ListenIP(string, default: なし)
  * ''VAR_Zabbix40SV_HousekeepingFrequency'': HousekeepingFrequency(string, default: なし)
  * ''VAR_Zabbix40SV_MaxHousekeeperDelete'': MaxHousekeeperDelete(string, default: なし)
  * ''VAR_Zabbix40SV_CacheSize'': CacheSize(string, default: なし)
  * ''VAR_Zabbix40SV_CacheUpdateFrequency'': CacheUpdateFrequency(string, default: なし)
  * ''VAR_Zabbix40SV_StartDBSyncers'': StartDBSyncers(string, default: なし)
  * ''VAR_Zabbix40SV_HistoryCacheSize'': HistoryCacheSize(string, default: なし)
  * ''VAR_Zabbix40SV_HistoryIndexCacheSize'': HistoryIndexCacheSize(string, default: なし)
  * ''VAR_Zabbix40SV_TrendCacheSize'': TrendCacheSize(string, default: なし)
  * ''VAR_Zabbix40SV_ValueCacheSize'': ValueCacheSize(string, default: なし)
  * ''VAR_Zabbix40SV_Timeout'': Timeout(string, default: なし)
  * ''VAR_Zabbix40SV_TrapperTimeout'': TrapperTimeout(string, default: なし)
  * ''VAR_Zabbix40SV_UnreachablePeriod'': UnreachablePeriod(string, default: なし)
  * ''VAR_Zabbix40SV_UnavailableDelay'': UnavailableDelay(string, default: なし)
  * ''VAR_Zabbix40SV_UnreachableDelay'': UnreachableDelay(string, default: なし)
  * ''VAR_Zabbix40SV_FpingLocation'': FpingLocation(string, default: なし)
  * ''VAR_Zabbix40SV_Fping6Location'': Fping6Location(string, default: なし)
  * ''VAR_Zabbix40SV_SSHKeyLocation'': SSHKeyLocation(string, default: なし)
  * ''VAR_Zabbix40SV_TmpDir'': TmpDir(string, default: なし)
  * ''VAR_Zabbix40SV_StartProxyPollers'': StartProxyPollers(string, default: なし)
  * ''VAR_Zabbix40SV_ProxyConfigFrequency'': ProxyConfigFrequency(string, default: なし)
  * ''VAR_Zabbix40SV_ProxyDataFrequency'': ProxyDataFrequency(string, default: なし)
  * ''VAR_Zabbix40SV_AllowRoot'': AllowRoot(string, default: なし)
  * ''VAR_Zabbix40SV_User'': User(string, default: なし)
  * ''VAR_Zabbix40SV_SSLCertLocation'': SSLCertLocation(string, default: なし)
  * ''VAR_Zabbix40SV_SSLKeyLocation'': SSLKeyLocation(string, default: なし)
  * ''VAR_Zabbix40SV_SSLCALocation'': SSLCALocation(string, default: なし)
  * ''VAR_Zabbix40SV_LoadModulePath'': LoadModulePath(string, default: なし)
  * ''VAR_Zabbix40SV_TLSCAFile'': TLSCAFile(string, default: なし)
  * ''VAR_Zabbix40SV_TLSCRLFile'': TLSCRLFile(string, default: なし)
  * ''VAR_Zabbix40SV_TLSCertFile'': TLSCertFile(string, default: なし)
  * ''VAR_Zabbix40SV_TLSKeyFile'': TLSKeyFile(string, default: なし)
  * ''VAR_Zabbix40SV_Include'': Include(string[], default: なし)
  * ''VAR_Zabbix40SV_LoadModule'': LoadModule(string[], default: なし)

- WEBインターフェイス設定（PHP環境変数）「/etc/zabbix/web/zabbix.conf.php」
  * ''VAR_Zabbix40SV_ZBX_SERVER'': Zabbixサーバホスト名(string, default: "localhost")
  * ''VAR_Zabbix40SV_ZBX_SERVER_PORT'': Zabbixサーバポート番号(string, default: "10051")
  * ''VAR_Zabbix40SV_ZBX_SERVER_NAME'': Zabbixサーバ表示名(string, default: なし)

- WEBインターフェイス設定（Apache連携）「/etc/httpd/conf.d/zabbix.conf」
  * ''VAR_Zabbix40SV_max_execution_time'': max_execution_time(number, default: "600")
  * ''VAR_Zabbix40SV_memory_limit'': memory_limit(string, default: "256M")
  * ''VAR_Zabbix40SV_post_max_size'': post_max_size(string, default: "32M")
  * ''VAR_Zabbix40SV_upload_max_filesize'': upload_max_filesize(string, default: "2M")
  * ''VAR_Zabbix40SV_max_input_time'': max_input_time(number, default: "600")
  * ''VAR_Zabbix40SV_always_populate_raw_post_data'': always_populate_raw_post_data(number, default: "-1")
  * ''VAR_Zabbix40SV_date_timezone'': date.timezone(string, default: "Asia/Tokyo")

- ログローテート設定「/etc/logrotate.d/zabbix-server」
  * ''VAR_Zabbix40SV_RotateCount'': 世代数(number, default: "12")
  * ''VAR_Zabbix40SV_RotateTiming'': 周期(daily|weekly|monthly, default: "weekly")

## Dependencies

特にありません。

## Usage

1. 本Roleを用いたPlaybookを作成します。
2. 任意変数を必要に応じて指定します。
3. Playbookを実行します。

## Example Playbook

インストールとすべての設定を行う場合は、提供した以下のRoleを"roles"ディレクトリに配置したうえで、
以下のようなPlaybookを作成してください。
- フォルダ構成
~~~
  - group_vars/
    ・ server1
    ・ server2
  - host_vars/
    ・ host1
    ・ host2
  - roles/
    ・ Zabbix40-Server_install/
    ・ Zabbix40-Server_setup/
    ・ Zabbix40-Server_ossetup/
  - Zabbix40-Server_install.yml
  - Zabbix40-Server_setup.yml
  - Zabbix40-Server_ossetup.yml
  - conf.yml
  - hosts
  - site.yml
~~~

- マスターPlaybook サンプル「Zabbix40-Server_setup.yml」
~~~
# Zabbix40-Server_setup.yml
 - name: setup Zabbix40_Server
   hosts: all
   gather_facts: yes
   become: yes
   tags:
    - setup
   roles:
     - Zabbix40-Server_setup
~~~

## Running Playbook

- extra-varsを利用する場合の実行例
> ansible-playbook site.yml -k -i hosts --extra-vars="@conf.yml"

- group\_varsを利用する場合の実行例   
  group\_varsで指定したグループ名がwebserver1の場合
> ansible-playbook site.yml -k -i hosts -l webserver1

- host\_varsを利用する場合の実行例  
  host\_varsで指定したホスト名がserver1の場合
> ansible-playbook site.yml -k -i hosts -l server1

- 本roleのみを実行する場合は --tags "setup"を付け加える
> ansible-playbook site.yml -k -i hosts --extra-vars="@conf.yml" --tags "setup"

# Copyright
Copyright (c) 2018 NEC Corporation

# Author Information
NEC Corporation