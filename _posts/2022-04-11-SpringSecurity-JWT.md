---
layout: post
title: "Spring - Security - JWT"
date: 2022-04-11 23:00:00 +0900
categories: Spring
---
# Spring - Security - JWT
---

## Spring - Security - JWT란?

원래 로그인 방식으로 cookie 또는 session 방식을 많이 이용해왔다.  
하지만 최근들어 MSA가 도입되고 확장성을 가지는 DB 구조와 SNS 로그인(OAuth)가 생기면서 Token 방식인 JWT를 사용하게 되었다. 

## Dependency 추가
---

gradle 빌드 기준

``` gradle
testImplementation 'org.springframework.security:spring-security-test'
implementation 'org.springframework.boot:spring-boot-starter-security'
implementation 'com.auth0:java-jwt:3.10.3'
```

기존에 Security의 Dependency에 JWT / implementation 'com.auth0:java-jwt:3.10.3'를 추가해준다.

auth0말고도 jjwt라는 디펜던시가 있는데 추후에 한번 알아보자

## SecurityConfig
---
```java
@Configuration
@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig extends WebSecurityConfigurerAdapter {

	@Override
	protected void configure(HttpSecurity http) throws Exception {
		http.csrf().disable();
		http.sessionManagement()
				.sessionCreationPolicy(SessionCreationPolicy.STATELESS) // session 방식 사용안함
			.and()
				.formLogin().disable() // form login 사용안함
				.httpBasic().disable() //
				.authorizeRequests()
				.antMatchers("URL")
				.access("hasRole('권한') or hasRole('권한') or hasRole('권한')")
				.anyRequest().permitAll();
	}

	@Bean // PasswordEncoder Bean 등록
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

JWT시큐리티를 적용하기 위해서 SecurityConfig 클래스를 생성한다.

Config는 WebSecurityConfigurerAdapter을 상속받아 사용하며 @Configuration 어노테이션을 추가한다.

위와 같이 설정후 프로젝트에 접속하게 되면 기본적으로 시큐리티에서 제공하는 로그인 화면으로 이동하게된다.  
하지만 토큰방식을 이용하기 때문에 @EnableWebSecurity 어노테이션을 추가한다.

## SecurityFilter
---

Session방식의 Security는 Login시 "/login" Url을 통해 UsernamePasswordAuthenticationFilter가 동작하여 로그인을 진행하게된다.

하지만 Token 방식은 따로 form을 사용하지 않기에 UsernamePasswordAuthenticationFilter를 직접 구현하여 로그인을 진행한다.

<img src="../public/img/JWT.png">

**JwtAuthenticationFilter**

```java
// 스프링 시큐리에서 UsernamePasswordAuthenticationFilter 가 있다.
// /login 요청해서 username, password 전송하면 (post)
// UsernamePasswordAuthenticationFilter 동작을 한다.
@RequiredArgsConstructor
public class JwtAuthenticationFilter extends UsernamePasswordAuthenticationFilter {

    private final AuthenticationManager authenticationManager;

    // /login 요청을 하면 로그인 시도를 위해서 실행되는 함수
    @Override
    public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {

        System.out.println("JwtAuthenticationFilter : 로그인 시도중");

        try {
			// 넘어오는 username과 password는 JSON으로 넘어와 따로 parse을 통해 User Object로 만들어준다.
            ObjectMapper om = new ObjectMapper();
            User user = om.readValue(request.getInputStream(), User.class);

            // 1. username, password 받는다.
            UsernamePasswordAuthenticationToken authenticationToken =
                    new UsernamePasswordAuthenticationToken(user.getUsername(), user.getPassword());


            // 2. 정상인지 로그인 시도를 해본다. authenticationManager로 로그인 시도를 하면
            // PrincopalDetailService의 loadUserByUsername() 함수가 실행된 후 정상이면 authentication이 리턴된다.
            Authentication authentication = authenticationManager.authenticate(authenticationToken);

            // 3. PrincipalDetail을 세션에 담는다. (권한 관리를 위해서 Session이 필요)
            PrincipalDetails principalDetails = (PrincipalDetails) authentication.getPrincipal();
            System.out.println("로그인 완료됨 : " + principalDetails.getUser().getUsername());// 로그인 정상적으로 되었다는 뜻.
            // authentication 객체가 session영역에 저장을 해야하고 그 방법이 return 해주면 된다.
            // 리턴의 이유는 권한 관리를 security가 대신 해주기 때문에 편리하게 구현이 가능하다.
            // 굳이 JWT 토큰을 사용하면서 세션을 만들 이유가 없다.

            // 4. JWT토큰을 만들어서 응답해준다.
            return authentication;
        }catch (IOException e){
            e.printStackTrace();
        }

        return null;
    }

