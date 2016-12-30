###快捷键 
* ctrl+u 刪除游標所在處到最前面的指令串內容
* 



####user group others
* useradd huang
* passwd huang
* groupadd my
* useradd -G my prouser1
* useradd -G my prouser2
* echo mypassword | passwd --stdin prouser1
* echo mypassword | passwd --stdin prouser2
* SUID: chmod u+s filename
* SGID: chmod g+s filename
* SBIT: chmod o+t filename

####范例	（让指令mycat 的功能跟cat的一样，只不过只有群组progroup 的用户能使用）
	[root@localhost ~]# which cat
	/bin/cat
	[root@localhost ~]# echo $PATH
	/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
	[root@localhost ~]# cp /bin/cat /usr/local/bin/mycat
	[root@localhost ~]# ll /usr/local/bin/mycat
	-rwxr-xr-x. 1 root root 54048  7月  1 22:16 /usr/local/bin/mycat
	[root@localhost ~]# chgrp progroup /usr/local/bin/mycat
	[root@localhost ~]# chmod 750 /usr/local/bin/mycat
	[root@localhost ~]# ll /usr/local/bin/mycat
	-rwxr-x---. 1 root progroup 54048  7月  1 22:16 /usr/local/bin/mycat

####使用 pstree 與 ps aux 觀察全系統的程序
	pstree -Aup (附加pid)
	ps aux (將系統中的程序呼叫出來，且輸出的資訊較為豐富)
####top 動態觀察程序
	top(觀察程序的動態資訊,每 5 秒鐘更新一次程序的現況)



#####bash 的工作管理
	command & ：直接將 command 丟到背景中執行，若有輸出，最好使用資料流重導向輸出到其他檔案
	[ctrl]+z ：將目前正在前景中的工作丟到背景中暫停
	jobs [-l]：列出目前的工作資訊
	fg %n ：將第 n 個在背景當中的工作移到前景來操作
	bg %n ：將第 n 個在背景當中的工作變成執行中



12-15 遇到了一个很坑的问题，是关于jvm内存不足的问题，我查了相当多的资料，并且跟着他们的步骤执行
但还是不行
12-16 看到一篇跟我遇到的问题类似文章，这篇文章大概是说没有设置swap
问题大概就是这样子的：
	如果系统的物理内存用光了，系统就会跑得很慢，但仍能运行;
	如果Swap空间用光了，那么系统就会发生错误。
	例如，Swap空间用完，则服务进程无法启动，通常会出现“application is out of memory”的错误，
	严重时会造成服务进程的死锁。
	因此Swap空间的分配是很重要的,通常Swap空间的大小应是物理内存的2-2.5倍.

我查看了我的swap空间 发现为0
> free -m

终于发现问题所在了
继续跟着大神走：
> dd if=/dev/zero of=/swap/file bs=1024 count=512000(使用dd命令创建/home/swap这么一个分区文件。文件的大小是512000个block，一般情况下1个block为1K，所以这里空间是512M)
> /sbin/mkswap /swapfile (接着再把这个分区变成swap分区)
> /sbin/swapon /swapfile (再接着使用这个swap分区。使其成为有效状态) 新的swap没有自动启动
> vi /etc/fstab(需要修改/etc/fstab文件，增加如下一行)
> /swapfile swap swap defaults 0 0

####vim操作
* :set nu 显示行数 :set nonu
* u 撤销一步操作
* ctrl+f 下一页
* ctrl+b 上一页
* . 重复之前的操作
* dd 删除游标指向的当前行
* ndd 删除n行
* nG 移动到n行
* G 移动到最后一行
* 6yy 复制当前行及以下的6行 
* ctrl+v 移动光标 y(复制) 移动到指定的位置 p(粘贴) 区块复制 d(删除反白的区域)
* :sp /etc/hosts 多窗口编辑 [ctrl]+w+↑』及『[ctrl]+w+↓ 按鍵的按法是：先按下 [ctrl] 不放， 再按下 w 後放開所有的按鍵，然後再按下 j (或向下方向鍵)，則游標可移動到下方的視窗
* r /tmp/text.txt 将text.txt的内容添加到当前编辑的文件的尾部

####history
* history 查看以前下过的指令
* !66 执行第66条指令
* !! 执行上一条指令
* !al 执行最近以al开头的指令


