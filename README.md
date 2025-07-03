# Haproxy-architecture

flowchart TD
    subgraph Clients
        C1[Client 1]
        C2[Client 2]
        C3[Client 3]
    end

    subgraph LoadBalancer
        LB1[HAProxy - LB1]
    end

    subgraph WebServers
        A[Server A - 192.168.1.11]
        B[Server B - 192.168.1.12]
        C[Server C - 192.168.1.13]
        D[Server D - 192.168.1.14]
    end

    subgraph Database
        DB[(Database - 192.168.1.2)]
    end

    C1 -->|HTTP Request| LB1
    C2 -->|HTTP Request| LB1
    C3 -->|HTTP Request| LB1

    LB1 -->|Cookie: SERVERID=A| A
    LB1 -->|Cookie: SERVERID=B| B
    LB1 -->|Cookie: SERVERID=C| C
    LB1 -->|Cookie: SERVERID=D| D

    A --> DB
    B --> DB
    C --> DB
    D --> DB
```

### Explanation:
This Mermaid diagram illustrates a simple HTTP load-balancing architecture using HAProxy with cookie insertion for session persistence.

**Components:**
- **Clients**: Multiple users making HTTP requests to the system.
- **LB1 (HAProxy)**: Acts as the load balancer, listening on 192.168.1.1.
- **Web Servers (A, B, C, D)**: Handle the actual HTTP requests. Cookies (`SERVERID`) inserted by HAProxy ensure that repeat requests from a client go to the same server.
- **Database**: A shared backend database server used by all web servers.

**How it works:**
1. Clients send HTTP requests to HAProxy.
2. HAProxy balances the request using roundrobin and inserts a cookie named `SERVERID` in the response.
3. Future requests from the client will include the cookie.
4. HAProxy uses the cookie to route the request to the same backend server.
5. If a server is down, HAProxy redirects the request to another live server and re-assigns the cookie.

**Advanced Configuration Notes:**
- `option httpchk` enables health checks to determine server availability.
- `option httpclose` disables HTTP keep-alive to make session analysis easier.
- `option forwardfor` adds original client IP in `X-Forwarded-For` header.

Let me know if you'd like diagrams for the other architecture variants (VRRP high availability, Stunnel+HAProxy, multi-site, etc.).
