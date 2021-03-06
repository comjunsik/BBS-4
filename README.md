# UserDAO.java
```java
package user;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class UserDAO {
	
	private Connection conn;
	private PreparedStatement pstmt;
	private ResultSet rs;
	
	public UserDAO() {
		try {
			String dbURL = "jdbc:mysql://localhost:3306/BBS?serverTimezone=UTC&autoReconnect=true&useSSL=false";
			String dbID = "root";
			String dbPassword = "root";
			Class.forName("com.mysql.cj.jdbc.Driver");
			conn = DriverManager.getConnection(dbURL, dbID, dbPassword);
		}catch(Exception e){
			e.printStackTrace();
		}
	}
	
	
```
**Connection** 이란 데이터베이스에 접근 가능한 객체<br>
**PreparedStatement** 는 한번 사용한 SQL문이 저장이 된다.<br>
try catch문을 사용하여 e.printStackTrace()로 오류가 무엇인지 확인<br>
**Class.forName** 메서드를 사용하여 Mysql JDBC Driver를 등록한다.드라이버 로딩(jdbc 4.0  이상부턴 이 코드 필요가 없이 자동으로 등록이 된다)<br>
이 부분은 ClassNotFoundException 핸들링을 해주어야 하므로 try-catch문으로 감싸주어야 한다.<br>
<h3>JDBC란?</h3>
Java DataBase Connectivity의 약자로서 자바 프로그램 내에서 DB와 관련된 작업을 처리할 수 있도록 도와주는 일을 한다.  Java에서 데이터베이스를 사용할 때에는 JDBC API를 이용하여 프로그래밍한다. 자바는 DBMS 종류에 상관없이 한나의 JDBC API를 사용하여 데이터베이스 작업을 처리할 수 잇기 때문에 알아두면 어떤 DBMS든 작업을 처리할 수 있게 된다. (jar 파일) <br>
DriverManager.getConnection() 를 통해서 DB에 연결 해준다. 파라미터로는 ("db주소", "db아이디", "db비밀번호") 이 연결부는 SQLException예외처리로 Try-Catch로 감싸 주어야 한다.<br><br>


**ResultSet** : execteQuery로 명령하면 ResultSet이라는 객체를 돌려준다<br>
예를 들어 execteQuery("Select * from tableName"); 이라고 보냈다면<br>
tableName 라는 테이블에서 값을 가져올겁니다. 이 가져온 것이 ResultSet임.<br>
ResultSet 주요 상수, 메서드 : http://noritersand.tistory.com/96 <br>

```
public int login(String userID, String userPassword) {
		String SQL = "SELECT userPassword FROM USER WHERE userID = ?";
		try {
			pstmt = conn.prepareStatement(SQL);
			pstmt.setString(1, userID);
			rs = pstmt.executeQuery();
			if(rs.next()) {
				if(rs.getString(1).equals(userPassword)) {
					return 1; //로그인 성공
				}
				else
					return 0; //비밀번호 불일치
				
			}
			return -1; //아이디 없음
			
		} catch(Exception e) {
			e.printStackTrace();
		}
		return -2; //데이터베이스 오류
	}
```
String 변수 SQL을 통해 SELECT userPassword FROM USER WHERE userID = ? 문장으로 USER 테이블에서 userPassword를 가져 오도록 한다.<br>
**pstmt = conn.prepareStatement(SQL);** 문장의 prepareStatement를 통해 정해진 문장을 Connection conn으로 데이터베이스에 삽입<br>
**pstmt.setString(1, userID);** SELECT userPassword FROM USER WHERE userID = **?** 에서 ? 에다 들아갈 문잘을 set 해준다. 매개변수로 넘어온<br> userID를 ?에 넣어 줌으로써 userID로 데이터베이스 접근<br>
**rs = pstmt.executeQuery();** ResultSet rs에 받은 결과를 저장<br>
**executeQuery()** 함수는 1.리턴 값으로 ResultSet 객체의 값을 반환합니다. <br>
			 2. SELECT 구문을 수행할 때 사용되는 함수입니다.<br>
