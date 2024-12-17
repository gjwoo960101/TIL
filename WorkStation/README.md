# WorkStation Setting
    - 어디서든 접속 가능한 WorkStaion PC를 setting 정리 문서
## WorkStaion Spec
### OS
    - Ubuntu


## Setting 방법
- PC setting
    1. https://ubuntu.com/download/server 접속후 ubuntu Server iso 설치  
    (!!주의!! DeskTop이 아닌 Server 설치임)
    2. 부팅 USB (rufus 프로그램 사용)
    3. 피시 재부팅시 USB 삽입,  F2로 bios접근 후 부팅순서를 USB 우선순위로 수정
    4. Ubuntu 설치시 주의사항
        - choose the type of installation > Ubuntu Server (선택)
        - Network configuration > DHCP (자동선택, 추후에 고정IP로 변경예정)
        - proxy,mirror,desk 는 무시하고 skip
        - profile configuration
            1. your name : 사용자가 알아보기 쉽게 표시하는용도 (중요하진 않은듯)
            2. your servers name : 서버의 이름
            3. pick a username : 사용자계정명 (SSH로 접속시 필수)
            4. Choose a password : 패스워드
        - SSH configuration > Install OpenSSH server(설치해야 외부에서 SSH로 접속가능)
        - Featured server snaps > 아무것도 설치 안함
    5. sudo apt-get update > sudo apt-get install net-tools (ifconfig 설치)  

- SSH Key 생성 / 등록 / 확인
    - 생성
        - ssh-keygen -t rsa > /home/[username]/.ssh에  
        id_rsa(Private key) /id_rsa.pub(Public key) 생성
    - 등록 
        - 현재 디렉토리에 public key가 존재할 경우  (public key 서버에 등록) 
            ```
            cat id_rsa.pub >> ./authorized_keys
            ```
        - 다른 디렉토리일 경우
            ```
            cat [key dir] >> /home/[username]/.ssh/authorized_keys
            ```
    - 접속확인
        1. 기존 아이디/패스워드 방식으로 로그인 후 안되는것 확인
            ```
                ssh [username]@[RemotePcIp]
            ```
        2. 확인 후 SSH Key로 접속
            ```
                ssh [username]@[RemotePcIp] -i [SSH key]
            ```

- WorkStation Docker 설치
    1. https://docs.docker.com/engine/install/ubuntu/ 접속
    2. Installation methods 부분을 그대로 실행
    ```
        # Add Docker's official GPG key:
        sudo apt-get update
        sudo apt-get install ca-certificates curl
        sudo install -m 0755 -d /etc/apt/keyrings
        sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
        sudo chmod a+r /etc/apt/keyrings/docker.asc

        # Add the repository to Apt sources:
        echo \
        "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
        $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
        sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
        sudo apt-get update
    ```
    ```
        sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
    ```
    3. docker 그룹생성 및 사용자 추가
        - 그룹생성 (해당 명령어 입력 후 이미 그룹이 생성되었다고 나오면 skip)
        ```
            sudo groupadd docker
        ```
        - 사용자 추가(우분투 버전에 따라 다를 수 있음 )
        ```
            sudo usermod -gG docker $USER
            sudo usermod -a -G docker $USER
        ```
        - 서버 재부팅
        ```
            sudo reboot
        ```


## 접속방법
    - ip확인 후 해당 ip로 SSH 접속 (ssh [username]@[RemotePcIp] -i [SSH key])

## VsCode Remote SSH로 접속
1. F1 > Remote-SSH: Open SSH Configuration file.. 선택
2. C:\Users\[사용자명]\.ssh\config 디렉토리 선택
3. 열린 config 파일에 SSH 접속정보 입력
```
HOST [SSH 호스트명]
    HostName [SSH 접속주소 또는 IP]
    User [서버접속 User명]
    IdentityFile [SSH Key]
```
4. F1 > Remote-SSH: Connect to Host .. 선택
5. 위에서 입력했던 호스트명 선택 > 해당 서버 Vscode로 접속