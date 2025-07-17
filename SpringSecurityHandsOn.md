
## Dependencies
```pom.xml
<parent>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-parent</artifactId>  
    <version>3.3.2</version>  
</parent>

<dependencies>  
    <dependency>        
	    <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-web</artifactId>  
    </dependency>   
     
    <dependency>        
	    <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-security</artifactId>  
    </dependency>
    
    <dependency>  
	    <groupId>org.projectlombok</groupId>  
	    <artifactId>lombok</artifactId>  
	    <version>1.18.20</version>  
	</dependency>
</dependencies>
```

## Spring boot application
```java
@SpringBootApplication  
public class SecurityApp {  
    public static void main(String[] args) {  
        SpringApplication.run(SecurityApp.class, args);  
    }  
}
```

## Controller (pages)
```java
@RestController  
@RequestMapping("/api")  
public class PageController {  
  
    @GetMapping("/hello")  
    ResponseEntity<String> getHelloPage(){  
        return new ResponseEntity<String>("Hello Security Demo",HttpStatus.OK);  
    }  
  
    @GetMapping("/admin")  
    ResponseEntity<String> getAdminPage(){  
        return new ResponseEntity<String>("Hello Admin",HttpStatus.OK);  
    }  
  
    @GetMapping("/user")  
    ResponseEntity<String> getUserPage(){  
        return new ResponseEntity<String>("Hello User",HttpStatus.OK);  
    }  
}
```

## First Try
After running, in log we will have.
```
...
Using generated security password: db4c415b-db0c-4e70-a19e-fa317e806b9f
...
```
Default user name -- ``user``

## Customize user name and password

For develop purpose only, in ``application.properties`` we add
```
spring.security.user.name=admin  
spring.security.user.password=12345
```
then there's no auto generated password, and the default user name is admin.


## MySecurityConfig
```Java
@Configuration  
public class MySecurityConfig {  
	
    @Bean  
    public static PasswordEncoder myPasswordEncoder(){  
        return new BCryptPasswordEncoder();  
    }  
}
```

## UserDetailsServiceImpl
May also use InMemoryUserDetailsManager
```Java
@Service  
public class UserDetailsServiceImpl implements UserDetailsService {  
  
    private Map<String, UserDetails> users;  
  
  
    public UserDetailsServiceImpl(){  
        PasswordEncoder pe = MySecurityConfig.myPasswordEncoder();  
        users = new HashMap<>();  
        users.put("admin", User.withUsername("admin")  
                .password(pe.encode("123456"))  
                .authorities("USER", "ADMIN").build());  
        users.put("user1", User.withUsername("user1")  
                .password(pe.encode("123"))  
                .authorities("USER").build());  
        users.put("user2", User.withUsername("user2")  
                .password(pe.encode("456"))  
                .authorities("USER").build());  
  
    }  
  
    @Override  
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {  
        if (users.containsKey(username)){  
            UserDetails ud = users.get(username);  
            return new User(ud.getUsername(), ud.getPassword(), ud.getAuthorities());  
        }  
        throw new UsernameNotFoundException("User not found");  
    }  
}
```

## MySecurityConfig 2nd version
You can let the session to be stateless.
You can let some endpoints be accessed without authorization
You can disable form/httpBasic login
You can disable csrf protection

```Java
@Configuration  
@EnableMethodSecurity  
public class MySecurityConfig {  
  
    @Bean  
    public SecurityFilterChain myFilterChain(HttpSecurity http) throws Exception{  
        http.sessionManagement(session ->  
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS));  
        http.authorizeHttpRequests((authorize) -> authorize  
                .requestMatchers("/hello").permitAll()  
                .requestMatchers("/signin").permitAll()  
                .anyRequest().authenticated());  
        http.formLogin(Customizer.withDefaults());  
        http.httpBasic(Customizer.withDefaults());  
        http.csrf(AbstractHttpConfigurer::disable);  
  
        return http.build();  
  
    }  
  
  
    @Bean  
    public static PasswordEncoder myPasswordEncoder(){  
        return new BCryptPasswordEncoder();  
    }  
  
    @Bean  
    public AuthenticationManager authenticationManager(AuthenticationConfiguration builder) throws Exception {  
        return builder.getAuthenticationManager();  
    }  
}
```