**if(rs.getString(1).equals(userPassword))** re.getString(1)을 함으로써 데이터베이스에서 받아온 값을 String으로 변환하여 뽑아 낸다.<br>
이때 getString(1) 1을 해준 이유는 데이터 베이스 두번째 column이 userPassword이기 때문이며 1대신 "userPassword"를 넣어도 무관하다.<br>
***
# loginAction.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ page import="user.UserDAO" %>
<%@ page import="java.io.PrintWriter" %>
<% request.setCharacterEncoding("UtF-8"); %>
<jsp:useBean id="user" class="user.User" scope="page" />
<jsp:setProperty name="user" property="userID" />
<jsp:setProperty name="user" property="userPassword" />
```

	    
**@ page import="user.UserDAO"**  우리가 만든 class 사용을 위해 user패키지의 UserDAO를 import 해준다.<br>
**@page import="java.io.PrintWriter"**  JavaScript 문장을 사용하기 위해 import해주는 것<br>
**request.setCharacterEncoding("UtF-8");**  건너오는 데이터를 UTF-8로 받기 위해.<br>

<h3>자바 빈즈란?</h3>
 자바 빈즈는 JSP 페이지의 로직 부분을 분리해서 코드를 재사용함으로 프로그램의 효율을 높이기 위해 사용한다. 프로그램의 모듈화는 코드를 재사용하므로 프로그램의 작성기간이 단축되고, 이미 사용되던 코드이므로 안정성이 보장되며 유지/보수가 쉽다. MVC 패턴에서 자바 빈은 프로그램 로직을 소유할 수 있고 DB와 연동해서 작업을 처리한다.
<br>
자바 빈(JavaBean)은 데이터를 표현하는 것을 목적으로 하는 자바 클래스다. 콤포넌트와 비슷한 의미로 사용되기도 한다. <br>


**jsp:useBean id="user" class="user.User" scope="page"**<br>
 useBean 액션 태그<br>
> 사용할 객체를 지정하는 작업 class 속성에 명시한 클래스를 id로 지정한 이름으로 사용하겠다는 의미<br>
>scope는 해당 객체의 유효범위를 지정함 => page, request, application 등..<br>
>scope="page"하면 현재 페이지에서만 적용하겠다.<br>

**jsp:setProperty name="user" property="userID"**<br>
setProperty 액션 태그
>: useBean에서 지정한 id에 해당하는 객체의 name 필드에 hyr을 대입함
>name 속성은 위에서 지정한 id이며
>property는 필드 변수 명
>value는 값을 의미한다.<br>
login.jsp 폼에 있는 userID를 User.java를 거쳐 받아온다.<br>
**주의점:** property에 사용되는 이름과 폼에 있는 변수명은 같아야한다.<br>

```jsp
<%
	String userID= null;
	if (session.getAttribute("userID") != null) {
		userID = (String) session.getAttribute("userID");
	}
	if(userID != null) {
		PrintWriter script = response.getWriter();
		script.println("<script>");
		script.println("alert('이미 로그인되었습니다.')");
		script.println("location.href = 'main.jsp'");
		script.println("</script>");
	}
	UserDAO userDAO = new UserDAO();
	int result = userDAO.login(user.getUserID(),user.getUserPassword());
	if (result == 1){
		session.setAttribute("userID",user.getUserID());
		PrintWriter script = response.getWriter();
		script.println("<script>");
		script.println("location.href = 'main.jsp'");
		script.println("</script>");
	}
	else if (result ==0) {
		PrintWriter script = response.getWriter();
		script.println("<script>");
		script.println("alert('비번이 틀립니다.')");
		script.println("history.back()");
		script.println("</script>");
	}
	else if (result == -1) {
		PrintWriter script = response.getWriter();
		script.println("<script>");
		script.println("alert('없는 아이디 입니다.')");
		script.println("history.back()");
		script.println("</script>");
	}
	else if (result == -2) {
		PrintWriter script = response.getWriter();
		script.println("<script>");
		script.println("alert('데이터 베이스 오류.')");
		script.println("history.back()");
		script.println("</script>");
	}
	%>
```
**UserDAO userDAO = new UserDAO();<br>
int result = userDAO.login(user.getUserID(),user.getUserPassword());**<br>
UserDAO의 인스턴스 생성 후 userDAO.java의 login()함수 호출, 파라미터로는 **자바 빈즈의 id인 user로 User.java의 함수들을 불러와 사용**
<br>
<br>
**jsp로 경고창 띄우기**
>PrintWriter script = response.getWriter();<br>
script.println("<script>");           스크립트 문장 유동적으로 사용<br>
script.println("location.href = 'main.jsp'");    main.<br>jsp로 이동<br>
script.println("</script>");<br>

>PrintWriter script = response.getWriter();<br>
script.println("<script>");<br>
script.println("alert('없는 아이디 입니다.')");  경고창 띄우기<br>
script.println("history.back()"); **history.back()** 이전페이지로 돌려 보내기<br> 
script.println("</script>");<br>