####login shell
* source ：讀入環境設定檔的指令
	source ~/.bashrc 將家目錄的 ~/.bashrc 的設定讀入目前的 bash 環境中

####快速创建文件，并且设置结束句
* [dmtsai@study ~]$ cat > catfile << "eof"
* > This is a test.
* > OK now stop
* > eof  <==輸入這關鍵字，立刻就結束而不需要輸入 [ctrl]+d

####擷取命令： cut, grep
* cut
	cut -d'分隔字元' -f fields <==用於有特定分隔字元 echo ${PATH} | cut -d ':' -f 3,5
	cut -c 字元區間            <==用於排列整齊的訊息

* [dmtsai@study ~]$ grep [-acinv] [--color=auto] '搜尋字串' filename
	選項與參數：
	-a ：將 binary 檔案以 text 檔案的方式搜尋資料
	-c ：計算找到 '搜尋字串' 的次數
	-i ：忽略大小寫的不同，所以大小寫視為相同
	-n ：順便輸出行號
	-v ：反向選擇，亦即顯示出沒有 '搜尋字串' 內容的那一行！ last | grep -v 'root'
	--color=auto ：可以將找到的關鍵字部分加上顏色的顯示喔！
	grep -vn 'the' regular_express.txt	

###排序
####sort
* cat /etc/passwd | sort -t ':' -k 3
####uniq
* last | cut -d ' ' -f1 | sort | uniq -c (每個人的登入總次數)
####wc
* cat /etc/man_db.conf | wc
* 131     723    5171（輸出的三個數字中，分別代表： 『行、字數、字元數』） 
* last | grep [a-zA-Z] | grep -v 'wtmp' | grep -v 'reboot' | \
> grep -v 'unknown' |wc -l（用于获取登录系统的总人数）

####
* ls -l / | tee -a ~/homefile | more

####字元轉換命令： tr, col, join, paste, expand
#####tr
	last | tr '[a-z]' '[A-Z]' (將 last 輸出的訊息中，所有的小寫變成大寫字元)
	cat /etc/passwd | tr -d ':' (將 /etc/passwd 輸出的訊息中，將冒號 (:) 刪除)
	
head -n 3 /etc/passwd /etc/shadow(用于比较两个文件头3行的信息)

#####join
	選項與參數：
		-t  ：join 預設以空白字元分隔資料，並且比對『第一個欄位』的資料，
      如果兩個檔案相同，則將兩筆資料聯成一行，且第一個欄位放在第一個！
		-i  ：忽略大小寫的差異；
		-1  ：這個是數字的 1 ，代表『第一個檔案要用那個欄位來分析』的意思；
		-2  ：代表『第二個檔案要用那個欄位來分析』的意思。
	
	join -t ':' /etc/passwd /etc/shadow | head -n 3

#####paste 直接『將兩行貼在一起，且中間以 [tab] 鍵隔開』
	paste /etc/passwd /etc/shadow

#####split
	split [-bl] file PREFIX
	選項與參數：
		-b  ：後面可接欲分割成的檔案大小，可加單位，例如 b, k, m 等；
		-l  ：以行數來進行分割。
		PREFIX ：代表前置字元的意思，可作為分割檔案的前導文字。
	cd /tmp; split -b 300k /etc/services services (我的 /etc/services 有六百多K，若想要分成 300K 一個檔案時)
	cat services* >> servicesback (將上面的三個小檔案合成一個檔案，檔名為 servicesback)
	ls -al / | split -l 10 - lsroot
	wc -l lsroot*


#####-的用途
	tar -cvf - /home | tar -xvf - -C /tmp/homeback
	我將 /home 裡面的檔案給他打包，但打包的資料不是紀錄到檔案，而是傳送到 stdout； 經過管線後，將 tar -cvf - /home 傳送給後面的 tar -xvf - 』。
	後面的這個 - 則是取用前一個指令的 stdout， 因此，我們就不需要使用 filename 了


	情境模擬題一：由於 ~/.bash_history 僅能記錄指令，我想要在每次登出時都記錄時間，並將後續的指令 50 筆記錄下來， 可以如何處理？

	目標：瞭解 history ，並透過資料流重導向的方式記錄歷史命令；
	前提：需要瞭解本章的資料流重導向，以及瞭解 bash 的各個環境設定檔資訊。

	其實處理的方式非常簡單，我們可以瞭解 date 可以輸出時間，而利用 ~/.myhistory 來記錄所有歷史記錄， 而目前最新的 50 筆歷史記錄可以使用 history 50 來顯示，故可以修改 ~/.bash_logout 成為底下的模樣：
	[dmtsai@study ~]$ vim ~/.bash_logout
	date >> ~/.myhistory
	history 50 >> ~/.myhistory
	clear

