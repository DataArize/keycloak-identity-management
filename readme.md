 # Keycloak Setup

This repository contains the configuration for a production-ready Keycloak server, which is used for user authentication and authorization in the Netflix-inspired platform architecture.

## Overview

Keycloak is an open-source identity and access management solution that provides authentication and authorization services for applications and services. This setup includes a StatefulSet deployment on Kubernetes, configured with TLS for secure communications.

## Prerequisites

Before deploying the Keycloak server, ensure you have the following:

- A running Kubernetes cluster (GKE or other)
- `kubectl` configured to access your cluster
- Access to create secrets and ConfigMaps in your Kubernetes cluster

## Deployment

The Keycloak server is deployed using Kustomize with the base manifests located in the `k8s/base` directory and overlays in the `k8s/overlay/development` directory.

### Steps to Deploy

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/DataArize/keycloak-identity-management.git
   cd keycloak-identity-management
   ```
2. **Create Required Secrets:**

    Keycloak requires several secrets to be created for secure operation. These secrets should not be stored in version control for security reasons. Below are the secrets that need to be created:
    - KC_BOOTSTRAP_ADMIN_USERNAME
    - KC_BOOTSTRAP_ADMIN_PASSWORD
    - KC_DB_USERNAME
    - KC_DB_PASSWORD
    - KC_HTTPS_CERTIFICATE_FILE
    - KC_HTTPS_CERTIFICATE_KEY_FILE

     **Use the following command to generate the secrets:**
     ```bash
   kubectl create secret generic keycloak-secrets \
    --namespace default \
    --from-literal=KC_BOOTSTRAP_ADMIN_USERNAME=<base64-encoded-admin-username> \
    --from-literal=KC_BOOTSTRAP_ADMIN_PASSWORD=<base64-encoded-admin-password> \
    --from-literal=KC_DB_USERNAME=<base64-encoded-db-username> \
    --from-literal=KC_DB_PASSWORD=<base64-encoded-db-password> \
    --from-literal=KC_HTTPS_CERTIFICATE_FILE=<base64-encoded-certificate-file-path> \
    --from-literal=KC_HTTPS_CERTIFICATE_KEY_FILE=<base64-encoded-certificate-key-file-path>

     ```  
     ``` bash
        kubectl create secret tls keycloak-tls \
       --cert=path/to/certificate.crt \
       --key=path/to/private.key
     ```
3. **Configure the Database:**

    Ensure that the database for Keycloak is set up and accessible. Update the KC_DB_URL in the keycloak-configmap accordingly.
4. **Deploy Keycloak with Kustomize:**

    Deploy the Keycloak setup using Kustomize:
    ```bash
   kubectl apply -k k8s/overlay/development
   ```
   This command will apply the configurations defined in the kustomization.yaml file located in the k8s/overlay/development directory.

## SSL Certificates
This setup requires SSL certificates for secure communication. You will need to generate these certificates and create secrets in Kubernetes as outlined in the "Create Required Secrets" section above. Consider using tools like Let's Encrypt for generating certificates.

## Accessing Keycloak
Once deployed, you can access the Keycloak admin console at:
```bash
https://<your-domain>:8443/auth/admin
```
Use the admin credentials you specified in the secrets to log in.

## Security Considerations

- **Secrets Management:** Ensure that sensitive information (like passwords and certificates) is not exposed in version control. Use Kubernetes Secrets for managing sensitive data.
- **Access Control:** Limit access to the Keycloak admin console to trusted IP addresses or users.
- **Regular Updates:** Keep Keycloak and its dependencies up-to-date to mitigate vulnerabilities.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

Keycloak Team for their outstanding open-source identity management solution.
