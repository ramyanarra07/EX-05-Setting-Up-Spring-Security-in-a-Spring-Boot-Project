# EXP05-Setting-Up-Spring-Security-in-a-Spring-Boot-Project
# Name: NARRA RAMYA
# Register Number: 212223040128
## AIM:
To write a program for setting up Spring Security in a Spring Boot project to secure endpoints with basic authentication and role-based access control.

## ALGORITHM:
Create a Spring Boot Project with the following dependencies:

Spring Web

Spring Security

Spring Boot DevTools (optional)

Add Spring Security dependency in pom.xml (if not using Spring Initializr).

Create a configuration class extending WebSecurityConfigurerAdapter (or using SecurityFilterChain for newer Spring versions).

Define an in-memory user with username, password, and roles using UserDetailsService.

Secure your REST endpoints using annotations or in the security config class.

Run and test the app using a browser or Postman:

Secure endpoints will prompt for username and password.

## PROGRAM CODE:
### pom.xml 
```

    <dependencies>
        <!-- Spring Boot Starter Web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Spring Boot Starter Security -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

        <!-- Spring Boot DevTools (Optional) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <!-- Spring Boot Starter Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- Spring Security Test -->
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```
### SecurityConfig.java (Spring Boot 3.x / Spring Security 6+)
```
package com.example.ex5;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .csrf(csrf -> csrf.disable()) // Disable CSRF for simple REST testing
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers("/public").permitAll()
                        .requestMatchers("/admin").hasRole("ADMIN")
                        .anyRequest().authenticated()
                )
                .httpBasic(Customizer.withDefaults()); // Enable HTTP Basic Authentication

        return http.build();
    }

    @Bean
    public UserDetailsService userDetailsService() {
        UserDetails regularUser = User.withDefaultPasswordEncoder()
                .username("user")
                .password("password")
                .roles("USER")
                .build();

        UserDetails adminUser = User.withDefaultPasswordEncoder()
                .username("admin")
                .password("admin123")
                .roles("ADMIN")
                .build();

        return new InMemoryUserDetailsManager(regularUser, adminUser);
    }
}
```

### HelloController.java
```
package com.example.ex5;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    // Accessible by anyone (unauthenticated)
    @GetMapping("/public")
    public String publicEndpoint() {
        return "This is a public endpoint. No authentication required!";
    }

    // Accessible by authenticated users with USER or ADMIN role
    @GetMapping("/private")
    public String privateEndpoint() {
        return "This is a secured endpoint. You are authenticated!";
    }

    // Accessible only by users with ADMIN role
    @GetMapping("/admin")
    public String adminEndpoint() {
        return "Welcome Admin! This endpoint requires ADMIN authority.";
    }
}
```

# Output:

## GET - public end point - No Auth
<img width="1673" height="940" alt="image" src="https://github.com/user-attachments/assets/816080c1-1840-4562-836d-76733fbbd660" />


## GET - private end point - No Auth
<img width="1672" height="941" alt="image" src="https://github.com/user-attachments/assets/03810643-41c7-43a8-ac1e-5ac97b35d36b" />


## private End Point with valid Auth
<img width="1672" height="941" alt="image" src="https://github.com/user-attachments/assets/32cefc08-cda3-45d7-8f74-e490ea33c3e5" />


## Admin End Point as Regular User (Forbidden)
<img width="1672" height="941" alt="image" src="https://github.com/user-attachments/assets/b74f07e8-9c4e-4a4a-ab27-fe210808b424" />


## Admin Endpoint as Admin User (Authorized)
<img width="1675" height="939" alt="image" src="https://github.com/user-attachments/assets/fc731bc6-4c42-4842-abda-872a40790f00" />



# Result:
RESULT
The Spring Boot application was successfully configured with Spring Security to secure REST endpoints using HTTP Basic Authentication and Role-Based Access Control (RBAC).
