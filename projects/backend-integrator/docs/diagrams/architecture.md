# backend-integrator - architecture

*This documentation is from the private repository backend-integrator.*

---

# Architecture Diagram: Counter + Roller + CloudInjector

```
                                     DEVELOPER
                                         |
                                         v
                                +-----------------+
                                | YAML Config     |
                                | counter.yaml    |
                                | roller.yaml     |
                                | cloud.yaml      |
                                +-----------------+
                                         |
                                         v
                  +------------------------------------------------+
                  |                                                |
                  |           Infra-Builder Framework              |
                  |                                                |
+-----------------+----------------+    +--------------------------+    +-------------------------+
|                                  |    |                          |    |                         |
|        Counter Engine            |    |       Roller Engine      |    |     CloudInjector      |
|                                  |    |                          |    |                         |
| - Parse counter.yaml             |    | - Parse roller.yaml      |    | - Parse cloud.yaml     |
| - Select templates               |    | - Hash JWT & passwords   |    | - Generate nginx conf  |
| - Generate:                      |    | - Generate:              |    | - Generate:            |
|   * API Hooks                    |    |   * AuthContext          |    |   * docker-compose.yml |
|   * NavFlow                      |    |   * RouteGuards          |    |   * certbot.sh         |
|   * Page Pipelines               |    |   * Role Middleware      |    |   * deploy.sh          |
|   * Route Skeletons              |    |   * Hash Store           |    |                         |
|                                  |    |                          |    |                         |
+-----------------+----------------+    +--------------------------+    +-------------------------+
                  |                                |                              |
                  v                                v                              v
+-----------------+----------------+----------------+------------------------------+
|                                                                                 |
|                            /generated Directory                                 |
|                                                                                 |
| Frontend:                                   Backend:                            |
| +-----------+                              +----------+                         |
| | api_hooks |                              | routes   |                         |
| | NavFlow   |                              | role_mw  |                         |
| | Auth      |                              | hash     |                         |
| +-----------+                              +----------+                         |
|                                                                                 |
| Infrastructure:                                                                 |
| +---------------+                                                               |
| | nginx.conf    |                                                               |
| | docker-compose|                                                               |
| | certbot.sh    |                                                               |
| | deploy.sh     |                                                               |
| +---------------+                                                               |
|                                                                                 |
+-----------------+--------------------------------------------------------+------+
                  |                                                         |
                  v                                                         v
+----------------------------------+              +-----------------------------------+
|            Frontend App          |              |            Backend App            |
| +----------------------------+   |              |  +---------------------------+    |
| | import { AuthProvider }    |   |              |  | import { apiRoutes,      |    |
| | import NavFlow             |   |              |  |         roleMiddleware } |    |
| | import PagePipelines       |   |              |  |                          |    |
| | import RouteGuards         |   |              |  | app.use('/api',          |    |
| |                            |   |              |  |   roleMiddleware,        |    |
| | <AuthProvider>             |   |              |  |   apiRoutes);           |    |
| |   <NavFlow/>               |   |              |  |                          |    |
| | </AuthProvider>            |   |              |  +---------------------------+    |
| +----------------------------+   |              |                                   |
+----------------------------------+              +-----------------------------------+
                                                                |
                                                                v
                                                  +---------------------------+
                                                  |   Running Application     |
                                                  |                           |
                                                  | +---------------------+   |
                                                  | | User Interface      |   |
                                                  | +---------------------+   |
                                                  |          |                |
                                                  |          v                |
                                                  | +---------------------+   |
                                                  | | Security Layer      |   |
                                                  | +---------------------+   |
                                                  |          |                |
                                                  |          v                |
                                                  | +---------------------+   |
                                                  | | API & Data Layer    |   |
                                                  | +---------------------+   |
                                                  +---------------------------+
```

## Data Flow Sequence

1. Developer defines application structure in YAML configuration files
2. Counter Engine processes page/component/API mappings from counter.yaml
3. Roller Engine processes security settings from roller.yaml
4. CloudInjector processes infrastructure settings from cloud.yaml
5. All engines render templates into the /generated directory
6. Developer imports generated code into their frontend and backend applications
7. Developer runs the deploy.sh script to set up infrastructure
8. Complete application runs with all connections, security, and infrastructure automated
