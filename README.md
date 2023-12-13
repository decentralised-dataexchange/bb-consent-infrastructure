<h1 align="center">
    GovStack Consent BB Infrastructure
</h1>

<p align="center">
    <a href="/../../commits/" title="Last Commit"><img src="https://img.shields.io/github/last-commit/decentralised-dataexchange/bb-consent-infrastructure?style=flat"></a>
    <a href="/../../issues" title="Open Issues"><img src="https://img.shields.io/github/issues/decentralised-dataexchange/bb-consent-infrastructure?style=flat"></a>
    <a href="./LICENSE" title="License"><img src="https://img.shields.io/badge/License-Apache%202.0-yellowgreen?style=flat"></a>
    <a href="https://artifacthub.io/packages/search?repo=consentbb" title="Artifacthub"><img src="https://img.shields.io/endpoint?url=https://artifacthub.io/badge/repository/consentbb"></a>
</p>

<p align="center">
  <a href="#about">About</a> •
  <a href="#release-status">Release Status</a> •
  <a href="#contributing">Contributing</a> •
  <a href="#licensing">Licensing</a>
</p>

## About

This repository hosts manifests for setting up infrastructure for Consent BB.

## Release Status

Released. Refer to the [wiki page](https://github.com/decentralised-dataexchange/bb-consent-docs/wiki/wps-and-deliverables) for the latest status of the deliverables.

## Requirements

Kubernetes: `>=1.20.0-0`

## Get Repo Info

```bash
helm repo add consentbb https://decentralised-dataexchange.github.io/bb-consent-infrastructure/stable/
helm repo update
```

## Install Chart

**Important:** only helm3 is supported

```bash
helm install [RELEASE_NAME] consentbb/consentbb
```

The command deploys Consent BB on the Kubernetes cluster in the default configuration.

See configuration below for customisation.

## Uninstall Chart

```bash
helm uninstall [RELEASE_NAME]
```

This removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

To see all configurable options with detailed comments, visit the chart's values.yaml, or run these configuration commands:

```bash
helm show values consentbb/consentbb
```
Example values file is provided [here](https://github.com/decentralised-dataexchange/bb-consent-infrastructure/blob/main/example-values.yaml). You can install this file by running below command:
```bash
helm install [RELEASE_NAME] consentbb/consentbb --values example-values.yaml
```

#### Global configuration

```yaml
# Namespace for kubernetes cluster
namespace: consentbb
# Prefix for kubernetes object names
prefix:
# Resource constraints for a pod
resources: {}
# Security context for a pod
securityContext:
  runAsUser: 0
# Storage class name
storageClassName: standard
```

#### Postgres

```yaml
postgres:
  enabled: true
  # Container image
  image: bitnami/postgresql:14.10.0
  # Username
  username: bn_keycloak
  # Password
  password: bn_keycloak
  # Database name
  database: bitnami_keycloak
```


#### Keycloak

```yaml
keycloak:
  enabled: true
  # Container image
  image: docker.io/bitnami/keycloak:22.0.2-debian-11-r0
  # Username
  adminUsername: admin
  # Password
  adminPassword: admin
  # Database user password
  dbPassword: bn_keycloak
  # Ingress
  ingress:
    # Annotations for the ingress
    annotations:
      # Default annotations if ingress is nginx ingress
      # Allow HTTP false
      kubernetes.io/ingress.allow-http: "false"
      # Ingress class as NGINX
      kubernetes.io/ingress.class: nginx
      # Enable CORS
      nginx.ingress.kubernetes.io/enable-cors: "true"
      # Proxy buffer size
      nginx.ingress.kubernetes.io/proxy-buffer-size: 128k
    # Hosts
    hosts:
      - host: test-keycloak.igrant.io
        paths:
          - path: /
            pathType: Prefix
    # TLS
    tls:
      - hosts:
          - test-keycloak.igrant.io
        secretName: tls-secret
```

#### MongoDB

```yaml
mongo:
  enabled: true
  # Container image
  image: bitnami/mongodb:7.0
  username: bb-consent-user
  password: bb-consent-password
  database: bb-consent-consentdb
```

#### API

```yaml
api:
  enabled: true
  # Container image
  image: igrantio/bb-consent-api:2023.11.17
  # Ingress
  ingress:
    # Annotations for the ingress
    annotations:
      # Default annotations if ingress is nginx ingress
      # Allow HTTP false
      kubernetes.io/ingress.allow-http: "false"
      # Ingress class as NGINX
      kubernetes.io/ingress.class: nginx
      # Enable CORS
      nginx.ingress.kubernetes.io/enable-cors: "true"
      # Proxy buffer size
      nginx.ingress.kubernetes.io/proxy-buffer-size: 128k
    # Hosts
    hosts:
      - host: consentbb-api.igrant.io
        paths:
          - path: /
            pathType: Prefix
          - path: /*
            pathType: Prefix
    # TLS
    tls:
      - hosts:
          - consentbb-api.igrant.io
        secretName: tls-secret
  # Configuration
  configuration:
    # Database
    database:
      # Host name
      host:
    TestMode: false
    # Organisation
    Organization:
      # Organisation name
      name:
      # Organisation location
      location:
      # Organisation description
      description:
      # Organisation EULA URL
      eulaUrl:
    # Organisation admin user
    User:
      # Organisation admin username
      username:
      # Organisation admin password
      password:
    # API key secret to generate and verify API keys
    ApiSecretKey:
    # Organisation type/industry sector name
    Type:
      name:
    # Keycloak
    Iam:
      # Keycloak URL accessible from the internet
      url:
      # Keycloak realm name
      realm:
      # Keycloak client id
      ClientId:
    # SMTP server
    Smtp:
      # SMTP username
      username:
      # SMTP password
      password:
      # SMTP host
      host:
      # SMTP port
      port: 587
      # From email
      adminEmail:
    # Webhook
    Webhooks:
      # Webhooks events enabled for subscription
      events:
        - consent.allowed
        - consent.disallowed
    # Privacy dashboard deployment details to be displayed in admin dashboard
    PrivacyDashboardDeployment:
      # Privacy dashboard hostname
      Hostname:
      # Privacy dashboard version
      Version:
    # Data policy
    Policy:
      # Data policy name
      Name:
      # Data policy URL
      Url:
      # Industry sector
      IndustrySector:
      # Geographic restriction
      GeographicRestriction:
      # Storage location
      StorageLocation:
```

#### Admin Dashboard

```yaml
adminDashboard:
  enabled: true
  # Container image
  image: igrantio/bb-consent-admin-dashboard:2023.11.6
  # Ingress
  ingress:
    # Annotations for the ingress
    annotations:
      # Default annotations if ingress is nginx ingress
      # Allow HTTP false
      kubernetes.io/ingress.allow-http: "false"
      # Ingress class as NGINX
      kubernetes.io/ingress.class: nginx
      # Enable CORS
      nginx.ingress.kubernetes.io/enable-cors: "true"
      # Proxy buffer size
      nginx.ingress.kubernetes.io/proxy-buffer-size: 128k
    # Hosts
    hosts:
      - host: consentbb-admin-dashboard.igrant.io
        paths:
          - path: /
            pathType: Prefix
          - path: /*
            pathType: Prefix
    # TLS
    tls:
      - hosts:
          - consentbb-admin-dashboard.igrant.io
        secretName: tls-secret
  # Configuration
  configuration:
    # Consent BB API server base URL
    baseUrl:
    # Admin dashboard version
    appVersion: 2023.11.5
    # Client id as mentioned in Consent BB API server IAM configuration
    clientId:
```

#### Privacy Dashboard

```yaml
privacyDashboard:
  enabled: true
  # Container image
  image: igrantio/bb-consent-privacy-dashboard:2023.11.6
  # Ingress
  ingress:
    # Annotations for the ingress
    annotations:
      # Default annotations if ingress is nginx ingress
      # Allow HTTP false
      kubernetes.io/ingress.allow-http: "false"
      # Ingress class as NGINX
      kubernetes.io/ingress.class: nginx
      # Enable CORS
      nginx.ingress.kubernetes.io/enable-cors: "true"
      # Proxy buffer size
      nginx.ingress.kubernetes.io/proxy-buffer-size: 128k
    # Hosts
    hosts:
      - host: consentbb-privacy-dashboard.igrant.io
        paths:
          - path: /
            pathType: Prefix
          - path: /*
            pathType: Prefix
    # TLS
    tls:
      - hosts:
          - consentbb-privacy-dashboard.igrant.io
        secretName: tls-secret
  # Configuration
  configuration:
    # Consent BB API server base URL
    baseUrl:
    # Redirect URL if external authenticatio mechanism is configured
    # in admin dashboard. This is so that after the individual logins to external IDP,
    # it will redirect back to this URL. It should always be privacy dashboard URL + /login
    # For .e.g https://demo-consent-bb-privacy-dashboard.igrant.io/login
    redirectUrl:
    # Client id as mentioned in Consent BB API server IAM configuration
    clientId:
```


## Other resources

* Wiki - https://github.com/decentralised-dataexchange/consent-dev-docs/wiki

## Contributing

Feel free to improve the plugin and send us a pull request. If you find any problems, please create an issue in this repo.

## Licensing

Copyright (c) 2023-25 LCubed AB (iGrant.io), Sweden

Licensed under the Apache 2.0 License, Version 2.0 (the "License"); you may not use this file except in compliance with the License.

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the LICENSE for the specific language governing permissions and limitations under the License.
