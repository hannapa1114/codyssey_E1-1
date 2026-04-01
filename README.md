# 프로젝트 개요
- 이 프로젝트의 목표는 같은 서비스를 여러번 실행해도 재현되는 환경을 만드는 사고방식을 경험하는 것이다.(이 경험은 이후 리눅스 트러블 슈팅, CI/CD 파이프라인, 클라우드 배포/운영 등으로 확장됨)
- 개발환경(터미널, Docker, Git)을 직접 세팅해보는 것.
- 팀원들 중 누구라도 같은 방식으로 실행, 배포, 디버깅할 수 있는 환경을 구성해 보는 것.
- 터미널로 작업 디렉토리와 권한을 정리
- Docker 설치 및 점검, 컨테이너 실행 및 관리
- 웹서버를 Dockerfile로 컨테이너화 하고, 포트 매핑으로 접속을 확인하며, 바인드 마운트/볼륨으로 "변경 반영"과 "데이터 영속성을"을 직접 검증

## 실행환경
- OS : Ubuntu questing
- Shell : bash 
- Docker Version : 28.5.2
- Git Version : 2.53.0

## 과제 목표
1. 절대 경로와 상대 경로의 차이를 예시를 들어 설명할 수 있다.
2. 파일 권한의 의미(r/w/x)와 755, 644 같은 표기가 어떤 규칙으로 해석되는지 설명할 수 있다.
3. 기존 Dockerfile을 기반으로 “커스텀 이미지”를 만들 수 있다.
4. 포트 매핑이 필요한 이유를 설명할 수 있다.
5. Docker 볼륨(영속 데이터)을 설명할 수 있다.
6. Git과 GitHub의 역할 차이(로컬 버전관리 vs 원격 협업 플랫폼)를 설명할 수 있다.
  
## 수행 항목 체크리스트
- [x] 터미널 조작 로그
- [x] 작업 디렉토리 권한 정리
- [x] Docker 설치및기본 점검
- [x] Docker 기본 운영명령 수행 
- [x] 컨테이너 실행 실습
- [x] Dockerfile 기반 커스텀 이미지제작
- [x] 포트 매핑 접속 성공 증거
- [x] Docker 볼륨 영속성 증거
- [x] Git 설정 및 GitHub/VSCode 연동 증거

## 기능 요구사항 (기술 문서 내 명령/출력은 코드블록으로 정리)
1. 터미널 조작 로그 기록
현재 경로 확인
```
flexaveler5737@ubuntu:~$ pwd
/home/flexaveler5737
```
현재 경로 파일 확인(숨김파일까지)
```
flexaveler5737@ubuntu:~$ ls -a
.  ..  .bash_logout  .bashrc  .profile
```
폴더 만들기, 폴더 이동하기
```
flexaveler5737@ubuntu:~$ mkdir clipractice
flexaveler5737@ubuntu:~$ cd clipractice/
flexaveler5737@ubuntu:~/clipractice$ cd ..
```
파일 만들기
```
flexaveler5737@ubuntu:~$ touch test.txt
flexaveler5737@ubuntu:~$ ls
clipractice  test.txt
```
파일 복사
```
flexaveler5737@ubuntu:~$ cp test.txt test1.txt
flexaveler5737@ubuntu:~$ ls
clipractice  test.txt  test1.txt
```
파일 이름 변경
```
flexaveler5737@ubuntu:~$ mv test.txt test2.txt
flexaveler5737@ubuntu:~$ ls
clipractice  test1.txt  test2.txt
```
파일 이동
```
flexaveler5737@ubuntu:~$ mv test2.txt clipractice/
flexaveler5737@ubuntu:~$ ls clipractice/
test2.txt
```
파일 삭제
```
flexaveler5737@ubuntu:~$ rm test1.txt 
flexaveler5737@ubuntu:~$ ls
clipractice
```
파일 내용 확인
```
flexaveler5737@ubuntu:~$ cat clipractice/test2.txt 
flexaveler5737@ubuntu:~$ vi clipractice/test2.txt
flexaveler5737@ubuntu:~$ less clipractice/test2.txt 
flexaveler5737@ubuntu:~$ cat clipractice/test2.txt 
hello, world
```
<img width="861" height="436" alt="Screenshot 2026-04-01 at 6 03 00 PM" src="https://github.com/user-attachments/assets/ba797592-c31e-4436-a964-7bf3ca1936b5" />

