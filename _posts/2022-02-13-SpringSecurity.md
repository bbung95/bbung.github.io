---
title: "Spring - Security"
excerpt: "Spring - Security"

categories:
- Java
tags:
- [Spring, Security, Java]

permalink: /java/spring-security/

toc: true
toc_sticky: true

date: 2022-02-13
last_modified_at: 2022-02-13
---
# Spring - Security
---

## Spring - Security란?

스프링으로 개발을 하면 세션을 이용한 로그인 방법을 많이 이용한다.

하지만 세션을 이용하여 개발을 하면 코드로 작성해야하는 부분과 보안적으로 노출되는 부분이 많아서
문제가 발생하게된다.

이러한 부분을 스프링 시큐리티에서 세션 / 토큰 방식을 쉽고 간편하게 사용 할 수 있도록 지원한다.

## Dependency 추가
---

gradle 빌드 기준

``` gradle
implementation 'org.springframework.boot:spring-boot-starter-security'
testImplementation 'org.springframework.security:spring-security-test'
```

build.gradle 에 위와 같이 라이브러리를 추가하거나 또는 프로젝트 셋팅시 시큐리티 체크를 해준다.


## 적용
---

### SecurityConfig
```java
@Configuration
@EnableWebSecurity  // 필터 체인 관리 시작 어노테이션
@EnableGlobalMethodSecurity(securedEnabled = true , prePostEnabled = true) // secured 어노테이션 활성화 , preAuthorize / PostAuthorize 어노테이션 활성화
public class SecurityConfig extends WebSecurityConfigurerAdapter {
	
    // 소스파일 접근 추가
	@Override
    public void configure(WebSecurity web) throws Exception {
        web.ignoring().antMatchers("/js/**","/css/**","/images/**","/font/**" , "/assets/**" );
    }

    @Override
	protected void configure(HttpSecurity http) throws Exception {
		
		http.csrf().disable();
		http.authorizeRequests()
			.antMatchers("/user/**").authenticated() // 인증만 되면 들어갈 수 있는 주소!!
			.antMatchers("/manager/**").access("hasRole('ROLE_ADMIN') or hasRole('ROLE_MANAGER')")
			.antMatchers("/admin/**").access("hasRole('ROLE_ADMIN')")
			.anyRequest().permitAll()
		.and()
			.formLogin() 					// 기본 인증 로그인 페이지
			.loginPage("/login")
			.usernameParameter("username") // 임의의 유저 파라메터값
			.passwordParameter("password") // 임의의 유저 패스워드값
			.loginProcessingUrl("/login") // 시큐리티가 주소를 낚아채 시큐리티 로그인을 진행해줍니다.
			.defaultSuccessUrl("/")
		.and()
			.oauth2Login()
			.loginPage("/login"); // 구글 로그인이 완료된 뒤의 처리가 필요함.
	}
	
	// 해당 메서드의 리턴되는 오브젝트를 IoC로 등록해준다. 
	@Bean
	public BCryptPasswordEncoder encodePwd() {
		return new BCryptPasswordEncoder();
	}
}
```

시큐리티를 적용하기 위해서 프로젝트 내에 Config 클래스를 생성한다.

Config는 WebSecurityConfigurerAdapter을 상속받아 사용하며 @Configuration  어노테이션을 추가한다.

위와 같이 설정후 프로젝트에 접속하게 되면 기본적으로 시큐리티에서 제공하는 로그인 화면으로 이동하게된다.  
시큐리티에서 제공하는 로그인을 사용하지 않으러면 @EnableWebSecurity 어노테이션을 추가한다.

클래스를 셍성한 후 configure 메서드를 오버라이딩하여 권한처리를 하여 사용한다.

### 권한 설정

``` java
@Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable() // .csrf().disable() / Cross-Site Request Forgery 웹취약성 토큰
              .authorizeRequests() 
              .antMatchers("/login").permitAll()
  //          .antMatchers("/user/**").hasRole("ADMIN")
              .anyRequest().authenticated()
            .and()
                .formLogin()
                .loginPage("/login")
                .usernameParameter("user_id")
                .passwordParameter("user_password")
                .successHandler(sHandler) // 로그인 성공시
            .and()
                .logout()
                .logoutSuccessUrl("/login")
    			.deleteCookies("JSESSIONID") // 세션 쿠키 삭제
                .invalidateHttpSession(true)
            .and()
                .exceptionHandling().accessDeniedPage("/login/denied");
                
    }
```

antMatchers()를 사용하여 접근 권한을 처리할 수 있다.

