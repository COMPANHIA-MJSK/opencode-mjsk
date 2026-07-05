# Cybersecurity Skill

## Purpose
Expert security practices, secure coding, vulnerability assessment, and compliance. Covers OWASP Top 10, penetration testing, and security architecture.

## OWASP Top 10 2021

### A01: Broken Access Control
```javascript
// BAD
app.get('/api/users/:id', (req, res) => {
  const user = db.findUser(req.params.id);
  res.json(user); // No auth check!
});

// GOOD
app.get('/api/users/:id', authenticate, (req, res) => {
  if (req.user.id !== req.params.id && !req.user.isAdmin) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  const user = db.findUser(req.params.id);
  res.json(user);
});
```

### A02: Cryptographic Failures
```python
# BAD
password = "user123"  # Hardcoded
key = "secret-key-123"  # Weak key

# GOOD
import os
from cryptography.fernet import Fernet

key = Fernet.generate_key()  # Secure random key
cipher = Fernet(key)

# Password hashing
from passlib.context import CryptContext
pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
hashed = pwd_context.hash(password)
```

### A03: Injection
```javascript
// BAD - SQL Injection
const query = `SELECT * FROM users WHERE id = ${req.params.id}`;

// GOOD - Parameterized Query
const query = 'SELECT * FROM users WHERE id = $1';
const result = await db.query(query, [req.params.id]);

// GOOD - Use ORM
const user = await User.findByPk(req.params.id);
```

### A05: Security Misconfiguration
```yaml
# GOOD - Secure Docker
FROM node:20-alpine
RUN addgroup -g 1001 -S nodejs && adduser -S nodejs -u 1001
USER nodejs
EXPOSE 3000
CMD ["node", "server.js"]
```

## Secure Coding Practices

### Input Validation
```python
from pydantic import BaseModel, validator

class UserInput(BaseModel):
    email: str
    age: int
    
    @validator('email')
    def validate_email(cls, v):
        if '@' not in v:
            raise ValueError('Invalid email')
        return v.lower()
    
    @validator('age')
    def validate_age(cls, v):
        if v < 0 or v > 150:
            raise ValueError('Invalid age')
        return v
```

### Authentication & Authorization
```javascript
// JWT Authentication
const jwt = require('jsonwebtoken');

function authenticate(req, res, next) {
  const token = req.headers.authorization?.split(' ')[1];
  if (!token) return res.status(401).json({ error: 'No token' });
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (err) {
    res.status(401).json({ error: 'Invalid token' });
  }
}

// Role-based access
function authorize(...roles) {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Forbidden' });
    }
    next();
  };
}
```

### Rate Limiting
```python
from flask_limiter import Limiter

limiter = Limiter(app, key_func=get_remote_address)

@app.route("/api/login", methods=["POST"])
@limiter.limit("5 per minute")
def login():
    # Login logic
    pass
```

## Vulnerability Assessment

### Static Analysis (SAST)
- SonarQube
- Semgrep
- Bandit (Python)
- ESLint Security Plugin

### Dynamic Analysis (DAST)
- OWASP ZAP
- Burp Suite
- Nikto

### Dependency Scanning
- Snyk
- Dependabot
- npm audit
- safety (Python)

## Security Headers
```javascript
// Express.js Security Headers
const helmet = require('helmet');
app.use(helmet());

// Content Security Policy
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'", "'unsafe-inline'"],
    styleSrc: ["'self'", "'unsafe-inline'"],
    imgSrc: ["'self'", "data:", "https:"],
  },
}));
```

## Compliance Standards
- **GDPR**: Data protection, right to be forgotten
- **PCI DSS**: Payment card data security
- **HIPAA**: Healthcare data protection
- **SOC 2**: Service organization controls
- **ISO 27001**: Information security management

## Best Practices
- Follow principle of least privilege
- Implement defense in depth
- Validate all inputs
- Use parameterized queries
- Encrypt sensitive data at rest and in transit
- Implement proper logging and monitoring
- Regular security audits and penetration testing
- Keep dependencies updated