2. 권한 실습 및 증거 기록
```
   r(4) : 읽기, w(2): 쓰기, x(1): 실행
   숫자의 합으로 권한을 부여할 수 있다.
   읽기+쓰기 = 6 , 읽기+실행 = 5, 모든권한 = 7
   숫자 첫번째는 소유자, 두번째는 그룹, 세번째는 기타 사용자이다.
```
```
chmod 755 파일명      # rwxr-xr-x
chmod 644 파일명      # rw-r--r--
chmod 700 폴더명      # rwx------

755 : 소유자는 모두, 다른 사용자는 읽기+실행만
644 : 소유자는 읽기+쓰기, 다른 사용자는 읽기만
700 : 소유자만 모든 권한
```
```
flexaveler5737@ubuntu:~$ ls -l
total 0
drwxr-xr-x 1 flexaveler5737 flexaveler5737 18 Apr  1 18:01 clipractice
flexaveler5737@ubuntu:~$ chmod 700 clipractice/
flexaveler5737@ubuntu:~$ ls -l
total 0
drwx------ 1 flexaveler5737 flexaveler5737 18 Apr  1 18:01 clipractice
flexaveler5737@ubuntu:~$ ls -l clipractice/test2.txt 
-rw-r--r-- 1 flexaveler5737 flexaveler5737 13 Apr  1 18:01 clipractice/test2.txt
flexaveler5737@ubuntu:~$ chmod 644 clipractice/test2.txt 
flexaveler5737@ubuntu:~$ ls -l clipractice/test2.txt 
-rw-r--r-- 1 flexaveler5737 flexaveler5737 13 Apr  1 18:01 clipractice/test2.txt
flexaveler5737@ubuntu:~$ chmod 655 clipractice/test2.txt 
flexaveler5737@ubuntu:~$ ls -l clipractice/test2.txt 
-rw-r-xr-x 1 flexaveler5737 flexaveler5737 13 Apr  1 18:01 clipractice/test2.txt
flexaveler5737@ubuntu:~$ 
```
<img width="858" height="438" alt="Screenshot 2026-04-01 at 6 16 01 PM" src="https://github.com/user-attachments/assets/74149228-9a03-4546-a40c-9078967bf115" />
   
