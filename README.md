## 概要

Ansible用playbookです。
CentOS7環境に、Zabbix-server(rpm)とZabbix-agent(rpm)の機能を自動設定する。


## 対象となる環境

* CentOS7 ( RHEL7 )
* インターネットにつながり、ansibleサーバからrootユーザで直接sshできること

## 自動設定内容

* os(共通(common))
	+ selinux無効化
	+ timezoneの設定(zone情報指定可能)
	+ chrony(時刻同期先IP指定可能)
	+ zabbix-repoの登録

* zabbix-server
	+ zabbix3.4(zabbix official repo)
	+ mariadb( DB名"zabbix"のユーザ"zabbix"のパスワード指定可能)
	+ httpd
	+ snmptrapd( snmptrapの受付コミュニティ名指定可能)
	+ snmptt(epel repo)

* zabbix-agent
	+ zabbix-agent3.4( zabbix-serverのIP指定可能)

# 指定可能なインストール先

* zabbix34/inventory/inventory.ini

```
[zabbix_servers] ... zabbix server
[zabbix_agents] ... zabbix-agent client
```

# 指定可能な設定内容

* zabbix34/roles/common/vars/main.yml

```
common:
- timezone: "Asia/Tokyo"  ... Timezone指定
    timeservers:
      - "192.168.10.1" ... TimeServer #1
      - "192.168.10.2" ... TimeServer #2
      ...............  ... TimeServer #..
zabbix_setup:
  - zabbix_mariadb_password: "password" ... MariaDB, zabbix's password
    snmptrap_community: "public"     ... snmptrapd 's community name
    zabbix_server_ip: "192.168.10.86" ... Zabbix server's IP
```

### 簡単な実施方法

# ansibleが稼働するサーバで、git clone実施

```
git clone https://github.com/mishikawan/zabbix34-ansible.git
cd zabbix34-ansible/
```

# Zabbix用IPアドレスを設定

- 192.168.10.86の部分を、Zabbix用IPアドレスに変更してください。
- 初期は、192.168.10.86にZabbixServer/Agentを導入するよう記載している。

```
vi zabbix34/inventory/inventory.ini

[zabbix_servers]
192.168.10.86 ansible_ssh_user=root
[zabbix_agents]
192.168.10.86 ansible_ssh_user=root
```

# ansible-playbook実行
```
ansible-playbook -i zabbix34/inventory/inventory.ini zabbix34/site.yml
```

無事完了すると、zabbixサーバ上でzabbixサーバが稼働しています。以下、URLでアクセス可能。
```
http://{zabbix-ip}/zabbix
  ID = Admin
  PASS = zabbix
```