## SignInRequestBody & SignInResponseBody

```Java
@NoArgsConstructor  
@AllArgsConstructor  
public class SignInRequestBody {  
    private String userName;  
    private String password;  
  
    public String getUserName() {  
        return userName;  
    }  
  
    public void setUserName(String userName) {  
        this.userName = userName;  
    }  
  
    public String getPassword() {  
        return password;  
    }  
  
    public void setPassword(String password) {  
        this.password = password;  
    }  
}

public class SignInResponseBody {  
    private int code;  
    private String message;  
    private String token;  
  
    public SignInResponseBody() {}  
  
    public SignInResponseBody(int code, String message, String token) {  
        this.code = code;  
        this.message = message;  
        this.token = token;  
    }  
  
    public int getCode() {  
        return code;  
    }  
  
    public void setCode(int code) {  
        this.code = code;  
    }  
  
    public String getMessage() {  
        return message;  
    }  
  
    public void setMessage(String message) {  
        this.message = message;  
    }  
  
    public String getToken() {  
        return token;  
    }  
  
    public void setToken(String token) {  
        this.token = token;  
    }  
}
```

## SignInController & SignInService

```Java
@RestController  
public class SignInController {  
    @Autowired  
    SignInService signInService;  
  
    @GetMapping("/singin")  
    public ResponseEntity<?> getSingin(){  
        return new ResponseEntity<String>("You are using the wrong http method!", HttpStatus.BAD_REQUEST);  
    }  
  
    @PostMapping("/signin")  
    public ResponseEntity<?> postUserNamePassword(@RequestBody SignInRequestBody signInRequestBody){  
        return signInService.signIn(signInRequestBody);  
    }  
}
```

```Java
public interface SignInService {  
    public ResponseEntity<SignInResponseBody> signIn(SignInRequestBody requestBody);  
}

@Service  
public class SignInServiceImpl implements SignInService{  
    @Autowired  
    AuthenticationManager authenticationManager;  
  
    @Autowired  
    JwtUtils jwtUtils;  
  
    @Override  
    public ResponseEntity<SignInResponseBody> signIn(SignInRequestBody requestBody) {  
        UsernamePasswordAuthenticationToken authenticationToken =  
                new UsernamePasswordAuthenticationToken(requestBody.getUserName(), requestBody.getPassword());  
        Authentication authenticate = authenticationManager.authenticate(authenticationToken);  
        if(Objects.isNull(authenticate)){  
            //throw new RuntimeException("Wrong username or password");  
            return new ResponseEntity<SignInResponseBody>(new SignInResponseBody(401, "Wrong username or password", null), HttpStatus.UNAUTHORIZED);  
        }  
  
        User user = (User) authenticate.getPrincipal();  
        String jwt = jwtUtils.generateTokenFromUsername(user);  
        return new ResponseEntity<SignInResponseBody>(new SignInResponseBody(200, "Success", jwt), HttpStatus.OK);  
    }  
}
```


## JwtUtils & JwtFilter & JwtCache (possibly JwtExceptionHandler)

```Java
@Component  
public class JwtUtils {  
  
    private static final Logger logger = LoggerFactory.getLogger(JwtUtils.class);  
  
    @Value("${spring.app.jwtSecret}")  
    private String jwtSecret;  
  
    @Value("${spring.app.jwtExpirationMs}")  
    private int jwtExpirationMs;  
  
    public String getJwtFromHeader(HttpServletRequest request) {  
        String bearerToken = request.getHeader("Authorization");  
        logger.debug("Authorization Header: {}", bearerToken);  
        if (bearerToken != null && bearerToken.startsWith("Bearer ")) {  
            return bearerToken.substring(7); // Remove Bearer prefix  
        }  
        return null;  
    }  
  
    public String generateTokenFromUsername(UserDetails userDetails) {  
        String username = userDetails.getUsername();  
        return Jwts.builder()  
                .subject(username)  
                .issuedAt(new Date())  
                .expiration(new Date((new Date()).getTime() + jwtExpirationMs))  
                .signWith(key())  
                .compact();  
    }  
  
    public String getUserNameFromJwtToken(String token) {  
        return Jwts.parser()  
                .verifyWith((SecretKey) key())  
                .build().parseSignedClaims(token)  
                .getPayload().getSubject();  
    }  
  
    private Key key() {  
        return Keys.hmacShaKeyFor(Decoders.BASE64.decode(jwtSecret));  
    }  
  
    public boolean validateJwtToken(String authToken) {  
        try {  
            System.out.println("Validate");  
            Jwts.parser().verifyWith((SecretKey) key()).build().parseSignedClaims(authToken);  
            return true;  
        } catch (MalformedJwtException e) {  
            logger.error("Invalid JWT token: {}", e.getMessage());  
        } catch (ExpiredJwtException e) {  
            logger.error("JWT token is expired: {}", e.getMessage());  
        } catch (UnsupportedJwtException e) {  
            logger.error("JWT token is unsupported: {}", e.getMessage());  
        } catch (IllegalArgumentException e) {  
            logger.error("JWT claims string is empty: {}", e.getMessage());  
        }  
        return false;  
    }  
}
```