3. Docker 설치 및 기본 점검
```
flexaveler5737@c3r1s5 ~ % docker -v
Docker version 28.5.2, build ecc6942
flexaveler5737@c3r1s5 ~ % docker info
Client:
 Version:    28.5.2
 Context:    orbstack
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc.)
    Version:  v0.29.1
    Path:     /Users/flexaveler5737/.docker/cli-plugins/docker-buildx
  compose: Docker Compose (Docker Inc.)
    Version:  v2.40.3
    Path:     /Users/flexaveler5737/.docker/cli-plugins/docker-compose

Server:
 Containers: 1
  Running: 0
  Paused: 0
  Stopped: 1
 Images: 1
 Server Version: 28.5.2
 Storage Driver: overlay2
  Backing Filesystem: btrfs
  Supports d_type: true
  Using metacopy: false
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 2
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
 CDI spec directories:
  /etc/cdi
  /var/run/cdi
 Swarm: inactive
 Runtimes: runc io.containerd.runc.v2
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 1c4457e00facac03ce1d75f7b6777a7a851e5c41
 runc version: d842d7719497cc3b774fd71620278ac9e17710e0
 init version: de40ad0
 Security Options:
  seccomp
   Profile: builtin
  cgroupns
 Kernel Version: 6.17.8-orbstack-00308-g8f9c941121b1
 Operating System: OrbStack
 OSType: linux
 Architecture: x86_64
 CPUs: 6
 Total Memory: 15.67GiB
 Name: orbstack
 ID: 819a1b5c-d256-496f-b022-59266a2ad298
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Experimental: false
 Insecure Registries:
  ::1/128
  127.0.0.0/8
 Live Restore Enabled: false
 Product License: Community Engine
 Default Address Pools:
   Base: 192.168.97.0/24, Size: 24
   Base: 192.168.107.0/24, Size: 24
   Base: 192.168.117.0/24, Size: 24
   Base: 192.168.147.0/24, Size: 24
   Base: 192.168.148.0/24, Size: 24
   Base: 192.168.155.0/24, Size: 24
   Base: 192.168.156.0/24, Size: 24
   Base: 192.168.158.0/24, Size: 24
   Base: 192.168.163.0/24, Size: 24
   Base: 192.168.164.0/24, Size: 24
   Base: 192.168.165.0/24, Size: 24
   Base: 192.168.166.0/24, Size: 24
   Base: 192.168.167.0/24, Size: 24
   Base: 192.168.171.0/24, Size: 24
   Base: 192.168.172.0/24, Size: 24
   Base: 192.168.181.0/24, Size: 24
   Base: 192.168.183.0/24, Size: 24
   Base: 192.168.186.0/24, Size: 24
   Base: 192.168.207.0/24, Size: 24
   Base: 192.168.214.0/24, Size: 24
   Base: 192.168.215.0/24, Size: 24
   Base: 192.168.216.0/24, Size: 24
   Base: 192.168.223.0/24, Size: 24
   Base: 192.168.227.0/24, Size: 24
   Base: 192.168.228.0/24, Size: 24
   Base: 192.168.229.0/24, Size: 24
   Base: 192.168.237.0/24, Size: 24
   Base: 192.168.239.0/24, Size: 24
   Base: 192.168.242.0/24, Size: 24
   Base: 192.168.247.0/24, Size: 24
   Base: fd07:b51a:cc66:d000::/56, Size: 64

WARNING: DOCKER_INSECURE_NO_IPTABLES_RAW is set
```
<img width="987" height="1398" alt="Screenshot 2026-04-01 at 6 21 04 PM" src="https://github.com/user-attachments/assets/6885d8a1-3ee2-47ab-be92-623ffbf603d8" />

