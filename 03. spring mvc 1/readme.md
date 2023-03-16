## MVC1     
`MVC패턴을 만들어 가는 과정을 보기위해 한 페이지에 작성되었다.`     
`MVC패턴을 공부한 것을 정리하기 위한 글이기 때문에 http요청, 응답같은 것들은 크게 다루지 않는다.`      

1. 서블릿       
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

    - @WebServlet 애노테이션을 이용해서 서블릿을 사용할 수 있다.      
    urlPatterns: Url매핑 정보 (해당 코드에선 /hello로 접속하면 이 서블릿이 호출된다.)     
    - *서블릿으로 html 응답하기*      
    서블릿으로 html을 응답하기 위해선 httpServletResponse를 이용하여 직접 html코드를 작성해야 한다.         



2. JSP      
3. mvc패턴      