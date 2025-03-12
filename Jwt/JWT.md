# JWT (Json Web Token)
Jwt의 기본적인 내용은 이미 많은 분들이 포스팅 해주셨기에 
이 글에 담지 않겠습니다. 여기서는 제가 Jwt를 구현하면서 알게된 내용과 구현하면서 느꼈던점을 위주로 작성하겠습니다.

클라이언트와 서버간 인증을 하는 방식에는 여러가지가 있습니다. 가장 많이 사용되는 방식은 두가지입니다.
> 세션인증 방식
- 세션인증 방식은 서버 세션 스토리지에 사용자의 정보를 저장하고 인증하는 방식입니다.  
> JWT 인증 방식
- JWT 인증방식은 서버에 사용자의 정보를 저장하지 않고 클라이언트에서 로그인시 서버에서 토큰을 발급하여 
클라이언트가 접근할때마다 토큰을 검사하여 인증하는 방식입니다. 

먼저 스프링 시큐리티에서 제공하는 UserDetails 인터페이스를 구현하여 인증 시 사용할 CustomUserDetails를 구현해야합니다. CustomuserDetails 내부에는 User 엔터티가 있고 
인증과정에서 username password role 등등 인증에 필요한 정보는 UserDetails에서 관리합니다.
```java
@Getter
public class CustomUserDetails implements UserDetails {

	private final User user;

	public CustomUserDetails(User user) {
		this.user = user;
	}

	@Override
	public Collection<? extends GrantedAuthority> getAuthorities() {

		Collection<GrantedAuthority> collection = new ArrayList<>();

		collection.add(new GrantedAuthority() {

			@Override
			public String getAuthority() {

				return user.getRole().name();
			}
		});

		return collection;
	}

	@Override
	public String getPassword() {
		return user.getPassword();
	}

	@Override
	public String getUsername() {
		return user.getUsername();
	}

	@Override
	public boolean isAccountNonExpired() {
		return true;
	}

	@Override
	public boolean isAccountNonLocked() {
		return true;
	}

	@Override
	public boolean isCredentialsNonExpired() {
		return true;
	}

	@Override
	public boolean isEnabled() {
		return true;
	}
}

```

```java
@Service
@RequiredArgsConstructor
public class CustomUserDetailsService implements UserDetailsService {

	private final UserRepository userRepository;

	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		User user = userRepository.findByUsername(username).orElseThrow(() -> new GlobalBusinessException(UserErrorCode.USER_NOT_FOUND));

		return new CustomUserDetails(user);
	}
}
```

## LoginFilter
> 보안을 강화하고 사용성을 높이는 access 토큰과 refresh 토큰을 사용한 다중 토큰 방식을 구현해보겠습니다.

- LoginFilter에서 살펴볼점은 스프링 시큐리티의 UsernamePasswordAuthenticationFilter를 상속하여 필터를 커스텀했다는 점입니다. 
- UsernamePasswordAuthenticationFilter의 attemptAuthentication과 AbstractAuthenticationProcessingFilter의 successfulAuthentication을 구현하였습니다.
- 클라이언트(이하 사용자)가 로그인하면 커스텀된 LoginFilter를 거쳐 attemptAuthentication 메소드에서 회원가입된 사용자의 정보를  authenticationManager가 확인합니다.
-  회원가입이 된 사용자면 successfulAuthentication을 호출합니다. successfulAuthentication에서는 간단한 사용자의 정보로 access토큰과 refresh 토큰을 만들어 클라이언트의 헤더와 쿠키에 반환해줍니다.


> claims 인증을 위한 사용자의 정보는 role데이터도 필요할까? userId만 있으면 되지않나 싶은 생각 

