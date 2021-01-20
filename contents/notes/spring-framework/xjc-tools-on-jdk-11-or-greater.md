## 1\. 문제점

스프링 프레임워크를 좀더 자세히 공부하기 위해 [토비의 스프링 3.1](https://ridibooks.com/books/3984000001)을 정독하고 있다. 책이 출판된 지 시간이 좀 흘러서 현재 스프링 프레임워크 5버전대와는 호환이 안 되는 내용이 종종 존재한다.

몇몇 내용은 가볍게 검색하고 해결하여 지나갈 수 있었으나, '7.2 인터페이스의 분리와 자기참조 빈'에서 SQL문을 DAO 코드로부터 분리해 XML에 보존하는 내용에 문제가 발생했다. 문제는 바로 `JAXB(Java Architecture for XML Binding)`가 **JDK 11부터 제공되지 않으면서** `xjc`를 이용해 XML 스키마 파일을 자바 클래스로 컴파일하는 것이 어려워졌다.

특히나 나의 경우, Maven이나 Gradle 같은 빌드 툴을 사용하지 않고 공부를 진행하고 있어서 더욱이 해결책을 찾기 어려웠다. 혹시 나와 같은 경우가 있다면, 이 글이 다른 사람에게도 해결책이 되기를 바래 본다.

## 2\. 해결방법

`xjc`로 컴파일을 하기 위해 해결해야 되는 것은 다음과 같다.

### 1\. 프로젝트 내에 필요한 의존성 추가

-   먼저 프로젝트 내에 필요한 의존성을 추가해야 한다. **버전은 임의 기준이고 다른 버전은 테스트해보지 않았으므로 주의.**
    -   org.glassfish.jaxb:jaxb-runtime:2.3.22 (프로젝트 내에 컴파일된 클래스를 위해 필요)
    -   javax.activation:activation:1.1.1 (컴파일할 때 필요; 사실 프로젝트 자체엔 필요 없음)
-   `jar` 파일 다운로드는 Maven Repo 등을 참고하시길 바란다.

### 2\. `JAXB` 스탠드얼론 배포 버전 다운로드

-   `xjc` 도구를 사용하기 위해서 스탠드얼론 배포 버전을 다운로드 받도록 한다. 다음 깃헙 리포지터리([https://github.com/javaee/jaxb-v2](https://github.com/javaee/jaxb-v2)) 화면의 제일 하단에 있는 링크 가운데 **Download standalone distribution**를 받도록 한다.
    
    [##_Image|kage@AhfvB/btqMRlK8MjF/vuQoVmqOIKC3UUoAgDwE8k/img.png|alignCenter|width="100%" data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]
-   압축을 풀면 내부에 `bin` 폴더와 `lib` 폴더 등이 존재한다. `bin` 폴더에는 `xjc.sh`, `xjc.bat`이 존재하는데 이걸 수정해야 한다. (본 글은 유닉스 기준으로 변경하였으나 윈도우인 분은 하단에 참조 항목 중 'Running JAXB xjc Compiler With OpenJDK 11'을 참조)
    
    ```
       # ... (생략)
    
    # [삭제] exec "$JAVA" $XJC_OPTS -jar "$JAXB_HOME/lib/jaxb-xjc.jar" "$@"
    exec "$JAVA" -cp "/.../lib/activation-1.1.1.jar:/.../jaxb-ri/lib/jaxb-xjc.jar" com.sun.tools.xjc.XJCFacade "$@"
    
    ```
    
-   `/.../lib/activation-1.1.1.jar`: 1번에서 프로젝트에 추가한 의존성의 경로
    
-   `/.../jaxb-ri/lib/jaxb-xjc.jar`: 스탠드얼론 버전을 받으면 `lib`에 존재하는 `jar`의 경로
    
-   **유닉스라면 반드시 두 `jar`는 `:`을 구분자로 사용해야 한다!!**
    

### 3\. 스탠드얼론 배포 버전을 이용해 컴파일

-   1, 2번 과정을 마쳤으면 이제 실행만 하면 된다.
-   먼저 터미널을 이용해 컴파일을 수행할 프로젝트의 루트 디렉터리로 이동한다. 그리고 다음 명령어를 실행한다.
    
    ```
       $ /.../jaxb-ri/bin/xjc.sh -p this.is.project.package sqlmap.xsd -d src
    ```
    
-   `/.../jaxb-ri/bin/xjc.sh`: 스탠드얼론 버전을 받아서 압축 풀었을 때 내부에 존재하던 `bin`의 스크립트 경로
-   `-p <패키지 경로>`: 컴파일할 대상 패키지를 지정한다.
-   `-d <저장 디렉터리>`: 생성된 파일이 저장될 디렉터리를 지정한다.

## 3\. 참조

-   [Running JAXB xjc Compiler With OpenJDK 11](https://dzone.com/articles/running-jaxb-xjc-compiler-with-openjdk-11)
-   [Java -classpath 옵션](https://aircook.tistory.com/entry/Java-classpath-%EC%98%B5%EC%85%98)
-   [JAXB](https://github.com/javaee/jaxb-v2)