###正则表达式
* grep -n 'the' regular_express.txt
* grep -vn 'the' regular_express.txt
* grep -in 'the' regular_express.txt
* grep -n 't[ae]st' regular_express.txt
* grep -n '[^g]oo' regular_express.txt
* grep -n '[^a-z]oo' regular_express.txt
* grep -n '[0-9]' regular_express.txt
* grep -n '^the' regular_express.txt
* grep -n '^[a-z]' regular_express.txt
* grep -n '^[^a-zA-Z]' regular_express.txt
* grep -n '\.$' regular_express.txt(\. 转义)
* grep -n '' regular_express.txt
* cat -An regular_express.txt | head -n 10 | tail -n 6 (斷行字元的判斷)
* grep -n '^$' regular_express.txt(找出哪一行是空白行)
* grep -v '^$' /etc/rsyslog.conf | grep -v '^#'(去空行并且去注释行)
* grep -n 'g..d' regular_express.txt
* grep -n 'oo*' regular_express.txt
* grep -n '[0-9]' regular_express.txt
* grep -n 'o\{2\}' regular_express.txt (找到兩個 o 的字串)
* grep -n 'go\{2,5\}g' regular_express.txt
* grep -n 'go\{2,\}g' regular_express.txt
* 

#####sed
	sed [-nefr] [動作]
	選項與參數：
	-n  ：使用安靜(silent)模式。在一般 sed 的用法中，所有來自 STDIN 的資料一般都會被列出到螢幕上。
	      但如果加上 -n 參數後，則只有經過 sed 特殊處理的那一行(或者動作)才會被列出來。
	-e  ：直接在指令列模式上進行 sed 的動作編輯；
	-f  ：直接將 sed 的動作寫在一個檔案內， -f filename 則可以執行 filename 內的 sed 動作；
	-r  ：sed 的動作支援的是延伸型正規表示法的語法。(預設是基礎正規表示法語法)
	-i  ：直接修改讀取的檔案內容，而不是由螢幕輸出。
	
	動作說明：  [n1[,n2]]function
	n1, n2 ：不見得會存在，一般代表『選擇進行動作的行數』，舉例來說，如果我的動作
	         是需要在 10 到 20 行之間進行的，則『 10,20[動作行為] 』
	
	function 有底下這些咚咚：
	a   ：新增， a 的後面可以接字串，而這些字串會在新的一行出現(目前的下一行)～
	c   ：取代， c 的後面可以接字串，這些字串可以取代 n1,n2 之間的行！
	d   ：刪除，因為是刪除啊，所以 d 後面通常不接任何咚咚；
	i   ：插入， i 的後面可以接字串，而這些字串會在新的一行出現(目前的上一行)；
	p   ：列印，亦即將某個選擇的資料印出。通常 p 會與參數 sed -n 一起運作～
	s   ：取代，可以直接進行取代的工作哩！通常這個 s 的動作可以搭配正規表示法！
      例如 1,20s/old/new/g 就是啦！

	nl /etc/passwd | sed '2,5d'(将列出来的内容删除掉2到5行)
	nl /etc/passwd | sed '3,$d' 删除从第三行到最后一行
	nl /etc/passwd | sed '2a drink tea'    a 後面加上的字串就已將出現在第二行後面
	nl /etc/passwd | sed '2,5c No 2-5 number'    第2-5行的內容取代成為『No 2-5 number』
	nl /etc/passwd | sed -n '5,7p'   僅列出 /etc/passwd 檔案內的第 5-7 行
	sed 's/要被取代的字串/新的字串/g'
	/sbin/ifconfig eth0 | grep 'inet ' | sed 's/^.*inet //g' \
		>   | sed 's/ *netmask.*$//g'
	cat /etc/man_db.conf | grep 'MAN'| sed 's/#.*$//g' | sed '/^$/d' (替换注释后去空行)
	grep 'A(xyz)+C' 找開頭是 A 結尾是 C ，中間有一個以上的 "xyz" 字串

