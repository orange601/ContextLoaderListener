# ContextLoaderListener
ContextLoaderListener란?

- Spring에서 DispatcherServlet은 그 자체로서 서블릿 이기 때문에 1개 이상의 DispatcherServlet을 설정하는 것이가능하다. 

만약 web.xml 에서 아래와 같이 DispatcherServlet을 설정하였다면 두개의 독릭적으로 작동하는 컨텍스트가
생성되게 된다.

````xml
<servlet>
  <servlet-name>aServlet</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/context/applicationContext_a.xml</param-value>
  </init-param>
</servlet>
````

````xml
<servlet>
  <servlet-name>bServlet</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/context/applicationContext_b.xml</param-value>
  </init-param>
</servlet>
````

- 이 경우 두 DispatcherServlet은 각각 별도의 WebApplicationContext를 생성하게 된다. 

이 때 두 컨텍스트는 독립적이므로 

**aServlet**는 applicationContext_a.xml의 설정파일로 컨텍스트가 생성되고,

**bServlet**는  applicationContext_b.xml 설정파일을 이용해서 컨텍스트가 생성되는데

aServlet에서는 applicationContext_b.xml 설정파일을 이용해서 생성된 빈객체를 사용할수가 없다.

bServlet에서도 마찬가로 applicationContext_a.xml 설정파일을 이용해서 생성된 빈객체를 사용할수가 없다.

이때 만약 aServlet 와 bServlet 에서 동시에 필요한 의존성(공통빈)이 있어야 하는경우

ContextLoaderListener를 사용하여 공통으로 사용된 빈을 설정할수 있게 된다.

다음과 같이 설정한다.
````xml
<context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>/WEB-INF/applicationContext_service.xml
               /WEB-INF/applicationContext_dao.xml
 </param-value>
</context-param>

<listener>
  <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
````


- ContextLoaderListener 와 DispatcherServlet은 각각 WebApplicationContext 인스턴스를 생성하게 되는데,
ContextLoaderListener 가 생성한 컨텍스트가 root컨텍스트가 되고, DispatcherServlet이 생성한 인스턴스는
root컨텍스트를 부모로 사용하는 자식 컨텍스트가 된다.

이때 자식컨텍스트들은 root컨텍스트가 제공하는 빈을 사용할 수 있기 때문에 ContextLoaderListener를 이용하여 공통빈을 설정하는 것이다.

참고로...
ContextLoaderListener는 ContextConfigLocation을 명시하지 않으면 /WEB-INF/applicationContext.xml을 설정파일로 사용한다.
이때, 설정파일이 존재하지 않으면 에러가 발생하니 주의해야 한다.

http://blog.naver.com/PostView.nhn?blogId=minis24&logNo=80097770192