- hasRole() or hasAnyRole() : 특정 권한을 가지는 사용자만 접근할 수 있습니다.  
- hasAuthority() or hasAnyAuthority() : 특정 권한을 가지는 사용자만 접근할 수 있습니다.  
- hasIpAddress() : 특정 아이피 주소를 가지는 사용자만 접근할 수 있습니다.  
- permitAll() or denyAll() : 접근을 전부 허용하거나 제한합니다.  
- rememberMe() : 리멤버 기능을 통해 로그인한 사용자만 접근할 수 있습니다.  
- anonymous() : 인증되지 않은 사용자가 접근할 수 있습니다.  
- authenticated() : 인증된 사용자만 접근할 수 있습니다.  

뿐만 아니라 특정 메서드에 관련하여 권한처리를 할 수 있다 

    @EnableGlobalMethodSecurity(securedEnabled = true , prePostEnabled = true)

- securedEnabled : @Secured("ROLE_[]") 어노테이션 활성화
- prePostEnabled : @PreAuthorize("hsaRole('ROLE_[]') or hsaRole('ROLE_[]')") / @PostAuthorize 어노테이션 활성화

위와 같이 특정 메서드에 권한처리를 할 수 있으며 최근에 나온 @Secured 어노테이션으로 쉽게 사용이 가능하다.  
만약 2개 이상의 권한 처리시에는 @PreAuthorize를 사용하여 or 을 통해 권한처리가 가능하다.

**http 접근 관련**

시큐리티를 사용하여 POST , PUT과 같은 접근을하게 되면 통신이 불가하다는 에러를 만나 볼 수 있다.  

기본적으로 보안을 위해 POST와 같은 접근을 제한해놨기 때문이다.

접근을 허용 하기 위해서는 아래와 같은 설정을 추가해줘야한다.

``` java
http.csrf().disable() // .csrf().disable() / Cross-Site Request Forgery 웹취약성 토큰
```

최근에는 앱에서 화면을 구성하고 , React를 통해 화면을 구성하면서 form이 아닌 script 요청을 하기 떄문에 자주 사용하지 않는다.


### 회원가입
---
회원가입은 간단하게 암호화를 진행하여 등록하게된다.

```java
@Autowired
private BCryptPasswordEncoder passwordEncoder;

// 회원가입
	@PostMapping("join")
	public String join(User user) {
		
		user.setRole("ROLE_USER");
		String rawPassword = user.getPassword();
		String encPassword = passwordEncoder.encode(rawPassword);
		user.setPassword(encPassword);
		
		userRepository.save(user); // 비밀번호 암호화가 필여하다. 시큐리티 로그인 불가능
		
		return "redirect:/login";
	}
```

위와 같이 접근하며 IoC에 등록하여 BCryptPasswordEncoder 인코딩을 통해 암호화후 등록을 해준다.

### UserDetail
---
시큐리티는 Authentication 객체를 세션에 넣게되는데 이것을 구현하기 위해서 UserDetail 을 상속받아 사용하게된다.

#### User Entity
```java
@Entity
@Data
public class User {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private int id;
	private String username;
	private String password;
	private String email;
	private String role;
	
	@CreationTimestamp
	private Timestamp createDate;
}

```

#### Authentication 객체
```java
// 시큐리티가 /login 주소 요청 오면 낚아채서 로그인을 진행시킨다.
// 로그인을 진행이 완료가 되면 시큐리티 session으 만들어 줍니다 (Security ContextHolder)
// 오프젝트 타입 => Authentication 타입 객체
// Authentication 안에 User 정보가 있어야 됨.
// User 오브젝트타입 => UserDetails 타입 객체

// Security Session => Authentication => UserDetails(PrincipalDetails)

public class PrincipalDetails implements UserDetails{
	
	private User user; // 유저 객체
	
	public PrincipalDetails(User user) {
		this.user = user;
	};
	
	// 해당 User의 권한을 리턴하는 곳!
	@Override
	public Collection<? extends GrantedAuthority> getAuthorities() {
		Collection<GrantedAuthority> collect = new ArrayList<GrantedAuthority>();
		collect.add(new GrantedAuthority() {
			@Override
			public String getAuthority() {
				return user.getRole();
			}
		});
		
		return collect;
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

### 로그인
---

#### UserDetailsService 서비스
```java
// 시큐리티 설정에서 loginProcessingUrl("/login")
// /login 요청이 오면 자동으로 UserDetailsService 타입으로 IoC되어 있는 loadUserByUsername 함수 실행
@Service
public class PricipalDetailsService implements UserDetailsService{
	
	@Autowired
	private UserRepository userRepository;
	
	// 시큐리티 session = Authentication = UserDetails
	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		
		User user = userRepository.findByUsername(username);
		
		if(user != null) {
			return new PrincipalDetails(user);
		}
		
		return null;
	}

}
```

    [로그인시도 -> SecurityConfig loginProcessingUrl 낚아챔 -> PrincipalDetailsService 에서 정보 확인 -> UserDetails생성 -> PrincipalDeatail 생성 -> session(Authentication)안에 말아서 리턴]

위와 같은 로직을 통해 로그인을 할 수 있게 된다.


