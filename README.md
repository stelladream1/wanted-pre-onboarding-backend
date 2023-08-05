# wanted-pre-onboarding-backend

## *1. 지원자의 성명*

안녕하세요 원티드 프리온보딩 8기 지원자 김현 입니다. 

<hr/>

## *2. 애플리케이션의 실행 방법(엔드포인트 호출 방법 포함)*

- #### 애플리케이션 실행 방법

1. 깃허브에서 프로젝트를 다운로드 합니다.  
   `git clone https://github.com/stelladream1/wanted-pre-onboarding-backend.git`

2. 데이터베이스와 연결하기 위한 환경 변수를 _**application.yml**_ 파일에서 설정합니다.

   ```
   server:
    port: 8080
   
    spring:
    
      datasource:
        driver-class-name: com.mysql.cj.jdbc.Driver
    
        url: <데이터 베이스 연결 URL>
    
        username: <아이디>
        password: <비밀번호>
    
      jpa:
          database-platform: org.hibernate.dialect.MySQL5InnoDBDialect
          open-in-view: false
          show-sql: true
          hibernate:
            ddl-auto: update
     
    jwt:
      secret: <JWT를 생성하기 위한 secret key>
   ```

3. 프로젝트를 빌드합니다.
   ` ./gradlew build -x test `

4. 애플리케이션 실행합니다.

   `java -jar build/libs/member-0.0.1-SNAPSHOT.jar `          
   **Warning**:  /build/libs 폴더에 프로젝트를 빌드한 jar 파일이 있는 지 확인해야 합니다.

- #### 앤드포인트 호출 방법

배포 주소: http://15.164.50.0:8080
              
| 기능                   | HTTP METHOD | URL                       |
|-----------------------|-------------|---------------------------|
| 회원가입               | POST        | /api/user/join            |
| 로그인                 | POST        | /api/user/login           |
| 게시글 작성하기        | POST        | /api/board/post           |
| 게시글 목록 조회하기   | GET         | /api/board/list?page={pageNum}    |
| 특정 게시글 조회하기   | GET         | /api/board/list/{id}      |
| 특정 게시글 수정하기   | PUT         | /api/board/update/{id}    |
| 특정 게시글 삭제하기   | DELETE      | /api/board/delete/{id}    |
 
   
<hr/>

## *3. 데이터베이스 테이블 구조*

