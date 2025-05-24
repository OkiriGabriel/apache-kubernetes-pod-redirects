# Apache Redirects in Kubernetes

This project demonstrates how to set up Apache with custom redirects in a Kubernetes cluster using ConfigMaps and Ingress.

## Project Structure

```
apache-config/
├── configmap.yaml        # ConfigMap for redirect rules
├── deployment.yaml       # Kubernetes Deployment and Service
├── httpd.conf           # Base Apache configuration
├── ingress.yaml         # Ingress configuration
├── main-configmap.yaml  # ConfigMap for main Apache config
└── redirects.conf       # Apache redirect rules
```

## Features

- Apache server running in Kubernetes
- Custom redirect rules using Apache's mod_rewrite
- Configuration managed through Kubernetes ConfigMaps
- Exposed via Kubernetes Ingress
- Support for both simple and complex redirects

## Redirect Types Supported

1. Simple Redirects:
   ```apache
   Redirect 301 /old-page /new-page
   Redirect 302 /temporary /permanent
   ```

2. Pattern-based Redirects:
   ```apache
   RewriteRule ^old-url/(.*)$ /new-url/$1 [R=301,L]
   ```

3. Query Parameter Redirects:
   ```apache
   RewriteCond %{QUERY_STRING} ^id=123$
   RewriteRule ^old-product$ /new-product? [R=301,L]
   ```

## Prerequisites

- Kubernetes cluster
- kubectl configured
- Ingress controller installed in your cluster

## Installation

1. Apply the ConfigMaps:
   ```bash
   kubectl apply -f apache-config/configmap.yaml
   kubectl apply -f apache-config/main-configmap.yaml
   ```

2. Deploy the Apache server:
   ```bash
   kubectl apply -f apache-config/deployment.yaml
   ```

3. Set up the Ingress:
   ```bash
   kubectl apply -f apache-config/ingress.yaml
   ```

## Configuration

### Customizing Redirects

Edit `apache-config/redirects.conf` to add or modify redirect rules. The file supports:
- Simple redirects using `Redirect` directive
- Complex redirects using `RewriteRule`
- Query parameter-based redirects
- Pattern matching

### Updating Configuration

After modifying any configuration files:
1. Update the corresponding ConfigMap
2. Restart the Apache deployment:
   ```bash
   kubectl rollout restart deployment apache-redirects
   ```

## Accessing the Service

### Local Development
1. Add to /etc/hosts:
   ```bash
   echo "127.0.0.1 apache.example.com" | sudo tee -a /etc/hosts
   ```

2. Test redirects:
   ```bash
   curl -I -H "Host: apache.example.com" http://localhost/old-page
   ```

### Production
1. Update the host in `ingress.yaml` to your domain
2. Configure DNS to point to your Ingress controller
3. Test using your domain:
   ```bash
   curl -I https://your-domain.com/old-page
   ```

## Troubleshooting

1. Check pod status:
   ```bash
   kubectl get pods -l app=apache-redirects
   ```

2. View Apache logs:
   ```bash
   kubectl logs -l app=apache-redirects
   ```

3. Check Ingress status:
   ```bash
   kubectl get ingress apache-redirects-ingress
   ```

## Security Considerations

- The configuration includes basic security settings
- Directory access is restricted by default
- Consider adding SSL/TLS for production use
- Review and adjust security settings based on your needs

## Contributing

Feel free to submit issues and enhancement requests! 