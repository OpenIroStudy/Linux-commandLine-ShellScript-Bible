## FTP란?
* File Transfer Protocol의 약자로, TCP/IP와 같은 프로토콜의 일종으로, 클라이언트와 서버사이에 파일을 전송하기 위한 프로토콜이다.  
* FTP 서버는 클라이언트에게 파일을 전송하기 위한 서버를 말함
* FTP를 사용할 때 시스템에 로그인하여 파일을 엑세스 하려면 네트워크 시스템 관리자에게 사용권한을 받아야 사용가능.
* 익명(anonymous)로 엑세스 할 수 있는 FTP도 존재.
    * anonymous FTP는 사용자들이 서버에 자신을 식별시키지 않더라도 파일에 접근할 수 있는 방법
    * 해당 서버에서 부여된 사용자 아이디나 패스워드가 없더라도 작업이 가능
    * 아이디로 `anonymou`라고 입력하고 패스워드에는 자신의 이메일 주소를 입력하면 로그인 허용.
    * 패스워드를 넣지않거나 어떤 내용을 넣더라도 로그인 하는데 문제는 없지만 이메일 주소를 넣는 것이 통신상의 예의라고함


<hr>
# vsftpd의 설치와 운영
Very Secure FTPD는 CentOS에서 기본적으로 제공되며, 리눅스와 유닉스 환경에서 보안성과 성능이 우수한 ftp 서버로 인정받고 있다.  
설치, 운영이 쉬워서 FTP서버를 운영하는데 많이 사용됨.  

```
yum -y install vsftpd
```  
위 명령을 입력해 vsftpd 패키지 설치.  
vsftpd에 anonymous로 접속되는 디렉터리는 `/var/ftp/`다.  
다음 명령으로 이 디렉터리 아래에 있는 /pub 디렉터리에 샘플 파일 몇 개 복사하고 서비스 가동시킨다.  
vsftpd 패키지의 서비스 이름도  vsftpd.  

```  

cd /var/ftp
ls
cd pub
cp /boot/vmlinuz-4* file1  -> 샘플 파일 복사
ls
systemctl restart vsftpd -> vsftpd 서비스 재가동
systemctl enable vsftpd  -> 리눅스 부팅 시 vsftpd 서비스를 자동으로 가동 
```  

![image](https://user-images.githubusercontent.com/67637716/194819949-b9231f76-7709-430c-b06d-a23debfabc65.png)  

외부에서 FTP서버에 원활하게 접근할 수 있도록, 21번 포트 열어놓는다.  
![image](https://user-images.githubusercontent.com/67637716/194820049-ca74d7fa-0aab-4dd5-9211-d4334c168b66.png)  

vsftpd의 설정 파일은 /etc/vsftpd/vsftpd.conf 파일이다.  
12행에 `anonymous_enable=NO`로 되어있는 부분을 YES로 변경한 후 저장.  

=> 이전 버전의 vsftpd는 기본적으로 anonymous의 접속이 허용되어 있었지만, 최근의 리눅스에서는 허용이 안 되어있다. (보안 측면에서 도움이 됨)  
![image](https://user-images.githubusercontent.com/67637716/194820356-c8ff21a1-83b3-48c7-82da-828db7aadaf8.png)  
설정변경했으니 systemctl restart vsftpd명령으로 서비스 재시작  

# FileZilla를 사용해 파일을 다운로드/업로드
FileZilla Client를 다운받는다.(모두 기본 설정 값으로 설치)  

![image](https://user-images.githubusercontent.com/67637716/194820946-1a23c1e9-d054-4b1b-b2d1-c8f14d768a35.png)  