![스크린샷 2023-08-03 191628](https://github.com/stelladream1/wanted-pre-onboarding-backend/assets/74993171/ffd57e5f-3e9a-4a64-85ae-29ab29d95557)

 
<hr />

## *4. 구현한 API의 동작을 촬영한 데모 영상 링크*
<hr />

## *5. 구현 방법 및 이유에 대한 간략한 설명*

- MVC 패턴을 사용하여 각각의 역할을 분리시켜 유연성과 유지보수성을 높였습니다.
- JPA 인터페이스를 사용하여 간편하게 데이터베이스의 CRUD 작업을 구현하였습니다. 

*1. 회원가입 엔드포인트*
   - BCrypt 함수를 사용하여 비밀번호를 암호화, 복호화하였습니다. 
   - 단방향 함수로 salt를 사용하여 해시의 다양성을 증가시켜 더욱 안전하게 비밀번호를 저장할 수 있기에 사용하였습니다.

*2.로그인 엔드포인트*  
   - 로그인 시 BCrpy 함수로 비밀번호 일치 여부를 판단한 후 주어진 이메일 정보를 바탕으로 JWT를 생성하고 유효기간을 지정하였습니다.        
   - io.jsonwebtoken 라이브러리를 사용하여 JWT를 구현하였습니다.       
   - 쉽고 효율적으로 JWT를 생성하고 파싱할 수 있기에 사용하였습니다.

*3. 게시글 작성 엔드포인트*             
   - 유효한 JWT를 Authorization 헤더에 포함하여 보낼 경우에만 게시글 작성이 가능합니다.

*4. 게시글 목록 조회 엔드포인트*                 
   - JPA를 기반으로 하는 Pageable을 활용한 Pagination을 구현하였습니다.         
   - 쉽고 간편하게 원하는 페이지에 접근할 수 있고 정렬 방식을 지정하여 조회할 수 있기에 사용하였습니다.

*5. 특정 게시글 수정, 삭제 엔드포인트*               
   - 유효한 JWT를 Authorization 헤더에 포함하여 보낼 경우에만 게시글 수정, 삭제가 가능합니다.      
   - JWT에서 이메일 정보를 추출하여 DB에 저장된 작성자와 일치할 경우에만 수정, 삭제가 가능합니다.                

<hr />         


## *6. API 명세(request/response 포함)*

### 1. 회원가입
> Request
```
POST  http://15.164.50.0:8080/api/user/join
Body
   {
       "email": "testuser@wanted.com",
       "password": "12341234"
   }
```
> Response
```
{
   CODE 201: 회원가입이 정상적으로 처리 되었습니다.
}
```
> Error Code
```
400: 이메일 또는 비밀번호 미입력, 올바르지 않은 이메일 형식, 비밀번호 8자리 미만
500: 예상치못한 서버 오류 
```
### 2. 로그인
> Request
```
POST  http://15.164.50.0:8080/api/user/login
Body
   {
       "email": "testuser@wanted.com",
       "password": "12341234"
   }
```
> Response
```
{
   ...AcessJWT...
}
```
> Error Code
```
400: 이메일 또는 비밀번호 미입력, 올바르지 않은 이메일 형식, 비밀번호 8자리 미만
401: 등록되지 않은 유저
500: 예상치못한 서버 오류 
```
### 3. 게시글 작성
> Request
```
POST  http://15.164.50.0:8080/api/board/post

Headers
   {
      "Authorization": "Bearer ...AccessJWT..." 
   }

Body
{
    "title":"제목 test입니다.",
    "content":"내용 test입니다."
}
```
> Response
```
{
    "post": {
        "id": [게시글 번호],
        "title": "제목 test입니다.",
        "content": "내용 test입니다.",
        "email": "testuser@wanted.com"
    },
    "message": "CODE 201: 게시글이 성공적으로 등록되었습니다"
}
```
> Error Code
```
400: 제목 또는 내용 미입력 
401: 존재하지않은 사용자의 토큰으로 접근 
500: 예상치못한 서버 오류 
```
### 4. 게시글 조회
> Request
```
POST  http://15.164.50.0:8080/api/board/list?page={pageNum}
```
> Response
```
{
    "startPage": 시작 페이지,
    "pageNumber": 현재 페이지,
    "List": [
        {
            "id": 11,
            "title": "제목 test입니다.",
            "content": "내용 test입니다.",
            "email": "testuser@wanted.com"
        },
        {
            "id": 10,
            "title": "제목 test2입니다.",
            "content": "내용 test2입니다.",
            "email": "user@wanted.com"
        }
    ],
    "endPage": 마지막 페이지,
    "message": "CODE 200: 성공적으로 글 목록을 조회했습니다."
}
```
> Error Code
```
404: 게시글 목록이 존재하지 않음  
500: 예상치못한 서버 오류 
```
### 5. 특정 게시글 조회
> Request
```
POST  http://15.164.50.0:8080/api/board/list/{id}
```
> Response
```
{
    "message": "CODE 200: 게시글을 성공적으로 조회하였습니다.",
    "board": {
        "id": 12,
        "title": "제목 test입니다.",
        "content": "내용 test입니다.",
        "email": "testuser@wanted.com"
    }
}
```
> Error Code
```
404: 특정 게시글이 존재하지 않음  
500: 예상치못한 서버 오류 
```
### 6. 특정 게시글 수정
> Request
```
POST  http://15.164.50.0:8080/api/board/update/{id}

Headers
   {
      "Authorization": "Bearer ...AccessJWT..." 
   }

Body
{
    "title": "수정 test입니다.",
    "content": "test입니다."
}
```
> Response
```
{
    "message": "CODE 201: 성공적으로 게시글을 수정했습니다.",
    "board": {
        "id": 12,
        "title": " 수정 test입니다 ",
        "content": "test입니다",
        "email": "testuser@wanted.com"
    }
}
```
> Error Code
```
401: 유효하지않은 토큰으로 접근
403: 게시글 작성자가 아닌데 수정 요청
404: 특정 게시글이 존재하지 않음  
500: 예상치못한 서버 오류 
```
### 7. 특정 게시글 삭제 
> Request
```
POST  http://15.164.50.0:8080/api/board/delete/{id}

Headers
   {
      "Authorization": "Bearer ...AccessJWT..." 
   }
```
> Response
```
{
    "message": "CODE 200: 성공적으로 게시글을 삭제했습니다."
}
```
> Error Code
```
401: 유효하지않은 토큰으로 접근
403: 게시글 작성자가 아닌데 삭제 요청
404: 특정 게시글이 존재하지 않음  
500: 예상치못한 서버 오류 
```

<hr />