```java
@RequiredArgsConstructor
public class LoginFilter extends UsernamePasswordAuthenticationFilter {

	public static final int COOKIE_MAX_AGE = 12 * 60 * 60;
	ObjectMapper objectMapper = new ObjectMapper();

	private final AuthenticationManager authenticationManager;
	private final JWTUtil jwtUtil;
    private final RefreshRepository refreshRepository;


	@Override
	public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response) throws AuthenticationException {

		try {
			LoginDto loginDto = objectMapper.readValue(request.getInputStream(), LoginDto.class);

			//스프링 시큐리티에서 username과 password를 검증하기 위해서는 token에 담아야 함
			UsernamePasswordAuthenticationToken authToken = new UsernamePasswordAuthenticationToken(loginDto.getUsername(), loginDto.getPassword(), null);

			//token에 담은 검증을 위한 AuthenticationManager로 전달
			return authenticationManager.authenticate(authToken);

		} catch (IOException e) {
			throw new RuntimeException(e);
		}
	}

	@Override
	protected void successfulAuthentication(HttpServletRequest request, HttpServletResponse response, FilterChain chain, Authentication authentication) throws IOException, ServletException {

		String username = authentication.getName();

		Collection<? extends GrantedAuthority> authorities = authentication.getAuthorities();
		Iterator<? extends GrantedAuthority> iterator = authorities.iterator();
		GrantedAuthority auth = iterator.next();

		String role = auth.getAuthority();

		String access = jwtUtil.createJwt("access",username, role, JwtProperties.ACCESS_TOKEN_TIME);
		String refresh = jwtUtil.createJwt("refresh",username, role, JwtProperties.REFRESH_TOKEN_TIME);

		addRefresh(username,refresh);

		//응답 설정
		response.setHeader(JwtProperties.HEADER_STRING, JwtProperties.TOKEN_PREFIX + access);
		response.addCookie(createCookie(refresh));
		response.setStatus(HttpStatus.OK.value());
	}


    @Override
    protected void unsuccessfulAuthentication(HttpServletRequest request, HttpServletResponse response, AuthenticationException failed) throws IOException, ServletException {
        response.setStatus(HttpStatus.UNAUTHORIZED.value());
    }

        protected void addRefresh(String username, String refresh) {
            Date date = new Date(System.currentTimeMillis() + JwtProperties.REFRESH_TOKEN_TIME);

            RefreshEntity refreshEntity = new RefreshEntity();
            refreshEntity.setUsername(username);
            refreshEntity.setRefresh(refresh);
            refreshEntity.setExpiration(date.toString());

            refreshRepository.save(refreshEntity);
        }

    Cookie createCookie(String value) {
        Cookie cookie = new Cookie("refresh", value);
        cookie.setMaxAge(COOKIE_MAX_AGE); // 12h
        cookie.setHttpOnly(true);   //JS로 접근 불가, 탈취 위험 감소

        return cookie;
    }
```
## JWTUtil
JWTUtil에서는 JWT 토큰을 관리합니다. 생성,정보확인,유효성검사 등
```java
// JWT 발급 및 검증하는 유틸
@Component
public class JWTUtil {

	private final SecretKey secretKey;

	// 비밀키 값을 SecretKey 객체로 반환
	public JWTUtil(@Value("${spring.jwt.key}") String key) {
		this.secretKey = Keys.hmacShaKeyFor(key.getBytes());
		System.out.println("secretKey = " + secretKey);
	}

	/**
	 *
	 * @param category access인지 refresh인지
	 * @param username
	 * @param role
	 * @param expiredMinute
	 * @return
	 */
	public String createJwt(String category, String username, String role, long expiredMinute){

		return Jwts.builder()
			.claim("category",category)
			.claim("username", username)
			.claim("role", role)
			.issuedAt(new Date(System.currentTimeMillis())) //현재 발행시간
			.expiration(new Date(System.currentTimeMillis() + expiredMinute))
			.signWith(secretKey) // 암호화
			.compact();
	}

	// 토큰 검증 - 카테고리
	public String getCategory(String token){
		Jws<Claims> jws =  Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token);
		return jws.getPayload().get("category", String.class);
	}

	// 토큰 검증 - 아이디
	public String getUsername(String token){
		Jws<Claims> jws =  Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token);
		return jws.getPayload().get("username", String.class);
	}

	// 토큰 검증 - role
	public String getRole(String token){
		Jws<Claims> jws = Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token);
		return jws.getPayload().get("role", String.class);
	}

	// 토큰 검증 - 토큰 유효기간 비교
	public Boolean isExpired(String token){

		try{
			Jws<Claims> jws = Jwts.parser().verifyWith(secretKey).build().parseSignedClaims(token);
			Date expDate = jws.getPayload().getExpiration();
			// 현재 날짜가 exp 날짜보다 뒤에 있으면, 만료됨
			return new Date().after(expDate);

		} catch (ExpiredJwtException e){
			e.printStackTrace();
			return true;
		}
	}
}
``` 
## JWTFilter
로그인 이후에 사용자는 발급받은 access 토큰을 헤더에 담아 서버에 요청시 전송하게 되는데 이때 서버에서는 JWT의 validation 검사를 진행해야 합니다. 토큰시간이 만료되지는 않았는지 사용자 정보가 일치하는 지 등 