#####awk：好用的資料處理工具（awk 則比較傾向於一行當中分成數個『欄位』來處理）
*	NF	每一行 ($0) 擁有的欄位總數
*	NR	目前 awk 所處理的是『第幾行』資料
*	FS	目前的分隔字元，預設是空白鍵	

*	last -n 5 | awk '{print $1 "\t" $3}'      取出帳號與登入者的 IP ，且帳號與 IP 之間以 [tab] 隔開
*	last -n 5| awk '{print $1 "\t lines: " NR "\t columns: " NF}'
*	cat /etc/passwd | awk '{FS=":"} $3 < 10 {print $1 "\t " $3}'   在 /etc/passwd 當中是以冒號 ":" 來作為欄位的分隔， 該檔案中第一欄位為帳號，第三欄位則是 UID
*	cat /etc/passwd | awk 'BEGIN {FS=":"} $3 < 10 {print $1 "\t " $3}'
	
	example:
		Name    1st     2nd     3th
		VBird   23000   24000   25000
		DMTsai  21000   20000   23000
		Bird2   43000   42000   41000
	cat pay.txt | \
	> awk 'NR==1{printf "%10s %10s %10s %10s %10s\n",$1,$2,$3,$4,"Total" }
	> NR>=2{total = $2 + $3 + $4
	> printf "%10s %10d %10d %10d %10.2f\n", $1, $2, $3, $4, total}'

	or 
	cat pay.txt | \
	> awk '{if(NR==1) printf "%10s %10s %10s %10s %10s\n",$1,$2,$3,$4,"Total"}
	> NR>=2{total = $2 + $3 + $4
	> printf "%10s %10d %10d %10d %10.2f\n", $1, $2, $3, $4, total}'


#####檔案比對工具
* diff [-bBi] from-file to-file
	選項與參數：
	from-file ：一個檔名，作為原始比對檔案的檔名；
	to-file   ：一個檔名，作為目的比對檔案的檔名；
	注意，from-file 或 to-file 可以 - 取代，那個 - 代表『Standard input』之意。

		-b  ：忽略一行當中，僅有多個空白的差異(例如 "about me" 與 "about     me" 視為相同
		-B  ：忽略空白行的差異。
		-i  ：忽略大小寫的不同。


	mkdir -p /tmp/testpw <==先建立測試用的目錄
	cd /tmp/testpw
	cp /etc/passwd passwd.old
	cat /etc/passwd | sed -e '4d' -e '6c no six line' > passwd.new  （sed 後面如果要接超過兩個以上的動作時，每個動作前面得加 -e 才行！）
	
	diff passwd.old passwd.new

* patch
	範例一：以 /tmp/testpw 內的 passwd.old 與 passwd.new  製作補丁檔案
	diff -Naur passwd.old passwd.new > passwd.patch
	cat passwd.patch
	patch -p0 < passwd.patch (範例二：將剛剛製作出來的 patch file 用來更新舊版資料)
	ll passwd*
	patch -R -p0 < passwd.patch(範例三：恢復舊檔案的內容)
	ll passwd*
####如果是在全系统中查找的话
	先用 find 去找出檔案；
	用 xargs 將這些檔案每次丟 10 個給 grep 來作為參數處理；
	grep 實際開始搜尋檔案內容。
	如： find / -type f 2> /dev/null | xargs -n 10 grep '\*'	
	find / -type f 2> /dev/null | xargs -n 10 grep -l '\*'   （只是想要知道檔名）
	
	
	
###shell 编程
#####编写shell的规范
	script 的功能；
	script 的版本資訊；
	script 的作者與聯絡方式；
	script 的版權宣告方式；
	script 的 History (歷史紀錄)；
	script 內較特殊的指令，使用『絕對路徑』的方式來下達；
	script 運作時需要的環境變數預先宣告與設定。	


#####条件判断式
* 利用if...then
<pre><code>
	if [条件判断式]; then
		当条件判断式成立时，可以进行的指令工作内容；
	fi
	
	# 一個條件判斷，分成功進行與失敗進行 (else)
	if [ 條件判斷式 ]; then
		當條件判斷式成立時，可以進行的指令工作內容；
	else
		當條件判斷式不成立時，可以進行的指令工作內容；
	fi

	# 多個條件判斷 (if ... elif ... elif ... else) 分多種不同情況執行
	if [ 條件判斷式一 ]; then
		當條件判斷式一成立時，可以進行的指令工作內容；
	elif [ 條件判斷式二 ]; then
		當條件判斷式二成立時，可以進行的指令工作內容；
	else
		當條件判斷式一與二均不成立時，可以進行的指令工作內容；
	fi
</code></pre>
* 利用 case ..... esac 判斷
<pre><code>
	case  $變數名稱 in   <==關鍵字為 case ，還有變數前有錢字號
	  "第一個變數內容")   <==每個變數內容建議用雙引號括起來，關鍵字則為小括號 )
		程式段
		;;            <==每個類別結尾使用兩個連續的分號來處理！
	  "第二個變數內容")
		程式段
		;;
	  *)                  <==最後一個變數內容都會用 * 來代表所有其他值
		不包含第一個變數內容與第二個變數內容的其他程式執行段
		exit 1
		;;
	esac                  <==最終的 case 結尾！『反過來寫』思考一下！
