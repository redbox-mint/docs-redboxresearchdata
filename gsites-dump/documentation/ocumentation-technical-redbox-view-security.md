Prior to ReDBox 1.7, the only way to include security at a view level (i.e. allow/deny access to a certain page or URL) was to include some custom code in your python controller script. This meant that it wasn't particularly configurable and due to the fact that there was no standardised approach, there was inconsistencies in the user experience. 

  
In ReDBox 1.7, we have implemented the [Spring Security framework](http://projects.spring.io/spring-security/) to allow configurability as well as uncouple the security logic from our view logic. 

  
The View Configuration file 
The view configuration file is located in the home/applicationContext-security.xml and is using the [Expression Based Access Control](http://docs.spring.io/spring-security/site/docs/3.0.x/reference/el-access.html) approach. 
Here is the default configuration as is set up for RedBox: 

 


<http auto-config="false"  disable-url-rewriting="true" access-decision-manager-ref="fascinatorAccessDecisionManagerBean" use-expressions="true" entry-point-ref="authenticationEntryPoint">
 <intercept-url pattern="/**/login" access="permitAll" />
 <intercept-url pattern="/**/sso/*" access="permitAll" />
 <intercept-url pattern="/**/accessDenied" access="permitAll" />
 <intercept-url pattern="/**/history/*" access="hasRole('admin')" />
 <intercept-url pattern="/**/admin" access="hasRole('admin')" />                                                         
 <intercept-url pattern="/**/settings" access="hasRole('admin')" />
 <intercept-url pattern="/**/batchprocesses" access="hasRole('admin')" />
 <intercept-url pattern="/**/queues" access="hasRole('admin')" />
 <intercept-url pattern="/**/workflow/*" access="hasWorkflowAccess()" />
 <intercept-url pattern="/**/workflows/simpleworkflow.script" access="hasWorkflowAccess()" />
 <intercept-url pattern="/**/workflows/simpleworkflow.ajax" access="hasWorkflowAccess()" />
 <intercept-url pattern="/**/workflows/simpleworkflow*" access="hasWorkflowAccess()" />
 <intercept-url pattern="/**/download/**/*.tfPackage" access="hasRole('admin')" />
 <intercept-url pattern="/**/download/**/*" access="hasDownloadAccess()" />
 <intercept-url pattern="/**/detail/*" access="hasViewAccess()"/>
 <intercept-url pattern="/dashboard/**" access="isAuthenticated()"/> 
 <intercept-url pattern="/**/*.*" access="permitAll" />
 <intercept-url pattern="/*/**" access="permitAll" />
 <custom-filter ref="authenticationFilter" position="FORM_LOGIN_FILTER" />
 <custom-filter ref="fascinatorAuthInterceptor" before="FILTER_SECURITY_INTERCEPTOR" />
 <access-denied-handler ref="accessDeniedHandler"/>
 </http>


As well as the standard functions as outlined in the [spring documentation](http://docs.spring.io/spring-security/site/docs/3.0.x/reference/el-access.html), we have also defined some custom functions to help with locking down a view based on object security:


 Expression   Description `hasDownloadAccess()` Returns `true` if user has permission to download an attachment. hasViewAccess() Returns true if the user has permission to view the record hasWorkflowAccess() Returns true if the user has the ability to edit (workflow) the record isOwner() Returns true if the user is the owner


   ```
         
```