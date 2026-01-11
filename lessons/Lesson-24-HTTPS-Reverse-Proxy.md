# Lesson 24: Local HTTPS, Reverse Proxy & Monitoring

## Objective

Add local TLS using mkcert; configure a reverse proxy (Traefik/Caddy); add monitoring and health checks.

---

## In-Class Materials

### Topics Covered

1. **Local HTTPS with mkcert**:
   - Generating self-signed certificates
   - Adding certificates to system trust store
   - Configuring services to use HTTPS
2. **Reverse Proxy**:
   - Traefik or Caddy for routing
   - Path-based routing (e.g., `/api` → backend)
   - Load balancing and SSL termination
3. **Service Discovery**:
   - DNS resolution within Docker network
   - Service names as hostnames
4. **Monitoring**:
   - Health check endpoints
   - Container resource usage
   - Basic uptime monitoring (Uptime Kuma optional)
5. **Networking**:
   - Docker bridge networks
   - Port binding and exposure
   - Internal vs external routes

### Key Concepts

- **TLS/SSL**: Encrypted communication (HTTPS)
- **Certificate**: Digital document proving identity
- **Reverse Proxy**: Routes requests to backend services
- **Health Check**: Endpoint to verify service is running

---

## In-Class Exercise (15–20 minutes)

**Task: Add HTTPS with mkcert and configure Caddy reverse proxy**

1. Install mkcert:

   ```bash
   # On macOS
   brew install mkcert

   # On Linux (Ubuntu/Debian)
   sudo apt-get install mkcert

   # On Windows
   choco install mkcert
   ```

2. Create local certificates:

   ```bash
   mkcert -install
   mkcert localhost 127.0.0.1 "*.local"
   ```

   - Creates `localhost+2.pem` (cert) and `localhost+2-key.pem` (key)

3. Create Caddy config (`Caddyfile`):

   ```caddyfile
   http://localhost:8080 https://localhost:8443 {
     tls /path/to/localhost+2.pem /path/to/localhost+2-key.pem

     # Frontend
     handle / {
       reverse_proxy frontend:80
     }

     # Backend API
     handle /api/* {
       reverse_proxy backend:80
     }

     # Health check endpoint
     handle /health {
       respond "OK"
     }
   }
   ```

4. Update `docker-compose.yml` to include Caddy:

   ```yaml
   version: '3.8'

   services:
     caddy:
       image: caddy:2.6-alpine
       ports:
         - "80:80"
         - "443:443"
       volumes:
         - ./Caddyfile:/etc/caddy/Caddyfile:ro
         - ./localhost+2.pem:/certs/localhost+2.pem:ro
         - ./localhost+2-key.pem:/certs/localhost+2-key.pem:ro
         - caddy_data:/data
         - caddy_config:/config
       depends_on:
         - frontend
         - backend
       networks:
         - app-network

     frontend:
       # ... existing config ...
       networks:
         - app-network

     backend:
       # ... existing config ...
       networks:
         - app-network

     db:
       # ... existing config ...
       networks:
         - app-network

   volumes:
     caddy_data:
     caddy_config:
     postgres_data:

   networks:
     app-network:
       driver: bridge
   ```

5. Add health check endpoint to backend (`Controllers/HealthController.cs`):

   ```csharp
   using Microsoft.AspNetCore.Mvc;

   namespace TaskApi.Controllers;

   [ApiController]
   [Route("[controller]")]
   public class HealthController : ControllerBase
   {
       [HttpGet]
       public IActionResult Get()
       {
           return Ok(new { status = "healthy", timestamp = DateTime.UtcNow });
       }
   }
   ```

6. Update docker-compose health check:

   ```yaml
   backend:
     # ... existing config ...
     healthcheck:
       test: ["CMD", "curl", "-f", "http://localhost:80/health"]
       interval: 10s
       timeout: 3s
       retries: 3
   ```

7. Add local monitoring with Uptime Kuma (optional):

   ```yaml
   uptime-kuma:
     image: louislam/uptime-kuma:1
     ports:
       - "3001:3001"
     volumes:
       - uptime-kuma-data:/app/data
     networks:
       - app-network

   volumes:
     uptime-kuma-data:
   ```

8. Run the stack:

   ```bash
   docker-compose up --build
   ```

9. Access services:
   - Frontend: <https://localhost:8443>
   - API: <https://localhost:8443/api/tasks>
   - Health: <https://localhost:8443/health>

10. Monitor container stats:

    ```bash
    docker stats
    ```

11. View logs:

    ```bash
    docker-compose logs -f caddy
    docker-compose logs -f backend
    ```

12. Commit your work:

    ```bash
    git add Caddyfile docker-compose.yml
    git commit -m "Add HTTPS with mkcert and Caddy reverse proxy with monitoring"
    ```

---

## Homework Assignment

**Reading & Practice**: SSL/TLS and Reverse Proxies

**Tasks**:

1. **Read**: [mkcert Documentation](https://github.com/FiloSottile/mkcert)
2. **Read**: [Caddy Documentation](https://caddyserver.com/docs/)
3. **Read**: [Traefik vs Caddy Comparison](https://docs.traefik.io/)
4. **Practice**: Generate certificates and verify HTTPS connection
5. **Practice**: Test reverse proxy routing between services

**Optional Challenge**:

- Set up Let's Encrypt for production HTTPS
- Implement Traefik instead of Caddy
- Add request/response logging middleware

---

## Homework Reference Materials

| Topic | Link | Notes |
|-------|------|-------|
| mkcert | [mkcert GitHub](https://github.com/FiloSottile/mkcert) | Local certs |
| Caddy | [Caddy Docs](https://caddyserver.com/docs/) | Reverse proxy |
| Traefik | [Traefik Docs](https://docs.traefik.io/) | Alternative proxy |
| HTTPS Best Practices | [OWASP — Transport Layer](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html) | Security |
| Uptime Kuma | [Uptime Kuma GitHub](https://github.com/louislam/uptime-kuma) | Monitoring |

---

## Key Takeaways

✓ Generated local HTTPS certificates with mkcert  
✓ Configured Caddy as reverse proxy  
✓ Routed requests to frontend and backend  
✓ Implemented health check endpoints  
✓ Set up container monitoring and logging  

---

## Next Lesson Preview

**Lesson 25 — Final Demo & Next Steps**: Students present their work and plan future learning.