    // attempAuthentication실행 후 인증이 정상적으로 되었으면 successfulAuthentication 함수가 실행된다.
    // JWT 토큰을 만들어서 request요청한 사용자에게 JWT토큰을 respone해주면 된다.
    @Override
    protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authResult) throws IOException, ServletException {

        System.out.println("successfulAuthentication 실행됨 : 인증이 완료되었다는 뜻");
        PrincipalDetails principalDetails = (PrincipalDetails) authResult.getPrincipal();

        // RSA방식이 아닌 Hash암호방식
        String jwtToken = JWT.create()
                .withSubject("Subject") 											// Subject
                .withExpiresAt(new Date(System.currentTimeMillis()+(60000*10))) 	// 유효기간
                .withClaim("id", principalDetails.getUser().getId())				// Cliam
                .withClaim("username", principalDetails.getUser().getUsername())	// Cliam
                .sign(Algorithm.HMAC512("sign"));									// 서명

        response.addHeader("Authorization", "Bearer "+jwtToken);
    }
}
```

아래의 필터를 통해 토큰의 서명인증과 유효성 체크를 하게된다.

***JwtAuthorizationFilter***
```java
// 시큐리티가 filter를 가지고 있는데 그 필터중에 BasicAuthenticationFilter라는 것이 있다.
// 권한이나 인증이 필요한 특정 주소를 요청했을 때 위 필터를 무조건 타게 되어있다.
// 만약에 권한이 인증이 필요한 주소가 아니라면 이 필터를 안탄다.
public class JwtAuthorizationFilter extends BasicAuthenticationFilter {

    private UserRepository userRepository;

    public JwtAuthorizationFilter(AuthenticationManager authenticationManager, UserRepository userRepository) {
        super(authenticationManager);
        this.userRepository = userRepository;
    }

    // 인증이나 권한이 필요한 주소요청이 있을 때 해당 필터를 타게 됨.
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws IOException, ServletException {
        //super.doFilterInternal(request, response, chain); // 응답 오류
        System.out.println("인증이나 권한이 필요한 주소 요청이 됨.");

        String jwtHeader = request.getHeader("Authorization");
        System.out.println("jwtHeader = " + jwtHeader);

        // header가 있는지 확인
        if(jwtHeader == null || !jwtHeader.startsWith("Bearer ")){
            chain.doFilter(request, response);
            return;
        }

        // JWT 토큰을 검증을 해서 정상적인 사용자인지 확인
        String token = jwtHeader.replace("Bearer ", "").trim();

        String username = JWT.require(Algorithm.HMAC512("cos")).build().verify(token).getClaim("username").asString();

        System.out.println("username = " + username);

        // 서명이 정상적으로 됨
        if(username != null && !username.equals("")){

            System.out.println("username 정상");
            User findUser = userRepository.findByUsername(username);

            System.out.println("findUser = " + findUser.getUsername());
            PrincipalDetails principalDetails = new PrincipalDetails(findUser);

            // JWT 토큰 서명을 통해서 서명이 정상이면 Authentication 객체를 만들어준다.
            Authentication authentication = new UsernamePasswordAuthenticationToken(principalDetails, null, principalDetails.getAuthorities());

            // 강제로 시큐리티의 세션에 접근하여 Authentication 객체를 저장.
            SecurityContextHolder.getContext().setAuthentication(authentication);

        }

        chain.doFilter(request, response);
    }

}
```

이렇게 작성한 필터를 SecurityConfig에 적용한다.

```java

private final CorsFilter corsFilter;
private final UserRepository userRepository;

@Override
protected void configure(HttpSecurity http) throws Exception {
	//security에 filter등록, SecurityContextPersistenceFilter 전에 실행 (Spring의 필터종류를 알아야한다)
	//security의 필터는 CustomFilter보다 무조건적으로 우선실행된다.
	//http.addFilterBefore(new CustomFilter(), SecurityContextPersistenceFilter.class);
	http.csrf().disable()
			.addFilter(corsFilter) // 모든 요청은 filter를 거치게된다. Controller에 @CrossOrigin(인증X), 시큐리티 핉터에 등록 인증(O)
			.sessionManagement()
			.sessionCreationPolicy(SessionCreationPolicy.STATELESS) // session 방식 사용안함
		.and()
			.formLogin().disable() // form login 사용안함
			.httpBasic().disable()
			.addFilter(new JwtAuthenticationFilter(authenticationManager()))				// 로그인
			.addFilter(new JwtAuthorizationFilter(authenticationManager(), userRepository)) // 토큰 서명인증
			.authorizeRequests()
			.antMatchers("/api/v1/user/**")
				.access("hasRole('ROLE_USER') or hasRole('ROLE_MANAGER') or hasRole('ROLE_ADMIN')")
			.antMatchers("/api/v1/manager/**")
				.access("hasRole('ROLE_MANAGER') or hasRole('ROLE_ADMIN')")
			.antMatchers("/api/v1/admin/**")
				.access("hasRole('ROLE_ADMIN')")
			.anyRequest().permitAll();
}
```

## CorsConfig
---

Token 방식을 사용하며 Controller 통신이아닌 RestController 통신을 자주 이용하게 된다.  

JWT를 사용하게된면 React와 같은 프로젝트와 통신하는 프로젝트를 진행하게 되는데 이때 발생하는 문제가 Cors문제이다.

**Cors란?**  

Cross Origin Resource Sharing 의 약자로 도메인이 다른 자원에 리소스를 요청할 때 접근 권한을 부여하는 메커니즘이다.  

Spring Project는 8080 port를 사용하고 React는 3000 port를 사용하게된다.  

만약 React에서 Spring API를 호출하게된다면 CORS문제를 만날 수 있을것이다.  

이것을 해결하기 위해 CorsConfig를 작성해준다.

```java
@Configuration
public class CorsConfig {