4. Docker 기본 운영 명령 수행
도커 이미지 목록 확인
```
flexaveler5737@c3r1s5 ~ % docker images
REPOSITORY               TAG       IMAGE ID       CREATED       SIZE
docker/getting-started   latest    3e4394f6b72f   3 years ago   47MB
```
도커 실행상태 확인(ps : 현재 실행중인 이미지 확인, ps -a : 실행 중지된 이미지까지 확인) 
```
flexaveler5737@c3r1s5 ~ % docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
flexaveler5737@c3r1s5 ~ % docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS                     PORTS     NAMES
f407b776f14f   docker/getting-started   "/docker-entrypoint.…"   10 minutes ago   Exited (0) 9 minutes ago             unruffled_chandrasekhar
```
로그 확인
```
flexaveler5737@c3r1s5 ~ % docker logs docker/getting-started
Error response from daemon: No such container: docker/getting-started
flexaveler5737@c3r1s5 ~ % docker logs f407b776f14f          
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2026/04/01 09:18:14 [notice] 1#1: using the "epoll" event method
2026/04/01 09:18:14 [notice] 1#1: nginx/1.23.3
2026/04/01 09:18:14 [notice] 1#1: built by gcc 12.2.1 20220924 (Alpine 12.2.1_git20220924-r4) 
2026/04/01 09:18:14 [notice] 1#1: OS: Linux 6.17.8-orbstack-00308-g8f9c941121b1
2026/04/01 09:18:14 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 20480:1048576
2026/04/01 09:18:14 [notice] 1#1: start worker processes
2026/04/01 09:18:14 [notice] 1#1: start worker process 30
2026/04/01 09:18:14 [notice] 1#1: start worker process 31
2026/04/01 09:18:14 [notice] 1#1: start worker process 32
2026/04/01 09:18:14 [notice] 1#1: start worker process 33
2026/04/01 09:18:14 [notice] 1#1: start worker process 34
2026/04/01 09:18:14 [notice] 1#1: start worker process 35
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET / HTTP/1.1" 200 8702 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /tutorial/ HTTP/1.1" 200 14807 "http://localhost/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /assets/stylesheets/application.adb8469c.css HTTP/1.1" 200 76332 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /assets/stylesheets/application-palette.a8b3c06d.css HTTP/1.1" 200 38773 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /assets/fonts/font-awesome.css HTTP/1.1" 200 30721 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /assets/javascripts/modernizr.86422ebf.js HTTP/1.1" 200 7296 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /css/styles.css HTTP/1.1" 200 892 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /assets/fonts/material-icons.css HTTP/1.1" 200 873 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /css/dark-mode.css HTTP/1.1" 200 6520 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /assets/javascripts/application.c33a9706.js HTTP/1.1" 200 79589 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /images/docker-labs-logo.svg HTTP/1.1" 200 6469 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /tutorial/tutorial-in-dashboard.png HTTP/1.1" 200 109800 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /fonts/hinted-Geomanist-Book.ttf HTTP/1.1" 200 73568 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /assets/fonts/specimen/MaterialIcons-Regular.woff2 HTTP/1.1" 200 44300 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:22 +0000] "GET /assets/fonts/specimen/FontAwesome.woff2 HTTP/1.1" 200 77160 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
192.168.215.1 - - [01/Apr/2026:09:18:23 +0000] "GET /assets/images/favicon.png HTTP/1.1" 200 521 "http://localhost/tutorial/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/18.6 Safari/605.1.15" "-"
^C2026/04/01 09:18:45 [notice] 35#35: signal 2 (SIGINT) received, exiting
2026/04/01 09:18:45 [notice] 34#34: signal 2 (SIGINT) received, exiting
2026/04/01 09:18:45 [notice] 35#35: exiting
2026/04/01 09:18:45 [notice] 34#34: exiting
2026/04/01 09:18:45 [notice] 1#1: signal 2 (SIGINT) received, exiting
2026/04/01 09:18:45 [notice] 33#33: signal 2 (SIGINT) received, exiting
2026/04/01 09:18:45 [notice] 33#33: exiting
2026/04/01 09:18:45 [notice] 32#32: signal 2 (SIGINT) received, exiting
2026/04/01 09:18:45 [notice] 30#30: signal 2 (SIGINT) received, exiting
2026/04/01 09:18:45 [notice] 32#32: exiting
2026/04/01 09:18:45 [notice] 30#30: exiting
2026/04/01 09:18:45 [notice] 35#35: exit
2026/04/01 09:18:45 [notice] 34#34: exit
2026/04/01 09:18:45 [notice] 33#33: exit
2026/04/01 09:18:45 [notice] 30#30: exit
2026/04/01 09:18:45 [notice] 32#32: exit
2026/04/01 09:18:45 [notice] 31#31: signal 2 (SIGINT) received, exiting
2026/04/01 09:18:45 [notice] 31#31: exiting
2026/04/01 09:18:45 [notice] 31#31: exit
2026/04/01 09:18:45 [notice] 1#1: signal 17 (SIGCHLD) received from 35
2026/04/01 09:18:45 [notice] 1#1: worker process 34 exited with code 0
2026/04/01 09:18:45 [notice] 1#1: worker process 35 exited with code 0
2026/04/01 09:18:45 [notice] 1#1: signal 29 (SIGIO) received
2026/04/01 09:18:45 [notice] 1#1: signal 17 (SIGCHLD) received from 31
2026/04/01 09:18:45 [notice] 1#1: worker process 31 exited with code 0
2026/04/01 09:18:45 [notice] 1#1: signal 29 (SIGIO) received
2026/04/01 09:18:45 [notice] 1#1: signal 17 (SIGCHLD) received from 30
2026/04/01 09:18:45 [notice] 1#1: worker process 30 exited with code 0
2026/04/01 09:18:45 [notice] 1#1: worker process 32 exited with code 0
2026/04/01 09:18:45 [notice] 1#1: signal 17 (SIGCHLD) received from 32
2026/04/01 09:18:45 [notice] 1#1: worker process 33 exited with code 0
2026/04/01 09:18:45 [notice] 1#1: exit
```
리소스 확인
```
flexaveler5737@c3r1s5 ~ % docker stats
CONTAINER ID   NAME                 CPU %     MEM USAGE / LIMIT   MEM %     NET I/O   BLOCK I/O   PIDS 
7af8d2bbab05   affectionate_curie   --        -- / --             --        --        --          -- 
```
<img width="1593" height="426" alt="Screenshot 2026-04-01 at 6 33 32 PM" src="https://github.com/user-attachments/assets/3b0105ee-f93e-4581-a609-63e6a4856dc5" />
   