```java
@RequiredArgsConstructor
public class JWTFilter extends OncePerRequestFilter {

	private final JWTUtil jwtUtil;
	private final UserRepository userRepository;

	@Override
	protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
		// 헤더에서 access키에 담긴 토큰을 꺼냄
		String accessToken = request.getHeader("access");

		// 토큰이 없다면 다음 필터로 넘김
		if (accessToken == null) {

			filterChain.doFilter(request, response);

			return;
		}

		// 토큰 만료 여부 확인, 만료시 다음 필터로 넘기지 않음
		try {
			jwtUtil.isExpired(accessToken);
		} catch (ExpiredJwtException e) {

			//response body
			PrintWriter writer = response.getWriter();
			writer.print("access token expired");

			//response status code
			response.setStatus(HttpServletResponse.SC_UNAUTHORIZED); //프론트와 협의된 코드를 주기
			return;
		}

		// 토큰이 access인지 확인 (발급시 페이로드에 명시)
		String category = jwtUtil.getCategory(accessToken);

		if (!category.equals("access")) {

			//response body
			PrintWriter writer = response.getWriter();
			writer.print("invalid access token");

			//response status code
			response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
			return;
		}

		String username = jwtUtil.getUsername(accessToken);

		User findUser = userRepository.findByUsername(username).orElseThrow(() -> new GlobalBusinessException(UserErrorCode.USER_NOT_FOUND));
		CustomUserDetails customUserDetails = new CustomUserDetails(findUser);

		Authentication authToken = new UsernamePasswordAuthenticationToken(customUserDetails, null, customUserDetails.getAuthorities());

		SecurityContextHolder.getContext().setAuthentication(authToken);

		filterChain.doFilter(request, response);
	}
}
```

## SecurityConfig
```java
@Configuration
@RequiredArgsConstructor
public class SecurityConfig {

	private final UserRepository userRepository;
	private final JWTUtil jwtUtil;
	private final RefreshRepository refreshRepository;

	@Bean
	public BCryptPasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}

	@Bean
	public AuthenticationManager authenticationManager(AuthenticationConfiguration authenticationConfiguration) throws Exception {
		return authenticationConfiguration.getAuthenticationManager();
	}

	@Bean
	public SecurityFilterChain configure(HttpSecurity http, AuthenticationManager authenticationManager, AuthenticationConfiguration authenticationConfiguration) throws Exception {

		LoginFilter loginFilter = new LoginFilter(authenticationManager,jwtUtil,refreshRepository);
		
		loginFilter.setFilterProcessesUrl("/auth/login");

		http
			.formLogin(AbstractHttpConfigurer::disable)
			.csrf(AbstractHttpConfigurer::disable);

		http
			.sessionManagement(sessionManager -> sessionManager.sessionCreationPolicy(SessionCreationPolicy.STATELESS));

		http
			.httpBasic(AbstractHttpConfigurer::disable);

		http
			.addFilterBefore(new JWTFilter(jwtUtil,userRepository), LoginFilter.class)
			.addFilterAt(loginFilter, UsernamePasswordAuthenticationFilter.class)
			.addFilterBefore(new CustomLogoutFilter(jwtUtil,refreshRepository), LogoutFilter.class)
		;

		http
			.authorizeHttpRequests((authz) -> authz
				.requestMatchers("/articles").authenticated()
//				.requestMatchers("/admin").hasRole("ADMIN")
				.requestMatchers("/reissue").permitAll()
				.anyRequest().permitAll());


		return http.build();
	}
}
```