</pre></code>
* 利用 function 功能
<pre><code>
	function fname() {
		程式段
	}

	例如：
		function printit(){
			echo "Your choice is ${1}"   # 這個 $1 必須要參考底下指令的下達
		}
	调用：
		printit 1
</pre></code>
* while do done, until do done (不定迴圈)
<pre><code>
	while [ condition ]  <==中括號內的狀態就是判斷式
	do            <==do 是迴圈的開始！
		程式段落
	done          <==done 是迴圈的結束
	例子：
	while [ "${yn}" != "yes" -a "${yn}" != "YES" ]
	do
		read -p "Please input yes/YES to stop this program: " yn
	done
	echo "OK! you input the correct answer."	


	//當 condition 條件成立時，就終止迴圈， 否則就持續進行迴圈的程式段。
	until [ condition ]
	do
		程式段落
	done
	例子：
	until [ "${yn}" == "yes" -o "${yn}" == "YES" ]
	do
		read -p "Please input yes/YES to stop this program: " yn
	done
	echo "OK! you input the correct answer."
</pre></code>
* for...do...done (固定迴圈)
<pre><code>
	for var in con1 con2 con3 ...
	do
		程式段
	done
	例子1：
	for animal in dog cat elephant
	do
		echo "There are ${animal}s.... "
	done

	例子2：
	users=$(cut -d ':' -f1 /etc/passwd)    # 擷取帳號名稱
	for username in ${users}               # 開始迴圈進行！
	do
	        id ${username}
	done
	
	例子3：
	network="192.168.1"              # 先定義一個網域的前面部分！
	for sitenu in $(seq 1 100)       # seq 為 sequence(連續) 的縮寫之意
	do
		# 底下的程式在取得 ping 的回傳值是正確的還是失敗的！
	        ping -c 1 -w 1 ${network}.${sitenu} &> /dev/null && result=0 || result=1
		# 開始顯示結果是正確的啟動 (UP) 還是錯誤的沒有連通 (DOWN)
	        if [ "${result}" == 0 ]; then
	                echo "Server ${network}.${sitenu} is UP."
	        else
	                echo "Server ${network}.${sitenu} is DOWN."
	        fi
	done
</pre></code>
* for...do...done 的數值處理
<pre><code>
	for (( 初始值; 限制值; 執行步階 ))
	do
		程式段
	done

	例子1：
	read -p "Please input a number, I will count for 1+2+...+your_input: " nu
	s=0
	for (( i=1; i<=${nu}; i=i+1 ))
	do
		s=$((${s}+${i}))
	done
	echo "The result of '1+2+3+...+${nu}' is ==> ${s}"
</pre></code>
* shell script 的追蹤與 debug
<pre><code>
	sh [-nvx] scripts.sh
	選項與參數：
	-n  ：不要執行 script，僅查詢語法的問題；
	-v  ：再執行 sccript 前，先將 scripts 的內容輸出到螢幕上；
	-x  ：將使用到的 script 內容顯示到螢幕上，這是很有用的參數！
	
	sh -n dir_perm.sh 
	# 若語法沒有問題，則不會顯示任何資訊！

	sh -x show_animal.sh
	+ PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:/root/bin
	+ export PATH
	+ for animal in dog cat elephant
	+ echo 'There are dogs.... '
	There are dogs....
	+ for animal in dog cat elephant
	+ echo 'There are cats.... '
	There are cats....
	+ for animal in dog cat elephant
	+ echo 'There are elephants.... '
	There are elephants....

</pre></code>









































