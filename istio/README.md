### Envoy

- when you break up a monotlith application (1 codebase) into micro-services (multiple codebases) you end up with lots of services that need to be able to communicate with each other
- these communications between services need to be fast, reliable, and flexible 
- to implement this you need service mesh
- a service mesh is an infrastructure layer for handling these service-to-service communications
- this is usually implemented using proxies
- envoy is such a proxy
- build originally at Lyft
- envoy is a high performance distributed proxy written in C++
- can be used as an interation of the nginx, haproxy, loadbalancers
- complarable with linkerd

### Envoy Features

- small memory footprint
- http/2 and gRPC support
- advanced loadbalancer features (automatic retries, circuit braking, rate limiting, requests shadowing, zone load balancing)
- dynamically managed using an API
- distributed traces

### Linkerd

- has more features but more memory footprint
- build on top of Netty and Finagle (JVM based)
- integrates with Consul and Zookeeper
- envoy supports hot reloading using an API, linkerd does not (by desing)

### Istio 

- is an open platform to connect, manage, and secure microservices
- istio uses envoy proxy in its data plane
- uses a sidecar deployment
- mixer (control plane) - responsible for enforcing access control and usage policies
- mixer also collects telemetry data from envoy
- pilot (control plane) - responsible for service discovery, traffic management and resiliency; A/B tests and canary deployments; timeouts, retries, circuit brakers
- pilot does this by converting istio rules to envoy configurations
- istio Auth (control plane) - TLS