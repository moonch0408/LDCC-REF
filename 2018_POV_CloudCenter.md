### Cloud Center

-----

> #####기능

1. 멀티 클라우드 환경 제공 
2. 서비스 모델링 툴 제공
3. API 제공 (확장성)
4. 클라우드 환경에 어플리케이션까지 배포 가능
5.  웹 브라우저를 통한 VM 콘솔 접근
6. Scale In/Out 기능 제공

> #####장점

1. 커스터마이징으로 API 혹은 개발 라이브러리 제공 시 어떤 기능이든 사용 가능
2. 모델링 된 서비스를 템플릿 형태로 제공 가능
3. SSO or LDAP 연동 관리 가능

> #####단점

1. 추가되는 서비스에 대한 개발 필요 (높은 이해도 요구)

2. 직관적이지 않은 UI 

3. 미터링 기능을 제공하지 않고 빌링에 대한 명확한 정의가 없음

    

**[POV 시나리오]**

> APIC 과 연동하여 서비스 모델링 및 배포 

- Demo Full Stack

![pov_concept](https://github.com/moonch0408/typora-image/blob/master/CloudCenter_pov_con.PNG?raw=true)

- Demo 모델링

![con_2](https://github.com/moonch0408/typora-image/blob/master/CloudCenter_pov_con2.PNG?raw=true)

- 사전 설정

  - Admin 사용자 환경에서 Demo 구성

  - 사용 환경 설정 및 User 생성

  - Repository 등록 ( Bundle Store 체크)

  - APIC에서 사용할 서비스에 대해 Python으로 Controller 개발 (생략 가능)

    자체 모듈이기 때문에 프로그래밍 언어 제제없음

  - Service 가 정의 된 쉘프로그래밍 파일 Repository에 업로드

    .zip 파일 형태로 업로드  

    (Service 등록 시 생성 파일 참고사항)   

    ​	apic_epg > service.sh 파일 생성

    ​	apic_epg.zip 파일 생성

1. 사용할 서비스에 대해 Service에 등록한다.

   - Service에서 사용할 Parameter 입력

   - Service ID는 사용할 zip 파일명과 동일

   - Service Life-Cycle 은 실행시킬 [ {파일명} {명령어}] (service start)형태로 입력

     **service.sh**

     ```sh
     #!/bin/bash
     
     ./utils.sh
     cmd=$1
     serviceStatus=""
     
     case $cmd in
     	start)
         serviceStatus="Starting"
     	RESULT=`curl -XGET http://ip:port/apic/create/bd/$APIC_TENANT/$APIC_BD`
     	print_log "$RESULT"
     	serviceStatus="Started"
         ;;
         stop)
     	serviceStatus="Stopping"
         RESULT=`curl -XGET http://ip:port/apic/delete/bd/$APIC_TENANT/$APIC_BD`
         print_log "$RESULT"
         serviceStatus="Stopped"
         ;;
         *)
         serviceStatus="No Valid Script Argument"
         exit 127
         ;;
     esac
     ```

2. Application 모델링을 한다.

   - 기존 Service와 등록한 Custom 서비스를 이용해서 모델링

3. 모델링한 Application을 등록한다.

4. Management 메뉴에서 등록한 Application을 확인 할 수 있다.

5. 등록된 Application을 Deploy 한다.

   - 기존 서비스에 등록된 Parameter 값
   - 모델링 시 Global 변수로 지정된 Parameter 값
   - Deploy 시 사용할 Cloud 환경 선택 

6. Deploy 상태를 확인 한다.

   

**[알아두면 유용한 사항]**

1. Admin > Usage & Fee, Activation Profile : 사용환경 설정
2. User 생성 시 > Activate User > Profile 설정 가능
3. Tenant Information : 계정 or 일반적인 정책 설정 가능 (ex)pwd 보안정책)
4. Tenant ID 는 Unique 하다
5. Repositories 설정에서 Bundle Store 옵션 선택시 ZIP파일을 UNZIP해서 명령어 사용 가능 
6. Service 구성 시 모델링에 따른 Parameter 타입 고려 (Global, Service, Deploy?)
7. Service 추가 시 Service ID는 ZIP파일 name과 동일하다 
8. Autoscale을 위한 미터링만 제공. 실질적인 미터링 데이터는 저장되지 않음(확인필요사항)

**[추후 추가 확인 사항]**

1. Deploy 시 스크립트에 대한 에러 메세지가 자세히 표시되지 않음(전체 로그 파일로 제공)

2. 로그에 대한 내용 Kibana로 분석 고려

3. NFV 제공 방안에 대한 검토

4. UCSD 사용시 활용 방안 검토

5. Deploy 후 관리 : SSH, 원격 등 사용 방안

   웹UI 제공 서비스의 경우 UI를 통한 서비스 사용 관리 방안

6. 미터링 데이터 수집 방식 및 빌링 방안 확인

7. 외부 모니터링(미터링)이용시 데이터 차이 확인 