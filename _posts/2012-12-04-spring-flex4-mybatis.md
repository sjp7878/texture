---
layout: post
title:  "spring + flex4 + mybatis"
description: spring + flex4 + mybatis
date:   2016-10-27 23:07:44 +0530
categories: Flex
---

## 구성

* Adobe Flash Builder 4.6 Premium
* Spring 3.2 FrameWork
* Tomcat 7.0(Web Application Server)
* MyBatis 3.2 **(iBatis 2.5 이후로 아파치 재단 지원력에서는 현재의 정보와 신기술을 맞춰갈 수 없다 판단하여 구글 코드 팀으로 옮겼단다.)**
* blazeds 4.0 **(데이타 서비스 통신)**
* spring-flex-1.5 integration **(Spring 과 Flex를 통신하기 위해 직접 클래스를 만들어 써야 하는 수고가 없도록 spring project 팀들이 고맙게도 만들어 두었다. ㄱㅅㄳ~)**
* SLF4J(Simple logging Facade for Java) (로깅찍는거) 요즘 이거 많이쓴다.

이유는 : [여기참조](http://devday.tistory.com/entry/SLF4J%EC%9D%98-%EC%9E%A5%EC%A0%90)

일단 플렉스를 이클립스의 플러그인으로 깔고 톰캣도 설치되있다는 전제하에 시작.

1. 먼저 이클립스의 왼쪽 빈공간에서 마우스 오른쪽을 눌러 New > Flex Project를 선택.

![스크린샷 2020-02-01 오후 8 39 55](https://user-images.githubusercontent.com/12682121/73591543-1c612a80-4533-11ea-9a2d-2a260092ba9c.png)

2. Project name에 자기가 입력하고 싶은 프로젝트 이름을 입력. Next

![스크린샷 2020-02-01 오후 8 48 35](https://user-images.githubusercontent.com/12682121/73591620-4109d200-4534-11ea-9a1d-d466562dd2ab.png)

3. Application Server Type은 자바를 이용할것이니 Java, Use remote object access service는 blazeds를 이용할 것이니 그곳을 선택.
Java source folder 는 src로 그냥 나두어도 되는데 나는 `java_src`로 바꿔줬다.

4. Output folder 역시 기본은 bin-debug나 `WebContent`로 바꿔 주었다. **(즉 나는 컴파일된 파일을 WebContent 밑으로 바로 놓겠다는 거다)**

5. Target runtime 옆의 New 버튼을 클릭.

![스크린샷 2020-02-01 오후 8 49 32](https://user-images.githubusercontent.com/12682121/73591632-60086400-4534-11ea-875a-a776f660fbde.png)

6. Apach 폴더의 자기에 맞는(나는 7.0)버젼을 찾아 선택해 주면 아래의 항목들이 기본으로 보인다. Finish를 눌러 완료.

![스크린샷 2020-02-01 오후 8 50 20](https://user-images.githubusercontent.com/12682121/73591642-7ca49c00-4534-11ea-9be1-2e7f6e4bed5d.png)

7. 이제 톰캣을 설정하였으므로 Target runtime에서 콤보박스 화살표를 누르면 방금 설치한 이름의 톰캣이 보인다.
그걸로 바꿔주고 Next.

![스크린샷 2020-02-01 오후 8 51 09](https://user-images.githubusercontent.com/12682121/73591652-9d6cf180-4534-11ea-8646-98b13c1cfa82.png)

8. Output folder URL에 일단 로컬에서 돌리기때문에 톰캣의 기본설정 포트 8080으로 Context root가 TEST 이므로 TEST 위와 같이 한다. Finish.

![스크린샷 2020-02-01 오후 8 51 56](https://user-images.githubusercontent.com/12682121/73591660-b5dd0c00-4534-11ea-9ae2-b7517784e2eb.png)

보면 output folder를 바꿨기 때문에 bin-debug 폴더가 안보이고 `WebContent` 폴더밑으로 기본 파일들이 생긴걸 확인할 수 있다.

## Spring과 Flex 연동.

**모든 jar파일들은 합해놓은게 [여기](http://blog.naver.com/sjp007774/150153185137)에 있다.**

1. 먼저 Spring을 사용하기 위하여 [다운](http://www.springsource.org/download/community)
2. 다운받은 Spring 폴더의 libs(3.2 기준)밑의 필요한 파일들을 복사.
3. MyBatis를 사용하기 위하여 MyBatis를 [다운](http://code.google.com/p/mybatis/downloads/list?can=3&q=Product%3DMyBatis)
4. 다운 받은 MyBatis 폴더의 jar파일(mybatis-3.2.0-SNAPSHOT.jar)을 복사.
5. MyBatis와 Spring을 연동하기 위하여 필요한 jar파일을 [다운](http://code.google.com/p/mybatis/downloads/list?q=label:Product-Spring)
6. 다운받은 폴더의 jar파일(mybatis-spring-1.1.1.jar)을 복사.
7. Spring의 Aspect을 사용하기 위해(3.0 부터는 Aspectj 관련 jar파일들이 함께 있지 않다.) [다운](http://www.eclipse.org/aspectj/downloads.php)

**다른 파일들은 모두 zip으로 압축이 되있는반면 jar로 압축이 되있었다. 압축을 풀면 lib폴더가 보인다.**

8. 압축을 푼 lib 폴더 밑의 jar파일들(`aspectjrt.jar`, `aspectjtools.jar`, `aspectjweaver.jar`, `org.aspectj.matcher.jar`)을 복사.
9. Spring의 AOP alliance(namespace AOP를 사용할 수 있게 해줌)를 사용하기 위하여 [다운](http://sourceforge.net/projects/aopalliance/)
이아이는 바로 jar파일로되있었다.
10. aopalliance.jar 파일을 복사.
11. SLF4J(log4j 같은거) 를 사용하기 위하여 [다운](http://slf4j.org/download.html)
12. 다운받은 폴더의 jar파일들(slf4j-api-1.7.2.jar, slf4j-log4j12-1.7.2.jar)을 복사.
13. log4jdbc(쿼리의 ?에 바로 값을 대입하여 보여줌)를 사용하기 위하여 [다운](http://code.google.com/p/log4jdbc/downloads/list)
14. 다운받은 lib폴더의 jar파일(slf4j-api-1.6.0.jar)을 복사.
15. log4j(로깅 찍는거)를 사용하기 위하여 다운.
16. log4j-1.2.16.jar 파일을 복사
17. 지금까지 복사한 모든 파일들을 WebContent/WEB-INF/lib 폴더밑으로 다 넣는다.

![스크린샷 2020-02-01 오후 8 55 52](https://user-images.githubusercontent.com/12682121/73591698-41569d00-4535-11ea-86e9-795941f2fe35.png)

## 설정파일 수정.

#### [web.xml]

```flex
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  <display-name>TEST</display-name>

        <context-param>
                <param-name>flex.class.path</param-name>
                <param-value>/WEB-INF/flex/hotfixes,/WEB-INF/flex/jars</param-value>
        </context-param>
 
        <listener>
         <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
         </listener>
         
        <servlet>
         <servlet-name>MessageBrokerServlet</servlet-name>
         <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
         <init-param>
             <param-name>contextConfigLocation</param-name>
             <param-value>
                     /WEB-INF/configs/web-application-config.xml
                     /WEB-INF/configs/bean-remoting-destination.xml
                     /WEB-INF/configs/mybatis-config.xml
             </param-value>
         </init-param>
         <load-on-startup>1</load-on-startup>
         </servlet>

        <servlet-mapping>
                <servlet-name>MessageBrokerServlet</servlet-name>
                <url-pattern>/messagebroker/*</url-pattern>
        </servlet-mapping>

  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```
servlet-name을 `MessageBrokerServlet`으로 하였기때문에 spring에서 `MessageBrokerServlet-servlet.xml`을 찾으려 할것이다.

하지만 파일들을 다른걸로 할거기 때문에 `ContextLoaderListener`를 달아준다.

xml파일 하나에 모든걸 다 때려넣을 수도 있지만 spring에서도 권장하듯이 서비스단, 데이타단, 화면단, 보안단 등등 이렇게 분할해 놓는게 나중에 유지보수 하기에도 좋다.

나는 설정을 가지고 있는 `web-application-config.xml`, flex의 remote object의 destination과 해당 자바정보를 갖고 있는 `bean-remoting-destination.xml`, 마지막으로

MyBatis에 관련한 `mybatis-config.xml` 세가지 xml로 분류하여 WEB-INF/configs 라는 폴더 밑에 넣어두었다.

servlet-mapping의 url-pattern의 `/messagebroker/*` (flex의 AMF통신) 오는건 spring의 DispatcherServlet 클래스에서 처리하게된다.

#### [web-application-config.xml]

```
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
                           http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
                           http://www.springframework.org/schema/context 
                           http://www.springframework.org/schema/context/spring-context-3.2.xsd
                           http://www.springframework.org/schema/aop
                           http://www.springframework.org/schema/aop/spring-aop-3.2.xsd">
                           
        <!-- MessageBroker Configuration -->                       
    <bean id="widwinMessageBroker" class="org.springframework.flex.core.MessageBrokerFactoryBean"/>
 
    <bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping"
            p:mappings="/*=widwinMessageBroker"/>
        
    <bean class="org.springframework.flex.servlet.MessageBrokerHandlerAdapter"/>                                             
                           
    <!-- Properties Configuration -->
        <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
                <property name="locations">
                        <list>
                                <value>classpath*:resources/properties/**/*.properties</value>
                        </list>
                </property>
        </bean>
    
    <!-- MyBatis Configuration -->
        <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
                <property name="configLocation">
                        <value>/WEB-INF/configs/mybatis-config.xml</value>
                </property>
                <property name="dataSource" ref="dataSource" />
                <property name="mapperLocations" value="classpath*:resources/mappers/**/*.xml" />
        </bean>
        
        <bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">  
                <constructor-arg index="0" ref="sqlSessionFactoryBean" />
        </bean>
        
    <!-- DataSource Configuration -->
        <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
                <property name="driverClassName" value="${jdbc.widwin.driverClassName}"/>
                <property name="url" value="${jdbc.widwin.url}"/>
                <property name="username" value="${jdbc.widwin.username}"/>
                <property name="password" value="${jdbc.widwin.password}"/>
        </bean>

        <!-- Transaction Configuration -->
        <bean id="transactionProxy" class="org.springframework.transaction.interceptor.TransactionProxyFactoryBean" abstract="true">
                <property name="transactionManager">
                        <bean class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
                                <property name="dataSource" ref="dataSource"/>
                        </bean>
                </property>
                <property name="transactionAttributes">
                        <props>
                                <prop key="insert*">PROPAGATION_REQUIRED</prop>
                                <prop key="update*">PROPAGATION_REQUIRED</prop>
                                <prop key="*">PROPAGATION_REQUIRED,readOnly</prop>
                        </props>
                </property>
        </bean>
        
        <!-- Common Logger Configuration -->
        <aop:aspectj-autoproxy/>
        <bean id="commonLogger" class="test.widwin.common.CommonLogger"/>
</beans>
```

properties configuration에서 반드시 `classpath*`를 붙여주어야한다. 상대경로로 찾기때문에 안붙여주면 찾지 못한다.

#### [jdbc.test.properties]

```
#jdbc.widwin.driverClassName= oracle.jdbc.OracleDriver
jdbc.widwin.driverClassName=net.sf.log4jdbc.DriverSpy

#jdbc.widwin.url=jdbc:oracle:thin:@xxx.xxx.x.x:1521:TEST
jdbc.widwin.url=jdbc:log4jdbc:oracle:thin:@xxx.xxx.x.x:1521:TEST
jdbc.widwin.username=TEST
jdbc.widwin.password=test
```
slf4j를 사용하면 driverClassName과 url에 log4jdbc를 끼어넣어야 함.(xxx 를 아이피로 바꿔주면 됨)

#### [bean-remoting-destination.xml]

```
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:flex="http://www.springframework.org/schema/flex"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
                           http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
                           http://www.springframework.org/schema/context 
                           http://www.springframework.org/schema/context/spring-context-3.2.xsd
                           http://www.springframework.org/schema/flex
                           http://www.springframework.org/schema/flex/spring-flex-1.5.xsd">
                           
        <context:component-scan base-package="test.widwin.blogics.board"/>                 
         
    <bean class="test.widwin.blogics.board.BoardController">
                <flex:remoting-destination destination-id="BoardRO" message-broker="widwinMessageBroker"/>
        </bean>
 
</beans>
```

<context:component-scan... 이 있으면 <bean에 class를 설정하지 않아도 됨. destination에 message-broker 꼭 설정해줘야 함. remoting-config.xml 파일은 관리안함.

#### [mybatis-config.xml]

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>        
        <typeAliases>
                <typeAlias alias="Board" type="test.widwin.blogics.board.vo.BoardBean"/>
        </typeAliases>
        
</configuration>
```

<mappers>부분을 주석을 풀어서 관리해도 되나 web-application-config.xml 에서 mapperLocations를 ₩*로 잡아주었으므로

따로 관리하지 않아도 된다.

#### [TEST.mxml]

```flex
<?xml version="1.0" encoding="utf-8"?>
<s:Application xmlns:fx="http://ns.adobe.com/mxml/2009" 
                           xmlns:s="library://ns.adobe.com/flex/spark" 
                           xmlns:mx="library://ns.adobe.com/flex/mx" minWidth="955" minHeight="600">
        
        <fx:Script>
                <![CDATA[
                        import mx.collections.ArrayCollection;
                        import mx.controls.Alert;
                        import mx.rpc.events.FaultEvent;
                        import mx.rpc.events.ResultEvent;
                        
                        import test.widwin.modules.board.vo.BoardVO;
                        import test.widwin.utils.RemoteObjectUtil;
                        
                        private function test2():void{
                                RemoteObjectUtil.doMethod("BoardRO", "getBoardList", getBoardListRstHdlr, [1]);
                        }
                        
                        private function insertBoard():void{
                                var vo:BoardVO = new BoardVO();
                                vo.name = tiName.text;
                                vo.title = tiTitle.text;
                                vo.description = tiDescription.text;
                                vo.pass = tiPass.text;
                                RemoteObjectUtil.doMethod("BoardRO", "insertBoard", insertBoardRstHdlr, [vo]);
                        }
                        
                        private function getBoardListRstHdlr(evt:ResultEvent):void{
                                adg.dataProvider = evt.result as ArrayCollection;
                        }
                        
                        private function insertBoardRstHdlr(evt:ResultEvent):void{
                                Alert.show("등록 성공");
                        }
                        
                        private function faultHdlr(evt:FaultEvent):void{
                                Alert.show(evt.fault.message.toString());
                        }
                ]]>
        </fx:Script>
        
        <fx:Declarations>
                
        </fx:Declarations>
        
        <s:layout>
                <s:VerticalLayout/>
        </s:layout>
        
        <s:Button label="test" click="test2()"/>
        
        <mx:AdvancedDataGrid id="adg">
                <mx:columns>
                        <mx:AdvancedDataGridColumn headerText="이름" dataField="name"/>
                        <mx:AdvancedDataGridColumn headerText="제목" dataField="title"/>
                        <mx:AdvancedDataGridColumn headerText="내용" dataField="description"/>
                </mx:columns>
        </mx:AdvancedDataGrid>
        
        <s:Form id="frm" defaultButton="{btnIns}">
                <s:FormHeading label="게시판" backgroundColor="haloSilver" />
                <s:FormItem sequenceLabel="i)" label="이름:" required="true">
                        <s:TextInput id="tiName" maxChars="64" />
                </s:FormItem>
                <s:FormItem sequenceLabel="ii)" label="제목:">
                        <s:TextInput id="tiTitle" maxChars="64" />
                </s:FormItem>
                <s:FormItem sequenceLabel="iii)" label="내용:" required="true">
                        <s:TextInput id="tiDescription" maxChars="32" />
                </s:FormItem>
                <s:FormItem sequenceLabel="iv)" label="비밀번호:" required="true">
                        <s:TextInput id="tiPass" maxChars="32" displayAsPassword="true" />
                </s:FormItem>
                <s:FormItem>
                        <s:Button id="btnIns" label="등록" click="insertBoard()" />
                </s:FormItem>
        </s:Form>
</s:Application>
```

#### [BoardVO.as]

```as
package test.widwin.modules.board.vo
{

         [RemoteClass(alias="test.widwin.blogics.board.vo.BoardBean")]         
        public class BoardVO
        {
                public var board_code:int;
                public var registration_date:String;
                public var name:String;
                public var title:String;
                public var description:String;        
                public var email:String;        
                public var homepage:String;        
                public var ip:String;        
                public var pass:String;        
                public var pos:int;        
                public var hit:int;
                public var file_yn:String;        
                public var depth:int;
                public var is_delete:String;
                public var created_timestamp:Date;
                public var creator_code:String;
                public var updated_timestamp:Date;
                public var updater_code:String;
                public var total_row:int;
                public var page:int;
        }
}
```
#### [BoardController.java]

```java
package test.widwin.blogics.board;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;

import test.widwin.blogics.board.service.IBoardService;
import test.widwin.blogics.board.vo.BoardBean;

@Controller
public class BoardController {
//        final Logger logger = LoggerFactory.getLogger(BoardController.class);
        
        @Autowired
        private IBoardService iBoardService;
        
        public List<BoardBean> getBoardList(int pageView){
                return iBoardService.getBoardList(pageView);
        }
        
        public int insertBoard(BoardBean boardBean){
                return iBoardService.insertBoard(boardBean);
        }
        
}
```

#### [IBoardService.java]

```java
package test.widwin.blogics.board.service;

import java.util.List;

import test.widwin.blogics.board.vo.BoardBean;

public interface IBoardService {
        public List<BoardBean> getBoardList(int pageView);
        
        public int insertBoard(BoardBean boardBean);

}
```

#### [BoardServiceImpl.java]

```java
package test.widwin.blogics.board.service;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import test.widwin.blogics.board.dao.BoardDao;
import test.widwin.blogics.board.vo.BoardBean;

@Service("iBoardService")
public class BoardServiceImpl implements IBoardService {
        
        @Autowired
        private BoardDao boardDao;
        
        @Override
        public List<BoardBean> getBoardList(int pageView) {
                return boardDao.getBoardList(pageView);
        }

        @Override
        public int insertBoard(BoardBean boardBean) {
                return boardDao.insertBoard(boardBean);
        }
        
}
```

#### [BoardDao.java]

```java
package test.widwin.blogics.board.dao;

import java.util.List;

import org.mybatis.spring.support.SqlSessionDaoSupport;
import org.springframework.stereotype.Repository;

import test.widwin.blogics.board.vo.BoardBean;


@Repository("boardDao")
public class BoardDao extends SqlSessionDaoSupport{
        public List<BoardBean> getBoardList(int pageView){
                return getSqlSession().selectList("test.widwin.blogics.board.dao.IBoardMapper.getBoardList", pageView);
        }
        
        public int insertBoard(BoardBean boardBean){
                return getSqlSession().insert("test.widwin.blogics.board.dao.IBoardMapper.insertBoard", boardBean);
        }  
}
```

패키지 인터페이스명.메소드 이름하고 BoardMapper.xml 에서 같은 인터페이스이름으로 namespace를 정의하면 된다.

#### [IBoardMapper.java]

```java
package test.widwin.blogics.board.dao;

import java.util.List;

import test.widwin.blogics.board.vo.BoardBean;

public interface IBoardMapper {
        public List<BoardBean> getBoardList(int pageView);

        public int insertBoard(BoardBean boardBean);
        
}
```

#### [BoardMapper.xml]

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="test.widwin.blogics.board.dao.IBoardMapper">
        <select id="getBoardList" parameterType="int" resultType="Board">
                SELECT *
            FROM (SELECT BOARD.* ,ROWNUM AS RNUM ,FLOOR(((ROWNUM-1)/15)+1) AS PAGE
                     ,COUNT(*) OVER() AS TOTAL_ROW
                FROM (SELECT * 
                      FROM BOARD
                      ORDER BY BOARD_CODE DESC) BOARD)                                     
            WHERE PAGE = #{pageView}
            ORDER BY POS ASC      
        </select>
        
        <insert id="insertBoard" parameterType="Board">
                INSERT INTO BOARD(BOARD_CODE, REGISTRATION_DATE, NAME, TITLE, DESCRIPTION, PASS, IP)
                VALUES((SELECT TRIM(NVL(MAX(BOARD_CODE + 1),1)) FROM BOARD), '201212', #{name}, #{title}, #{description}, #{pass}, '192.168.0.9')
        </insert>
</mapper>
```

**namespace가 인터페이스랑 같아야한다.**

#### [log4j.xml] **log4j.properties로 관리해도 됨(log4j.xml이 가독성도 좋고 관리가 용이함)**

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">

<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">

  <appender name="stdout-appender" class="org.apache.log4j.ConsoleAppender">
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS} %5p %c{1}: %m%n"/>
    </layout>
  </appender>

  <appender name="sql-appender" class="org.apache.log4j.FileAppender">
    <param name="File" value="./logs/sql.log"/>
    <param name="Append" value="false"/>
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="-----&gt; %d{yyyy-MM-dd HH:mm:ss.SSS} &lt;%t&gt; %m%n%n"/>
    </layout>
  </appender>

  <appender name="sql-timing-appender" class="org.apache.log4j.FileAppender">
    <param name="File" value="./logs/sqltiming.log"/>
    <param name="Append" value="false"/>
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="-----&gt; %d{yyyy-MM-dd HH:mm:ss.SSS} %m%n%n"/>
    </layout>
  </appender>

  <appender name="jdbc-appender" class="org.apache.log4j.FileAppender">
    <param name="File" value="./logs/jdbc.log"/>
    <param name="Append" value="false"/>
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS} %m%n"/>
    </layout>
  </appender>

  <appender name="jdbc-connection" class="org.apache.log4j.FileAppender">
    <param name="File" value="./logs/connection.log"/>
    <param name="Append" value="false"/>
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="%d{yyyy-MM-dd HH:mm:ss.SSS} %m%n"/>
    </layout>
  </appender>

  <!-- log SQL (pre-execution) plus exceptions caused by SQL -->
  <logger name="jdbc.sqlonly" additivity="false">
    <level value="debug"/>
    <appender-ref ref="sql-appender"/>
  </logger>

  <!-- log SQL with timing information, post execution -->
  <logger name="jdbc.sqltiming" additivity="false">
    <level value="debug"/>
    <appender-ref ref="stdout-appender"/>
  </logger>

  <!-- only use the two logs below to trace ALL JDBC information,
       NOTE:  This can be very voluminous!  -->

  <!-- log all jdbc calls except ResultSet calls -->
  <logger name="jdbc.audit" additivity="false">
    <level value="fatal"/>
    <appender-ref ref="jdbc-appender"/>
  </logger>

  <!-- log the jdbc ResultSet calls -->
  <logger name="jdbc.resultset" additivity="false">
    <level value="fatal"/>
    <appender-ref ref="jdbc-appender"/>
  </logger>
  
  <!-- log connection open/close events and dump of all open connection numbers -->
  <logger name="jdbc.connection" additivity="false">
    <level value="fatal"/>
    <appender-ref ref="connection-appender"/>
  </logger>

  <!-- this log is for internal debugging of log4jdbc, itself -->
  <!-- debug logging for log4jdbc itself -->
  <logger name="log4jdbc.debug" additivity="false">
    <level value="debug"/>
    <appender-ref ref="stdout-appender"/>
  </logger>

  <!-- by default, log everything to the console with a level of WARN or higher -->
  <root>
    <level value="debug"/>
    <appender-ref ref="stdout-appender"/>
  </root>
</log4j:configuration>
```

#### [applicationContext.xml]

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:flex="http://www.springframework.org/schema/flex"
       xsi:schemaLocation="http://www.springframework.org/schema/beans 
                           http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
                           http://www.springframework.org/schema/context 
                           http://www.springframework.org/schema/context/spring-context-3.2.xsd
                           http://www.springframework.org/schema/flex
                           http://www.springframework.org/schema/flex/spring-flex-1.5.xsd">
</beans>
```

리스너를 따로 달아줬어도 applicationContext.xml은 WEB-INF/밑에 위치하여야 함.

#### [service-config.xml]

```
<?xml version="1.0" encoding="UTF-8"?>
<services-config>

    <services>
        <service-include file-path="remoting-config.xml" />
        <service-include file-path="proxy-config.xml" />
        <service-include file-path="messaging-config.xml" />     
        <default-channels>
           <channel ref="my-amf"/>
        </default-channels>        
    </services>

    <security>
        <login-command class="flex.messaging.security.TomcatLoginCommand" server="Tomcat"/>
    </security>

    <channels>

        <channel-definition id="my-amf" class="mx.messaging.channels.AMFChannel">
            <endpoint url="http://{server.name}:{server.port}/{context.root}/messagebroker/amf" class="flex.messaging.endpoints.AMFEndpoint"/>
        </channel-definition>

        <channel-definition id="my-secure-amf" class="mx.messaging.channels.SecureAMFChannel">
            <endpoint url="https://{server.name}:{server.port}/{context.root}/messagebroker/amfsecure" class="flex.messaging.endpoints.SecureAMFEndpoint"/>
            <properties>
                <add-no-cache-headers>false</add-no-cache-headers>
            </properties>
        </channel-definition>

        <channel-definition id="my-polling-amf" class="mx.messaging.channels.AMFChannel">
            <endpoint url="http://{server.name}:{server.port}/{context.root}/messagebroker/amfpolling" class="flex.messaging.endpoints.AMFEndpoint"/>
            <properties>
                <polling-enabled>true</polling-enabled>
                <polling-interval-seconds>4</polling-interval-seconds>
            </properties>
        </channel-definition>
    </channels>

    <logging>
        <target class="flex.messaging.log.ConsoleTarget" level="Error">
            <properties>
                <prefix>[BlazeDS] </prefix>
                <includeDate>false</includeDate>
                <includeTime>false</includeTime>
                <includeLevel>false</includeLevel>
                <includeCategory>false</includeCategory>
            </properties>
            <filters>
                <pattern>Endpoint.*</pattern>
                <pattern>Service.*</pattern>
                <pattern>Configuration</pattern>
            </filters>
        </target>
    </logging>

    <system>
        <redeploy>
            <enabled>false</enabled>
        </redeploy>
    </system>
</services-config>
```

<default-channels 꼭 달아주어야 함.

BoardRO destination을 못 찾을때

#### [.flexProperties]

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<flexProperties enableServiceManager="false" flexServerFeatures="4" flexServerType="8" flexWarLocation="C:/자료/교육시이트/blazeds-bin-4.0.0.14931/blazeds.war" serverContextRoot="/TEST" serverRoot="C:/testWorkspace/TEST/WebContent" serverRootURL="http://localhost:8080/TEST" toolCompile="true" useServerFlexSDK="false" version="2"/>
```
<span style="color:red;">serverContextRoot가 /WebContent로 잡혀있는데 꼭 맞는 contextRoot 바꿔줘야 함(여기서는 /TEST)</span>

[flex bug report](http://bugs.adobe.com/jira/browse/FB-20894)에 기재되있음