```Java
@Component  
public class JwtFilter extends OncePerRequestFilter {  
    @Autowired  
    private JwtUtils jwtUtils;  
  
    @Autowired  
    private JwtCache jwtCache;  
  
    //@Autowired  
    //private UserDetailsService userDetailsService;  
    //private static final Logger logger = LoggerFactory.getLogger(JwtFilter.class);  
    @Override  
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)  
            throws ServletException, IOException {  
        //logger.debug("AuthTokenFilter called for URI: {}", request.getRequestURI());  
        try {  
            String jwt = parseJwt(request);  
            if (jwt != null && jwtUtils.validateJwtToken(jwt)) {  
                String username = jwtUtils.getUserNameFromJwtToken(jwt);  
                System.out.println("Username of " + username + " parsed from jwt.");  
  
                UserDetails userDetails = jwtCache.records.get(username);  
                //UserDetails userDetails = userDetailsService.loadUserByUsername(username);  
  
                UsernamePasswordAuthenticationToken authentication =  
                        new UsernamePasswordAuthenticationToken(userDetails,  
                                null,  
                                userDetails.getAuthorities());  
                //logger.debug("Roles from JWT: {}", userDetails.getAuthorities());  
  
                //authentication.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));  
                SecurityContextHolder.getContext().setAuthentication(authentication);  
            }  
        } catch (Exception e) {  
            //logger.error("Cannot set user authentication: {}", e);  
        }  
  
        filterChain.doFilter(request, response);  
    }  
  
    private String parseJwt(HttpServletRequest request) {  
        String jwt = jwtUtils.getJwtFromHeader(request);  
        //logger.debug("AuthTokenFilter.java: {}", jwt);  
        return jwt;  
    }  
}
```

```Java
@Component  
public class JwtCache {  
    public Map<String, UserDetails> records = new ConcurrentHashMap<>();  
}
```

## MySecurityConfig 3rd version
```Java
@Configuration  
@EnableMethodSecurity  
public class MySecurityConfig {  
  
    @Autowired  
    public JwtFilter jwtFilter;  
  
    @Bean  
    public SecurityFilterChain myFilterChain(HttpSecurity http) throws Exception{  
        http.sessionManagement(session ->  
                session.sessionCreationPolicy(SessionCreationPolicy.STATELESS));  
        http.authorizeHttpRequests((authorize) -> authorize  
                .requestMatchers("/hello").permitAll()  
                .requestMatchers("/signin").permitAll()  
                .anyRequest().authenticated());  
        http.formLogin(Customizer.withDefaults());  
        http.httpBasic(Customizer.withDefaults());  
        http.csrf(AbstractHttpConfigurer::disable);  
  
        http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);  
        return http.build();  
  
    }  
  
  
    @Bean  
    public static PasswordEncoder myPasswordEncoder(){  
        return new BCryptPasswordEncoder();  
    }  
  
    @Bean  
    public AuthenticationManager authenticationManager(AuthenticationConfiguration builder) throws Exception {  
        return builder.getAuthenticationManager();  
    }  
}
```

## ``application.properties``
```
spring.app.jwtSecret=fdlakjfdadanflj1324123adfadf2345144afafadsfadfdas  
spring.app.jwtExpirationMs=2000000000
```