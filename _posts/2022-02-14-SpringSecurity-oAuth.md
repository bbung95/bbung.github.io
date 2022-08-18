---
title: "Spring - Security - oAuth"
excerpt: "Spring - Security - oAuth"

categories:
- Java
tags:
- [Spring, Security, Java]

permalink: /java/spring-security-oauth/

toc: true
toc_sticky: true

date: 2022-02-14
last_modified_at: 2022-02-14
---
# Spring - Security - oAuth
---

## oAuth
---
oAuth란?

	OAuth는 인터넷 사용자들이 비밀번호를 제공하지 않고 다른 웹사이트 상의 자신들의 정보에 대해 웹사이트나 애플리케이션의 접근 권한을 부여할 수 있는 공통적인 수단으로서 사용되는, 접근 위임을 위한 개방형 표준이다. (위키백과)

oAuth를 통해 google , kakao , facebook 과 같은 소셜 로그인 을 구현 할 수 있다.

## Dependency 추가
---

gradle 빌드 기준

``` gradle
implementation 'org.springframework.boot:spring-boot-starter-oauth2-client'
```

build.gradle 에 위와 같이 라이브러리를 추가하거나 또는 프로젝트 셋팅시 시큐리티 체크를 해준다.


## google 로그인
---
대표적인 구글 로그인을 구현하려고 한다.

### 구글 키 발급


### application 추가

**yml**
``` yml
server:
  port: 8080
  servlet:
    context-path: /
    encoding:
      charset: UTF-8
      enabled: true
      force: true
      
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/security?serverTimezone=Asia/Seoul
    username: cos
    password: cos1234
    
  mvc:
    view:
      prefix: /templates/
      suffix: .html

  jpa:
    hibernate:
      ddl-auto: update #create update none
      naming:
        physical-strategy: org.hibernate.boot.model.naming.PhysicalNamingStrategyStandardImpl
    show-sql: true
    
 
  security:
    oauth2:
      client:
        registration:
          google:
            client-id: [google-client-id]
            client-secret: [google-client-secret]
            scope:
            - email
            - profile

        # kakao:
        #   client-id: [kakao-client-id]
        #   client-secret: [kakao-client-secret]
        #   scope:
        #   - email
        #   - profile
```

.yml 또는 .properties에 security.oauth2.client.registration.~~ 를 작성하여 필요한 키값을 작성해준다.


### SecurityConfig

일반 시큐리티 로그인에서 작성했던 Config에 추가로 코드를 입력하여 준다.

```java
@Autowired
	private PrincipalOauth2UserService principalOauth2UserService;

.....

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
			.oauth2Login() // OAuth 로그인
			.loginPage("/login")
			.userInfoEndpoint()
			.userService(principalOauth2UserService);
			// 구글 로그인이 완료된 뒤의 후처리가 필요함. Tip.코드 X , (엑세스토큰+사용자프로필정보 O )
	}
```

위와 같이 oauth2Login() 을 작성하여 쉽게 OAuth2 로그인 코드를 작성 할 수 있다.  
하지만 로그인이 완료된후 일반 로그인과는 다르게 후처리가 필요하다.

### PrincipalOauth2UserService

일반 로그인은 유저 객체의 데이터를 받아 회원가입을 하고 같은 유저 객체를 시큐리티 세션에 넣어줘 로그인을 하게 된다.

하지만 OAuth 로그인 같은 경우 현재 사이트에서 사용하고있는 데이터와 맞지 않고 또 추가적인 데이터가 필요 할 수 있다.

이러한 후처리 작업은 PrincipalOauth2UserService 에서 수행하게 되며 DefaultOAuth2UserService 을 상속받아 loadUser를 오버라이딩하여 사용한다.