6. 컨테이너 실행 실습
- hello world 실행 성공
<img width="1343" height="396" alt="Screenshot 2026-04-01 at 8 08 46 PM" src="https://github.com/user-attachments/assets/e466e06b-c682-4335-9742-27454ddf8278" />


   - docker attach는 실행 중인 컨테이너의 메인 프로세스와 터미널을 연결하여 로그 확인, 실행 결과 모니터링 등이 가능하다.
   - docker run은 컨테이너를 생성하고 실행하는 명령어이다.
   - docker exec 명령어는 이미 실행된 컨테이너의 환경의 디버깅 하는 용도로 사용한다.(이 명령어로 실행된 컨테이너는 프로세스로 취급하지 않아 로그 확인이나 프로세스 완료여부를 확인하기 어렵다)
 <img width="581" height="738" alt="Screenshot 2026-04-01 at 6 52 53 PM" src="https://github.com/user-attachments/assets/1d7badd8-5727-4415-96d9-49b93a99989e" />
 
8. 기존 Dockerfile 기반 커스텀 이미지 제작
   커스텀 포인트
- ubuntu 22.04 버전을 도커 이미지 베이스로 사용했다.
- ubuntu 다운로드 도구를 최신으로 업데이트 해주고, python, curl 패키지가 설치되게 하였다.
- 환경변수를 설정해 주었다(유저이름, 포트)
- 작업 디렉토리를 설정해주었다.
- localhost:8080/health api에 접근 할 때 'OK'라는 응답을 보내주는 간단한 파이썬 웹서버가 실행된다.
- 헬스 체크 추가 (30초 간격으로, 10초이내 응답해야하고, 시작 후 5초 대기하고, 실패시 3번 재시도한다)
- 포트 8080 노출

Dockerfile
```
FROM ubuntu:22.04

LABEL description="Custom Ubuntu with Python web server"

# 패키지 설치
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    curl \
    && rm -rf /var/lib/apt/lists/*

# 환경 변수
ENV APP_HOME=/app
ENV APP_USER=flexaveler
ENV PORT=8080

# 사용자 생성
RUN useradd -m -u 1000 ${APP_USER}

# 작업 디렉토리
RUN mkdir -p ${APP_HOME} && chown -R ${APP_USER}:${APP_USER} ${APP_HOME}
WORKDIR ${APP_HOME}

# 간단한 Python 웹 서버 스크립트 생성
RUN echo 'from http.server import HTTPServer, SimpleHTTPRequestHandler\n\
class Handler(SimpleHTTPRequestHandler):\n\
    def do_GET(self):\n\
        if self.path == "/health":\n\
            self.send_response(200)\n\
            self.send_header("Content-type", "text/plain")\n\
            self.end_headers()\n\
            self.wfile.write(b"OK")\n\
        else:\n\
            super().do_GET()\n\
\n\
if __name__ == "__main__":\n\
    server = HTTPServer(("0.0.0.0", 8080), Handler)\n\
    print("Server running on port 8080")\n\
    server.serve_forever()' > server.py

# 헬스체크
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD curl -f http://localhost:8080/health || exit 1

# 포트 노출
EXPOSE 8080

# 사용자 전환
USER ${APP_USER}

# 실행 명령어
CMD ["python3", "server.py"]
```
도커 빌드 및 실행 후 결과 출력 명령어
```
docker build -t web-ubuntu:1.0 .
docker run -d -p 8080:8080 web-ubuntu:1.0
curl http://localhost:8080/health    # OK 응답
```