# 심화

## RefreshEntity 
```java
@Entity
@Getter
@Setter
public class RefreshEntity {

	@Id @GeneratedValue(strategy = GenerationType.IDENTITY)
	long id;

	private String username;
	private String refresh;
	private String expiration;
}
```
## ReissueController
```java
@RestController
@RequiredArgsConstructor
public class ReissueController {

	private final JWTUtil jwtUtil;
	private final RefreshRepository refreshRepository;

	// TODO 서비스단으로 로직 이동하기
	@PostMapping("/reissue")
	public ResponseEntity<?> reissue(HttpServletRequest request, HttpServletResponse response){

		//get refresh token
		String refresh = null;
		Cookie[] cookies = request.getCookies();
		for (Cookie cookie : cookies) {

			if (cookie.getName().equals("refresh")) {

				refresh = cookie.getValue();
			}
		}

		if (refresh == null) {

			//response status code
			return new ResponseEntity<>("refresh token null", HttpStatus.BAD_REQUEST);
		}

		//expired check
		try {
			jwtUtil.isExpired(refresh);
		} catch (ExpiredJwtException e) {

			//response status code
			return new ResponseEntity<>("refresh token expired", HttpStatus.BAD_REQUEST);
		}

		// 토큰이 refresh인지 확인 (발급시 페이로드에 명시)
		String category = jwtUtil.getCategory(refresh);

		if (!category.equals("refresh")) {

			//response status code
			return new ResponseEntity<>("invalid refresh token", HttpStatus.BAD_REQUEST);
		}

		//DB에 저장되어 있는지 확인
		Boolean isExist = refreshRepository.existsByRefresh(refresh);
		if (!isExist) {

			//response body
			return new ResponseEntity<>("invalid refresh token", HttpStatus.BAD_REQUEST);
		}

		String username = jwtUtil.getUsername(refresh);
		String role = jwtUtil.getRole(refresh);

		//make new JWT
		String newAccess = jwtUtil.createJwt("access", username, role, JwtProperties.ACCESS_TOKEN_TIME);
		String newRefresh = jwtUtil.createJwt("refresh", username, role, JwtProperties.REFRESH_TOKEN_TIME);

		//Refresh 토큰 저장 DB에 기존의 Refresh 토큰 삭제 후 새 Refresh 토큰 저장
		refreshRepository.deleteByRefresh(refresh);
		addRefresh(username, newRefresh);

		//response
		response.setHeader("access", newAccess);
		response.addCookie(createCookie("refresh", newRefresh));


		return new ResponseEntity<>(HttpStatus.OK);
	}

	private void addRefresh(String username, String refresh) {

		Date date = new Date(System.currentTimeMillis() + JwtProperties.REFRESH_TOKEN_TIME);

		RefreshEntity refreshEntity = new RefreshEntity();
		refreshEntity.setUsername(username);
		refreshEntity.setRefresh(refresh);
		refreshEntity.setExpiration(date.toString());

		refreshRepository.save(refreshEntity);
	}

	Cookie createCookie(String key, String value){
		Cookie cookie = new Cookie(key, value);
		cookie.setMaxAge(12*60*60); // 12h
		cookie.setHttpOnly(true);   //JS로 접근 불가, 탈취 위험 감소
		return cookie;
	}
}
```
---

세션 인증방식만 해오던터라 처음 JWT를 공부할때 막막했다. 
하지만 여러 정보와 강의를 보면서 차차 알아갔고 이제 큰틀은 잡힌듯한 느낌이 든다. 
추후 리팩토링을 하면서 JWT + 스프링 시큐리티에 대해 더 깊게 공부해보고 싶다는 생각이 들었다.