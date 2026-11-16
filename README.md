# Microservice Security Demo

This repository contains a collection of intentionally vulnerable microservices designed for security testing and demonstration purposes. Each microservice contains various security vulnerabilities that are commonly found in real-world applications.

## Structure

The microservices are organized into the following groups:

### E-Commerce Services

- `product-catalog`: Product management and inventory service
- `payment-processor`: Payment processing and transaction service
- `user-authentication`: User management and authentication service

### Analytics Services

- `data-collector`: Data collection and aggregation service
- `report-generator`: Report generation and visualization service

### Infrastructure Services

- `service-registry`: Service discovery and registration
- `config-manager`: Configuration management service

## Security Vulnerabilities

Each microservice contains intentional security vulnerabilities for demonstration purposes, including but not limited to:

### Application Vulnerabilities

- SQL Injection
- Cross-Site Scripting (XSS)
- Insecure Deserialization
- Broken Authentication
- Sensitive Data Exposure
- XML External Entities (XXE)
- Broken Access Control
- Security Misconfiguration
- Using Components with Known Vulnerabilities
- Insufficient Logging & Monitoring

### Infrastructure as Code Vulnerabilities

- Public access to resources (S3, RDS, EC2)
- Overly permissive IAM policies and roles
- Hardcoded credentials and secrets
- Missing encryption configurations
- Open security groups and network access
- No VPC or network segmentation
- Disabled security features
- Weak password policies
- Missing authentication controls
- Excessive service permissions

## Security Scanning

This repository includes automated security scanning using Checkmarx One. The configuration is in `.github/workflows/checkmarx-scan.yml`, and the file is commented line by line so it can be used as a walkthrough.

### When Scans Run

The workflow runs:

- On every push to the `main` branch
- On every pull request to the `main` branch
- Manually via workflow dispatch

### How the Workflow Works

The workflow has two jobs.

1. **`detect-changes`** compares the commit against its base and builds a list of the service folders that contain changed files.
2. **`checkmarx-scan`** runs one scan for each folder on that list. The scans run in parallel as a matrix, and a failure in one scan does not cancel the others.

A service that has no changes is not scanned. If no service folder changed, the scan job is skipped.

### What Gets Scanned

| Situation | Services scanned |
| --- | --- |
| Push or pull request that changes files in some service folders | Only those services |
| Push or pull request that changes the workflow file itself | All services |
| Manual run (workflow dispatch) | All services |
| Push or pull request where no base commit can be determined, such as a new branch or a force push | All services |

### Scan Settings

- **Pull requests** use an incremental SAST scan (`--sast-incremental`), which scans only changed code. Pushes to `main` run a full scan.
- **Superseded pull request runs** are canceled when a newer commit is pushed. Runs on `main` are never canceled.
- **SAST preset** is `Base Preset`.
- **Action version** is pinned to a specific release of `checkmarx/ast-github-action` instead of `@main`, so scans are repeatable. The version is noted in a comment in the workflow file.

### How Results Appear in Checkmarx One

Each microservice is scanned independently as its own Checkmarx One project.

- **Project name:** the folder path, for example `e-commerce/product-catalog`.
- **Project tag:** `monorepo:<service-name>`, for example `monorepo:product-catalog`.

### Required Secrets

To run the security scans, the following secrets need to be configured in your GitHub repository:

- `REGION`: Your Checkmarx One region. The workflow uses it as the subdomain in `https://<REGION>.checkmarx.net`.
- `TENANT`: Your Checkmarx One tenant name
- `CLIENT_ID`: Your Checkmarx One client ID
- `CLIENT_SECRET`: Your Checkmarx One client secret

### Adding a New Microservice

The list of scanned services is defined once, in the `FOLDERS` array in the `detect-changes` job of `.github/workflows/checkmarx-scan.yml`. To add a service, create its folder and add the folder path to that array. No other workflow changes are needed.

## Warning

⚠️ **IMPORTANT**: These services are intentionally vulnerable and should ONLY be used in isolated environments for security testing and educational purposes. DO NOT deploy these services in production or expose them to the internet.
