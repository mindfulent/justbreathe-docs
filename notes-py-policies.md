# CORS and COOP Best Practices for FastAPI Development

## Overview

This guide covers implementing Cross-Origin Resource Sharing (CORS) and Cross-Origin Opener Policy (COOP) in a FastAPI application during local development.

## CORS Configuration

CORS controls how web applications on different domains interact. For localhost development, proper CORS configuration enables communication between frontend and backend services.

### Basic CORS Setup

Import required modules and set up the FastAPI application with CORS middleware:

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

origins = [
    "http://localhost",
    "http://localhost:3000",
    "http://127.0.0.1",
    "http://127.0.0.1:3000"
]

app.add_middleware(
    CORSMiddleware,
    allow_origins=origins,
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### Best Practices for CORS

1. **Explicit Origins**: Reference our current implementation:

```python:backend/app/main.py
startLine: 133
endLine: 139
```

2. **Environment Variables**: Store allowed origins in environment variables:
```python:backend/app/core/config.py
startLine: 32
endLine: 37
```

3. **Security Headers**: Implement security headers as shown in:
```python:backend/app/main.py
startLine: 142
endLine: 157
```

## COOP Implementation

Cross-Origin Opener Policy (COOP) helps isolate your site from other origins, preventing cross-origin attacks.

### Setting COOP Headers

Our current COOP implementation can be found in:
```nginx:frontend/nginx.conf
startLine: 10
endLine: 12
```

### COOP Best Practices

1. **Security Levels**:
   - `same-origin`: Highest security, complete isolation
   - `same-origin-allow-popups`: Less restrictive, allows popups
   - `unsafe-none`: Least restrictive, use only when necessary

2. **Google OAuth Compatibility**: 
   Reference our current implementation that handles Google OAuth:

```javascript:frontend/src/config/csp.js
startLine: 1
endLine: 10
```

## Additional Security Considerations

### 1. Input Validation
- Implement strict validation for all API endpoints
- Use Pydantic models for request/response validation
- Sanitize user inputs

### 2. Rate Limiting
- Implement rate limiting for API endpoints
- Consider using FastAPI's built-in rate limiting tools
- Monitor and log rate limit violations

### 3. HTTPS Configuration
- Use HTTPS even in development
- Generate self-signed certificates for testing
- Ensure proper SSL/TLS configuration

## Testing and Debugging

1. **Browser Tools**:
   - Use Chrome DevTools Network tab
   - Monitor CORS preflight requests
   - Check security headers

2. **Application Logging**:
   - Log CORS-related events
   - Track security header impacts
   - Monitor authentication flows

3. **Cross-Browser Testing**:
   - Test in multiple browsers
   - Verify CORS behavior consistently
   - Validate security headers

## Production Considerations

1. **Environment-Specific Configuration**:
   - Strict CORS policies
   - Production-grade security headers
   - Limited allowed origins

2. **Monitoring**:
   - Log security events
   - Track CORS violations
   - Monitor authentication attempts

3. **Regular Updates**:
   - Keep dependencies updated
   - Review security policies
   - Update allowed origins as needed