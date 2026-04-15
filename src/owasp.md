# OWASP Top 10

![OWASP](images/owasp_logo.png)

_Image source: [OWASP.org](https://owasp.org/)_

The **OWASP Top 10** is a widely recognized standard that highlights the most critical security risks affecting modern web applications. It serves as an awareness document for developers, architects, and security teams, helping them understand common vulnerabilities, their impact, and how to mitigate them. Updated periodically based on real‑world data and industry trends, the OWASP Top 10 provides a practical foundation for building more secure software by focusing attention on the threats most likely to be exploited in the wild.

- [Owasp Top 10 - OWASP.org](https://owasp.org/Top10/2025/)

## 1. Broken Access Control

Failures in enforcing permissions allow users to access data or actions they shouldn’t.

**Spring Boot example**   
A controller exposes user details without checking ownership:

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    // No check: user can fetch ANY user
    return userService.findById(id);
}
```

**Fix**  
Use Spring Security method-level authorization:

```java
@PreAuthorize("#id == authentication.principal.id")
```

> Check the `id` against the `sub` claim from JWT.

> [Broken Access Controll - OWASP Top 10](https://owasp.org/Top10/2025/A01_2025-Broken_Access_Control/)

## 2. Cryptographic Failures

Sensitive data is exposed due to missing or weak encryption.

**Spring Boot example**  
Storing passwords in plain text or using MD5:

```java
String hash = DigestUtils.md5DigestAsHex(password.getBytes());
```

**Fix**  
Use BCrypt:

```java
PasswordEncoder encoder = new BCryptPasswordEncoder();
```

> [Cryptographic Failures - OWASP Top 10](https://owasp.org/Top10/2025/A04_2025-Cryptographic_Failures/)

## 3. Injection

Untrusted input is interpreted as code or commands.

**Spring Boot example**  
Using string concatenation in JPA queries:

```java
@Query("SELECT u FROM User u WHERE u.name = '" + name + "'")
```

**Fix**  
Use parameter binding:

```java
@Query("SELECT u FROM User u WHERE u.name = :name")
```

> **XSS (Cross-Site Scripting)** is also an injection attack. It happens when an untrusted input is rendered into a webpage without proper escaping, allowing attackers to execute malicious JavaScript in the victim’s browser. This can lead to session theft, account takeover, redirects, or UI manipulation.

> [Injection](https://owasp.org/Top10/2025/A05_2025-Injection/)

## 4. Insecure Design

Security issues caused by missing or flawed architecture and design decisions.

**Spring Boot example**  
No rate limiting → brute force login possible.

**Fix**

- Spring Cloud Gateway rate limiting
- Cloud Armor rate limiting
- Captcha for login endpoints

> [Insecure Design - OWASP Top 10](https://owasp.org/Top10/2025/A06_2025-Insecure_Design/)

## 5. Security Misconfiguration

Incorrect or missing security settings across applications, servers, or cloud resources.

**Spring Boot example**  
Actuator endpoints exposed publicly

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

**Fix**  
Restrict exposure.

```yaml
include: health,info
```

And secure with Spring Security.

> [Security Misconfiguration - OWASP Top 10](https://owasp.org/Top10/2025/A02_2025-Security_Misconfiguration/)

## 6. Vulnerable and Outdated Components

Using libraries or frameworks with known vulnerabilities.

**Spring Boot example**  
Using vulnerable Log4j version.

**Fix**  
Upgrade to patched versions and use dependency scanning (OWASP DC, Snyk, Trivy).

> [Software Supply Chain Failures](https://owasp.org/Top10/2025/A03_2025-Software_Supply_Chain_Failures/)

## 7. Identification and Authentication Failures

Weak authentication or session handling allows attackers to impersonate users.

**Spring Boot example**  
Session ID not regenerated after login → session fixation.

**Fix**
Spring Security handles this automatically, but only if enabled:

```java
http.sessionManagement().sessionFixation().migrateSession();
```

> Not checking JWT cryptographic signature falls into this categoty.

> [Authentication Failures](https://owasp.org/Top10/2025/A07_2025-Authentication_Failures/)

## 8. Software and Data Integrity Failures

Trusting unvalidated or untrusted code, data, or CI/CD pipelines.

**Spring Boot example**  
CI pipeline pulling dependencies without checksum verification.

**Fix**

- Maven checksum validation
- Signed artifacts
- Secure CI/CD runners

## 9. Security Logging and Monitoring Failures

Insufficient logging or alerting prevents detection of attacks.

**Spring Boot example**  
Login failures not logged.

**Fix**  
Implement logging:

```java
logger.warn("Failed login for user {}", username);
```

Send logs to SIEM (Cloud Logging, ELK, etc.).

> A SIEM (_Security Information and Event Management_) is a centralized system that collects, aggregates, correlates, and analyzes logs from across your infrastructure to detect security threats in real time.

> [Security Logging & Alerting Failures](https://owasp.org/Top10/2025/A09_2025-Security_Logging_and_Alerting_Failures/)

## 10. Server-Side Request Forgery (SSRF)

Server makes unintended internal or external requests controlled by the attacker.

**Spring Boot example**  
Fetching user-supplied URLs:

```java
RestTemplate rest = new RestTemplate();
String result = rest.getForObject(userInputUrl, String.class);
```

**Fix**

- Validate URLs
- Allowlist domains
- Block metadata endpoints (`169.254.169.254`)
- Use Cloud Armor SSRF rules