<img width="583" height="369" alt="Screenshot 2026-04-01 at 7 33 30 PM" src="https://github.com/user-attachments/assets/52b63a00-dbd2-4548-8480-b85b51b7df2e" />

10. 포트 매핑 및 접속 증거
<img width="1313" height="157" alt="Screenshot 2026-04-01 at 7 36 07 PM" src="https://github.com/user-attachments/assets/6a9c070f-fbc1-4258-9760-a22e8164d5e4" />

  
12. Docker 볼륨 영속성 검증

```
# 호스트에서 실행
mkdir -p ~/docker-test/data
cd ~/docker-test/data

# 초기 파일 생성
echo "Initial data from host" > host-file.txt
cat host-file.txt

# -v 옵션으로 호스트 경로와 컨테이너 경로 연결
docker run -it \
  -v ~/docker-test/data:/app/data \
  --name test-container \
  ubuntu:22.04 /bin/bash

-v ~/docker-test/data:/app/data
~/docker-test/data : 호스트 경로
/app/data : 컨테이너 경로
양쪽이 실시간으로 동기화됨

# 컨테이너 내에서 실행
cd /app/data
ls -la
cat host-file.txt          # "Initial data from host" 출력
```
실행결과
```
root@415320873e09:/# cd /app/data/
root@415320873e09:/app/data# ls -la
total 4
drwxr-xr-x 1 root root 96 Apr  1 10:49 .
drwxr-xr-x 1 root root  8 Apr  1 10:54 ..
-rw-r--r-- 1 root root 23 Apr  1 10:49 host-file.txt
root@415320873e09:/app/data# cat host-file.txt 
Initial data from host
root@415320873e09:/app/data# exit
exit
flexaveler5737@c3r1s5 dockerpractice % docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
flexaveler5737@c3r1s5 dockerpractice % docker container ls
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
flexaveler5737@c3r1s5 dockerpractice % docker container ls -al
CONTAINER ID   IMAGE          COMMAND       CREATED         STATUS                      PORTS     NAMES
415320873e09   ubuntu:22.04   "/bin/bash"   2 minutes ago   Exited (0) 21 seconds ago             container-1
flexaveler5737@c3r1s5 dockerpractice % docker rm container-1
container-1
flexaveler5737@c3r1s5 dockerpractice % cd ~/docker-test/data
flexaveler5737@c3r1s5 data % ls -al
total 8
drwxr-xr-x  3 flexaveler5737  flexaveler5737  96 Apr  1 19:49 .
drwxr-xr-x  3 flexaveler5737  flexaveler5737  96 Apr  1 19:48 ..
-rw-r--r--  1 flexaveler5737  flexaveler5737  23 Apr  1 19:49 host-file.txt

```


14. Git 설정 및 GitHub 연동
   <img width="1894" height="903" alt="image" src="https://github.com/user-attachments/assets/d28a32a2-aba1-4a6e-83e0-230f35d28f3f" />


## 보너스 과제
1. Docker Compose 기초
- docker-compose.yml의 기본 구조를 학습하고, 단일 서비스를 Compose로 실행한다.
- 배움 포인트: 컨테이너 실행 명령이 “문서화된 실행 설정”으로 바뀌는 이유
2. Docker Compose 멀티 컨테이너
- 웹 서버 + (임의의 보조 서비스) 2개 이상을 Compose로 함께 실행한다.
- 컨테이너 간 네트워크 통신이 가능한지 확인한다.
- 배움 포인트: 네트워크/서비스 디스커버리 개념 맛보기
3. Compose 운영 명령어 습득
- up, down, ps, logs를 사용해 실행/종료/상태/로그를 관리한다.
- 배움 포인트: 운영 관점의 “상태 확인 루틴” 만들기
4. 환경 변수 활용
- Dockerfile 또는 Compose에서 환경 변수를 주입해 서버 포트/모드를 바꿔본다.
- 배움 포인트: 설정과 코드의 분리
5. GitHub SSH 키 설정
- HTTPS 대신 SSH로 푸시가 가능하도록 키를 등록하고 동작을 확인한다.
- 배움 포인트: 인증 방식 차이와 보안 습관

