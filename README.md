# ZabbixServer4.0 for RHEL7

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
本プロジェクトのリポジトリで統合監視ソフトウェアであるZabbixServerのインストール、セットアップRoleを公開しています。  
OSSの対象バージョンは以下のバージョンとなります。  
・Zabbix 4.0  
　  
対応OSは以下となります。  
・RHEL7  

各ロールの説明はREADME_role.mdをご参照ください。

## Supports

- 管理マシン(Ansibleサーバ)
  * Linux系OS（RHEL/CentOS）
  * Ansible バージョン 2.5 以上 (動作確認済みバージョン：2.5、2.6)
  * Python バージョン 2.6 または2.7


- 管理対象マシン(構築対象マシン)
  * Red Hat Enterprise Linux 7

## roles

  - Zabbix40-Server_install  
     Zabbix Server 4.0関連のパッケージのインストールを行うroleです。  
     提供している機能は以下です。

    * Apache HTTP Serverのインストール
    * MariaDBのインストール
    * PHP、及びPHP依存パッケージのインストール
    * 追加パッケージ(trousers)のインストール
    * Zabbixリポジトリパッケージのインストール
    * ZabbixServer4.0関連パッケージのインストール


  - Zabbix40-Server_setup  
     Zabbix Server 4.0、及び関連コンポーネントのconfigファイルへの設定を行うroleです。  
     提供している機能は以下です。

     * MariaDBのコンフィグファイルの設定
     * MariaDBへのZabbix用DBの作成、初期データのインポート
     * MariaDBへのZabbixからのアクセスユーザの作成
     * Zabbix Server関連のconfigファイルの設定
     　　-　zabbix_server.conf、zabbix.conf.php、zabbix.conf
     * Zabbix Serverのログファイルのlogrotateの設定


  - Zabbix40-Server_ossetup  
     Zabbix Server 4.0のサービスの状態の設定／変更、再起動を行うroleです。  
     提供している機能は以下です。

    * 以下、コンポーネントのサービスの自動起動設定の変更
       - MariaDB
       - Apache HTTP Server
       - Zabbix Server 4.0
    * 以下、コンポーネントの再起動
       - MariaDB
       - Apache HTTP Server
       - Zabbix Server 4.0


 ## Evidence

本ロールは、gatheringに公開されているエビデンス取得対応をしています。
各ロールにエビデンスを定義してありますので、エビデンス取得ロールを利用して設定内容のエビデンスを取得することができます。
詳しい利用方法については、gathering ロールのREADME.md等をご参照ください。

 - 定義されているエビデンス

      * roles/Zabbix40-Server_install/vars/gathering_definition.yml  
        ・ Zabbixに関連するインストールパッケージ環境の取得  
        ・ 関連情報の取得  

   ~~~
     ---
     command:
       - cat /etc/redhat-release
       - getenforce
       - systemctl status firewalld
       - rpm -qa
       - yum repolist
       - ls -l /etc/yum.repos.d/*.repo
       - cat /etc/yum.repos.d/*.repo
       - cat /mnt/media.repo
       - ls -l /etc/zabbix/zabbix_server.conf
       - ls -l /etc/zabbix/web/zabbix.conf.php
       - ls -l /etc/my.cnf
       - ls -l /etc/httpd/conf.d/zabbix.conf
       - ls -l /etc/logrotate.d/zabbix_server
       - ls -l /var/log/zabbix/*server*
       - ls -l /var/run/zabbix/*server*
       - tail -n 100 /var/log/zabbix/zabbix_server.log
       - ls -l /tmp
   ~~~

      * roles/Zabbix40-Server_setup/vars/gathering_definition.yml
        ・ 設定されるconfファイルの取得

   ~~~
      ---
      command:
        - cat /etc/my.cnf
        - cat /etc/zabbix/zabbix_server.conf
        - cat /etc/zabbix/web/zabbix.conf.php
        - cat /etc/httpd/conf.d/zabbix.conf
        - cat /etc/logrotate.d/zabbix-server
        - ls -l /var/log/zabbix/*server*
        - ls -l /tmp
      file:
        - /var/log/zabbix/zabbix_server.log
   ~~~

      * roles/Zabbix40-Server_ossetup/vars/gathering_definition.yml
         ・ Zabbix Server 4.0関連のサービスの状態

   ~~~
   ---
   command:
     - systemctl status mariadb
     - systemctl status httpd
     - systemctl status zabbix-server
     - systemctl list-unit-files | grep mariadb
     - systemctl list-unit-files | grep httpd
     - systemctl list-unit-files | grep zabbix-server
     - ls -l /var/log/zabbix/*server*
   file:
     - /var/log/zabbix/zabbix_server.log
   ~~~


 - エビデンス取得のためのマスタPlaybook(gathering_definition.yml)の例

   ~~~
      ###  install master playbook for Evidence

      ---
       - name: evidence Zabbix40-Server_install
         hosts: all
         become: yes
         roles:
          - role: gathering
            VAR_gathering_definition_role_path: "{{ inventory_dir }}/roles/Zabbix40-Server_install"

      - name: evidence Zabbix40-Server_setup
        hosts: all
        become: yes
        roles:
         - role: gathering
           VAR_gathering_definition_role_path: "{{ inventory_dir }}/roles/Zabbix40-Server_setup"

      - name: evidence Zabbix40-Server_ossetup
        hosts: all
        become: yes
        roles:
         - role: gathering
           VAR_gathering_definition_role_path: "{{ inventory_dir }}/roles/Zabbix40-Server_ossetup"
   ~~~


## 備考
本プロジェクトのリポジトリで公開しているRoleにはZabbixServerをインストールのためのリポジトリパッケージを含んでいますが、ZabbixServer自体のrpmファイルは含まれません。  

# Copyright
Copyright (c) 2018 NEC Corporation

# Author Information
NEC Corporation