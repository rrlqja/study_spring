## MVC1     
`MVC패턴을 만들어 가는 과정을 보기위해 한 페이지에 작성되었다.`     
`MVC패턴을 공부한 것을 정리하기 위한 글이기 때문에 http요청, 응답같은 것들은 크게 다루지 않는다.`      

1. <b>서블릿</b>       
> @ServletComponentScan 애노테이션을 추가해야 스프링 부트에서 서블릿을 사용할 수 있다.      

```java
@ServletComponentScan //서블릿 자동 등록
@SpringBootApplication
public class ServletApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServletApplication.class, args);
    }
}
```

- 서블릿 등록       

    ```java     
    @WebServlet(name = "helloServlet", urlPatterns = "/hello")
    public class HelloServlet extends HttpServlet {
        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            System.out.println("request = " + request);
            System.out.println("response = " + response);
        }
    }
    ```         

    - <b>@WebServlet</b> 애노테이션을 이용해서 서블릿을 사용할 수 있다.      
    urlPatterns: Url매핑 정보 (해당 코드에선 /hello로 접속하면 이 서블릿이 호출된다.)     
    - <b>서블릿으로 html 응답하기<b>      
    서블릿으로 html을 응답하기 위해선 httpServletResponse를 이용하여 직접 html코드를 작성해야 한다.         
<br/>       
    > 서블릿으로 http요청에 응답할 수 있지만 html을 손수 작성해야한다는 단점이 있다.    
    이러한 단점을 보완하기 위해 jsp를 이용해보겠다.     

<br/>

2. <b>JSP</b>      
<b>- 회원 등록 폼 jsp</b>     
    ```html     
    <%@ page contentType="text/html;charset=UTF-8" language="java" %> // jsp 문서라는 뜻
    <html>
    <body>
        <form action="/jsp/members/save.jsp" method="post">
            username: <input type="text" name="username" />
            age: <input type="text" name="age" />
            <button type="submit">전송</button>
        </form>
    </body>
    </html>
    ```     
- 회원 저장 jsp     
    ```html     
    <%@ page import="hello.servlet.domain.member.MemberRepository" %>
    <%@ page import="hello.servlet.domain.member.Member" %>
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <%
    // request, response 사용 가능
    MemberRepository memberRepository = MemberRepository.getInstance();
    System.out.println("save.jsp");
    String username = request.getParameter("username");
    int age = Integer.parseInt(request.getParameter("age"));
    Member member = new Member(username, age);
    System.out.println("member = " + member);
    memberRepository.save(member);
    %>
    <html>
    <head>
    <meta charset="UTF-8">
    </head>
    <body>
    성공
        <ul>
            <li>id=<%=member.getId()%></li>
            <li>username=<%=member.getUsername()%></li>
            <li>age=<%=member.getAge()%></li>
        </ul>
        <a href="/index.html">메인</a>
    </body>
    </html>
    ```     
    jsp를 이용해보았다. html에 추가적으로 java코드를 활용하였는데, 데이터를 조회하는 코드 등이 모두 jsp에 작성돼있다.       
<br/>       
    >  jsp가 너무 많은 역할을 한다. 코드가 복잡해지고 커지면 유지보수하는데에 큰 어려움이 있을 것 같다. 비즈니스 로직을 처리하는 부분과 화면을 보여주는 부분을 확실히 구분해야 할 필요가 있어보인다.    
    이러한 이유로 mvc 패턴이 등장하였다. mvc 패턴을 적용해 보겠다.    



3. <b>mvc패턴</b>             
    - 서블릿이나 jsp로 비즈니스 로직과 뷰 렌더링을 처리하게 되면 너무 많은 역할을 하게되고 유지보수가 어려워진다.       
    - <b>또한 비즈니스 로직과 뷰의 라이플 사이클이 다르기 때문에</b> 관리하기 좋지 않다.        
    - 이러한 부분을 보완하고자 mvc패턴이 등장하였다.    
        - 컨트롤러: http요청을 받아서 검증하고 비즈니스 로직을 실행한다. 결과값을 모델에 담아서 뷰에 전달한다.      
            > 컨트롤러에서 직접 비즈니스 로직을 수행하진 않고 서비스 계층을 별도로 만들어 처리한다.     
        - 모델: 뷰가 필요한 데이터를 담아 전달해주는 역할.      
        - 뷰: 모델에 담겨있는 데이터를 사용하여 화면을 그리는 역할.     
<br/>
    ![mvc1](https://user-images.githubusercontent.com/59528611/225895600-21e6b7e0-80ea-4512-b592-c8e647fb1594.jpeg)     
<br/>   
    - mvc패턴 적용     
        - <b>컨트롤러</b>      
        ```java     
        @WebServlet(name = "mvcMemberFormServlet", urlPatterns = "/servlet-mvc/members/new-form")
        public class MvcMemberFormServlet extends HttpServlet {

        @Override
        protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            String viewPath = "/WEB-INF/views/new-form.jsp";
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response); // 서블릿이나 jsp로 이동.(서버 내에서 다시 호출 됨)
            }
        }
        ```     
        > 컨트롤러에서 viewPath에 있는 jsp를 호출한다. dispatcher.forward()      
        - <b>뷰</b>        
        1. 회원등록 jsp     
        ```html     
        <%@ page contentType="text/html;charset=UTF-8" language="java" %>
        <html>
        <head>
            <meta charset="UTF-8">
            <title>Title</title>
        </head>
        <body>
            <!-- 상대경로 사용, [현재 URL이 속한 계층 경로 + /save] -->
            <form action="save" method="post">
            username: <input type="text" name="username" />
            age: <input type="text" name="age" />
            <button type="submit">전송</button>
            </form>
        </body>
        </html>
        ```     