```java
@Service
public class PrincipalOauth2UserService extends DefaultOAuth2UserService{
	
	@Autowired
	private UserRepository userRepository;
	
	// 구글로 부터 받은 userRequest 데이터에 대한 후처리되는 함수
	// 함수 종료시 @AuthenticaitonPrincipal 어노테이션이 만들어진다
	@Override
	public OAuth2User loadUser(OAuth2UserRequest userRequest) throws OAuth2AuthenticationException {
		
		System.out.println("getClientRegistration : "+userRequest.getClientRegistration()); // registrationId로 어떤 OAuth로 로그인했는지 확인
		System.out.println("getAccessToken : "+userRequest.getAccessToken().getTokenValue());
		
		OAuth2User oauth2User = super.loadUser(userRequest);
		// 구글로그인 버튼 클릭 -> 구글로그인창 -> 로그인 완료 -> code를 리턴(OAuth-client라이브러리) -> AccessToken 요
		// userRequest 정보 -> loadUser함수 호출 -> 구글로부터 회원프로필 받아준다.
		System.out.println("getAttributes : "+oauth2User.getAttributes());
		
    // User Entity 데이터 후처리 작업 => 회원가입
		String provider = userRequest.getClientRegistration().getRegistrationId(); // Google
		String providerId = oauth2User.getAttribute("sub");
		String username = provider+"_"+providerId; // Google_123123213231231231213213
		String password = new BCryptPasswordEncoder().encode("겟인데어");
		String email = oauth2User.getAttribute("email");
		String role = "ROLE_USER";
		
		User userEntity = userRepository.findByUsername(username);
		
		if(userEntity == null) {
			// 회원가입 진행
			userEntity = User.builder()
					.username(username)
					.password(password)
					.email(email)
					.role(role)
					.provider(provider)
					.providerId(providerId)
					.build();
			
			userRepository.save(userEntity);

			System.out.println("최초 구글 로그인을 하셨습니다.");
		}else{
      System.out.println("구글 로그인을 하신적이 있습니다.");
    }
		
		return new PrincipalDetails(userEntity, oauth2User.getAttributes());
	}
}
```

OAuth 로그인시 User 값이 존재하지않기에 넘어오는 데이터로 User 데이터를 만들어 회원가입과 로그인을 할 수 있다.

### PrincipalDetails

이제 로그인이 컨트롤러에서 세션 정보를 불러와 사용 할 텐데 여기서 문제가 발생하게된다.

로그인시 시큐리티 세션에는 Authentication 객체가 들어가게되는데 거기에 넣을수 있는 데이터로 UserDetails와 OAuth2User 2가지만 들어 갈 수 있다.

UserDetails은 일반 로그인 시 , OAuth2User는 OAuth2 로그인 시 생성되는데 아래 코드와 같이 컨트롤러에서 받아 사용 할 수 있다.

하지만 저렇게 2가지 방법으로 세션 데이터를 받을 시 중복코드를 작성해야하며 비효율적인 코드를 사용하게 된다.

```java
  @GetMapping("test/login")
	@ResponseBody
	public String loginTest(Authentication authentication, @AuthenticationPrincipal UserDetails userDetail) { // DI(의존성 주입)
		
		System.out.println((UserDetails)authentication.getPrincipal()); // Authentication
		System.out.println(userDetail); // @AuthenticationPrincipal
		
		return "세션 정보 확인하기";
	}
	
	@GetMapping("test/oauth/login")
	@ResponseBody
	public String oauthLoginTest(Authentication authentication, @AuthenticationPrincipal OAuth2User oauth) { // DI(의존성 주입)
		
		System.out.println((OAuth2User)authentication.getPrincipal()); // Authentication
		System.out.println(oauth); // @AuthenticationPrincipal
		
		return "OAuth 세션 정보 확인하기";
	}
```

이러한 방법을 객체지향을 통해 해결할 수 있는데 class는 상속과 구현을 통해 오버라이딩과 오버로딩을 할 수 있다.

여기서 implements로 UserDetails과 OAuth2User를 구현하여 사용한다면 하나의 클래스로 두가지 인터페이스를 가질 수 있게된다.

이렇게 완성된 클래스는 인터페이스 기반으로 Authentication 세션에 들어갈 수 있는 객체가 된다.

```java
@ToString
@Setter
@Getter
public class PrincipalDetails implements UserDetails , OAuth2User{
	
	private User user;
	private Map<String , Object> attributes;
	
	// 일반 로그인
	public PrincipalDetails(User user) {
		this.user = user;
	};
	
	//OAuth 로그인
	public PrincipalDetails(User user , Map<String , Object> attributes) {
		this.user = user;
		this.attributes = attributes;
	};

  .....

  @Override
	public Map<String, Object> getAttributes() {
		return attributes;
	}

	@Override
	public String getName() {
		return null;
	}
```

위와 같이 2가지 인터페이스를 구현하여 클래스를 생성하고 생성자를 통해 필요한 데이터를 받을 수 있게된다.

하나 클래스로 구현된 Authentication 객체는 아래와 같이 PrincipalDetails타입으로 받아 사용 할 수 있다.
```java
@GetMapping("user")
	public String user(Model model , @AuthenticationPrincipal PrincipalDetails principalDetails) {
		
		System.out.println("principalDetails : "+principalDetails.getUser());
		model.addAttribute("success", "user");
		
		return "index";
	}
```