    @Bean
    public CorsFilter corsFilter(){
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        CorsConfiguration config = new CorsConfiguration();
        config.setAllowCredentials(true); // 내 서버가 응답을 할 때 json을 자바스크립트에서 처리할 수 있게 할지를 설정하는 것
        config.addAllowedOrigin("*");     // 모든 ip에 응답을 혀용하겠다.
        config.addAllowedHeader("*");     // 모든 header에 응답을 허용하겠다.
        config.addAllowedMethod("*");     // 모든 post, get, put, delete접근을 허용하겠다.

        source.registerCorsConfiguration("/api/**", config);
        return  new CorsFilter(source);
    }
}
```

## UserDetail
---
시큐리티는 Authentication 객체를 세션에 넣게되는데 이것을 구현하기 위해서 UserDetail 을 상속받아 사용하게된다.

#### User Entity
```java
@Data
public class User {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
    private String password;
    private String roles; // USER,ADMIN

    public List<String> getRoleList(){
        if(this.roles.length() > 0){
            return Arrays.asList(this.roles.split(","));
        }
        return new ArrayList<>();
    }
}
```

#### Authentication 객체
```java
// 로그인을 진행이 완료가 되면 시큐리티 session으 만들어 줍니다 (Security ContextHolder)
// 오브젝트 타입 => Authentication 타입 객체
// Authentication 안에 User 정보가 있어야 됨
public class PrincipalDetails implements UserDetails{
	
	private User user; // 유저 객체
	
	public PrincipalDetails(User user) {
		this.user = user;
	};
	
	// 해당 User의 권한을 리턴하는 곳!
	@Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        Collection<GrantedAuthority> authorities = new ArrayList<>();
        user.getRoleList().forEach(r->{
            authorities.add(() -> r);
        });
        return authorities;
    }

	@Override
	public String getPassword() {
		return user.getPassword();
	}

	@Override
	public String getUsername() {
		return user.getUsername();
	}
	
	// 계정 만료
	@Override
	public boolean isAccountNonExpired() {
		return true;
	}

	// 계정 잠김
	@Override
	public boolean isAccountNonLocked() {
		return true;
	}
	
	// 비밀번호가 오래 되었나
	@Override
	public boolean isCredentialsNonExpired() {
		return true;
	}
	
	// 계정 활성화
	@Override
	public boolean isEnabled() {
		
		// 사이트에서 유저가 1년동안 로그인을 안했다면!! 휴먼 계정으로 하기로 함
		// 현재시간 - 로긴시간 => 1년을 초과하면 false
		
		return true;
	}
}
```

## 회원가입
---
회원가입은 간단하게 암호화를 진행하여 등록하게된다.

```java
@RestController
@RequiredArgsConstructor
public class RestApiController

private final PasswordEncoder passwordEncoder; // 비밀번호 암호화가 필여하다. 시큐리티 로그인 불가능

	// 회원가입
	@PostMapping("join")
	public String join(@RequestBody User user) {
		
		user.setPassword(passwordEncoder.encode(user.getPassword()));
		user.setRoles("ROLE_USER");
		userRepository.save(user);

		return "회원가입완료";
	}
}
```

### 로그인
---

#### UserDetailsService
```java
// 시큐리티 설정에서 loginProcessingUrl("/login")
// /login 요청이 오면 자동으로 UserDetailsService 타입으로 IoC되어 있는 loadUserByUsername 함수 실행
@Service
@RequiredArgsConstructor
public class PrincipalDetailsService implements UserDetailsService {

	private final UserRepository userRepository;

	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		User findUser = userRepository.findByUsername(username);

		return new PrincipalDetails(findUser);
	}
}
```

