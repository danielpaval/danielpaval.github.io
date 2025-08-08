---
title: "To Envoy or not to Envoy"
date: 2025-03-05
tags:
  - Envoy
  - Sidecar
  - Reverse Proxy
---

I recently came across the need for a reusable microservice in the form of an OCI / Docker image to meet different authentication and authorization requirements depending on the end project and environment. While the current limited set of possible security configurations is handled by the microservice itself, I analyzed the possibility of delegating this aspect to a specialized sidecar container living in the same Kubernetes pod.

After a bit of prompt engineering and wanting to consider something else than NGINX I decided to first try [Envoy Proxy](https://www.envoyproxy.io/). Long story short, here's a Docker Compose setup:

```yaml
version: '3'
services:
  envoy:
    image: envoyproxy/envoy:dev-c59c8658912ecf0d451e27a2d2f3c1c29feb5b1d
    ports:
      - "10000:10000"
      - "9901:9901"
    volumes:
      - ./envoy.yaml:/etc/envoy/envoy.yaml
```

And the associated `envoy.yaml` configuration that covers:

- JWT validation
- RBAC authorization for a specific role in the roles claim
- A sample Lua script

```yaml
static_resources:
  listeners:
    - name: listener_0
      address:
        socket_address:
          address: 0.0.0.0
          port_value: 10000
      filter_chains:
        - filters:
            - name: envoy.filters.network.http_connection_manager
              typed_config:
                "@type": type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager
                stat_prefix: ingress_http
                route_config:
                  virtual_hosts:
                    - name: backend
                      domains: ["*"]
                      routes:
                        - match:
                            prefix: "/"
                          route:
                            cluster: backend_service
                http_filters:
                  # Ensure a valid JWT is present on the request
                  - name: envoy.filters.http.jwt_authn
                    typed_config:
                      "@type": type.googleapis.com/envoy.extensions.filters.http.jwt_authn.v3.JwtAuthentication
                      providers:
                        oidc_provider:
                          issuer: "<issuer URL>"
                          remote_jwks:
                            http_uri:
                              uri: "<JWKS URL>"
                              cluster: oidc_provider
                              timeout: 5s
                          forward: true
                          from_headers:
                            - name: Authorization
                              value_prefix: "Bearer "
                          payload_in_metadata: jwt_payload
                      rules:
                        - match:
                            prefix: "/"
                          requires:
                            provider_name: oidc_provider
                  # Ensure that the JWT contains a FLOWX_ROLE roles
                  - name: envoy.filters.http.rbac
                    typed_config:
                      "@type": type.googleapis.com/envoy.extensions.filters.http.rbac.v3.RBAC
                      rules:
                        action: ALLOW
                        policies:
                          allow_roles:
                            permissions:
                              - any: true
                            principals:
                              - authenticated:
                                  principal_name:
                                    exact: "*"
                              - metadata:
                                  filter: envoy.filters.http.jwt_authn
                                  path:
                                    - key: jwt_payload
                                    - key: roles
                                  value:
                                    list_match:
                                      one_of:
                                        string_match:
                                          exact: "FLOWX_ROLE"
                  # Sample LUA script
                  - name: envoy.filters.http.lua
                    typed_config:
                      "@type": type.googleapis.com/envoy.extensions.filters.http.lua.v3.Lua
                      inline_code: |
                        function envoy_on_request(handle)
                          handle:logInfo("Incoming request logged by Lua!")
                        end
                  # Final routing
                  - name: envoy.filters.http.router
                    typed_config:
                      "@type": type.googleapis.com/envoy.extensions.filters.http.router.v3.Router
  clusters:
    - name: backend_service
      type: STRICT_DNS
      connect_timeout: 0.25s
      load_assignment:
        cluster_name: backend_service
        endpoints:
          - lb_endpoints:
              - endpoint:
                  address:
                    socket_address:
                      address: host.docker.internal
                      port_value: 8080
    - name: oidc_provider
      type: STRICT_DNS
      connect_timeout: 5s
      transport_socket:
        name: envoy.transport_sockets.tls
        typed_config:
          "@type": type.googleapis.com/envoy.extensions.transport_sockets.tls.v3.UpstreamTlsContext
      load_assignment:
        cluster_name: oidc_provider
        endpoints:
          - lb_endpoints:
              - endpoint:
                  address:
                    socket_address:
                      address: <Authorization server URL>
                      port_value: 443

admin:
  address:
    socket_address: { address: 0.0.0.0, port_value: 9901 }

```

The backend service referenced above, to which Envoy relays requests after the security filters, is the reusable microservice that I mentioned earlier and running on port 8080 on my Docker Desktop host.

One thing that I found Envoy missing out of the box is the ability to check the roles mapping agains the user info or token introspection endpoints of the OIDC provider. The workaround for this would be a custom LUA script to be PoC-ed at a later time.