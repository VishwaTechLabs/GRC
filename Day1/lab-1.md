# 🛡️ SecureFlow SaaS --- Stage 1: Hands-On Cloud Infrastructure Lab

![GRC
Lab](https://img.shields.io/badge/GRC-Lab-0B5CAD?style=for-the-badge)
![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Amazon
Linux](https://img.shields.io/badge/Amazon_Linux-2023-232F3E?style=for-the-badge&logo=linux&logoColor=white)
![Nginx](https://img.shields.io/badge/Nginx-Web_Server-009639?style=for-the-badge&logo=nginx&logoColor=white)
![Route
53](https://img.shields.io/badge/Route_53-DNS-8C4FFF?style=for-the-badge&logo=amazonaws&logoColor=white)
![TLS](https://img.shields.io/badge/TLS-HTTPS-2E7D32?style=for-the-badge&logo=letsencrypt&logoColor=white)
![CloudWatch](https://img.shields.io/badge/CloudWatch-Monitoring-FF4F8B?style=for-the-badge&logo=amazonaws&logoColor=white)
![Hands
On](https://img.shields.io/badge/HANDS--ON-YES-success?style=for-the-badge)

> **Stage 1 objective:** Build the real cloud foundation for a fictional
> SaaS company called **SecureFlow SaaS**, expose a simple web
> application through a real domain, secure it with HTTPS, and capture
> enough technical evidence to use the environment later for GRC risk
> assessment and control mapping.

------------------------------------------------------------------------

## 🎯 What You Are Building

This stage turns the GRC poster into a **real working environment**.

``` mermaid
flowchart LR
    U[👤 Customer / Browser]
    D[🌐 Real Domain]
    R53[Route 53 DNS]
    SG[🛡️ Security Group]
    EC2[☁️ EC2 - Amazon Linux 2023]
    N[Nginx]
    APP[SecureFlow SaaS Demo App]
    CW[📊 CloudWatch]
    S3[🗄️ S3 Evidence / Backup]

    U -->|HTTPS| D
    D --> R53
    R53 -->|A record| EC2
    SG --> EC2
    EC2 --> N
    N --> APP
    EC2 --> CW
    APP --> S3
```

### Final Stage 1 flow

``` text
User
  |
  | https://your-domain.example
  v
DNS / Route 53
  |
  | resolves hostname
  v
EC2 Public IP
  |
  v
Nginx
  |
  v
SecureFlow SaaS Demo Application
  |
  +---- CloudWatch logs
  |
  +---- S3 evidence / backup
```

------------------------------------------------------------------------

# 🏢 1. Fictional Company

## SecureFlow SaaS

**Business purpose:** SecureFlow SaaS is a fictional cloud-based SaaS
platform that allows business customers to manage users and documents
securely.

### Business capabilities

-   👤 User management
-   🔐 Authentication and authorization
-   📄 Document upload
-   🗄️ Document storage
-   🧾 Audit logging
-   🌐 Secure web access
-   📊 Operational monitoring

### Stage 1 scope

Stage 1 intentionally starts small.

  Component                                  Stage 1
  ----------------------- --------------------------
  Real domain                                     ✅
  Route 53 DNS                                    ✅
  AWS VPC                                         ✅
  EC2                                             ✅
  Amazon Linux 2023                               ✅
  Nginx                                           ✅
  Demo SaaS application                           ✅
  HTTPS/TLS                                       ✅
  Security Group                                  ✅
  IAM role                                        ✅
  CloudWatch                                      ✅
  S3 evidence/backup        Optional but recommended
  RDS                                        Stage 2
  ALB                                        Stage 2
  WAF                                        Stage 2
  CloudFront                                 Stage 2
  GuardDuty                                  Stage 2
  KMS                                        Stage 2
  Secrets Manager                            Stage 2

------------------------------------------------------------------------

# 🧭 2. Learning Outcomes

After completing Stage 1, you should be able to explain and demonstrate:

-   How a real domain is registered.
-   How DNS resolves a domain to AWS infrastructure.
-   How Route 53 hosted zones and records work.
-   How an EC2 instance hosts a web application.
-   How Security Groups control network access.
-   How Nginx acts as the web server/reverse proxy.
-   How HTTPS protects traffic in transit.
-   How certificates are issued and renewed.
-   How IAM roles should be used instead of long-lived AWS access keys
    on EC2.
-   How CloudWatch collects operational/security-relevant logs.
-   How to collect evidence for a future GRC assessment.
-   How a SaaS data flow starts becoming an auditable architecture.

------------------------------------------------------------------------

# 🧰 3. Prerequisites

## AWS

You need an AWS account with permission to create:

-   VPC resources
-   EC2 instances
-   IAM roles
-   Route 53 resources
-   CloudWatch resources
-   S3 resources

> ⚠️ **Cost warning:** AWS services can incur charges. Use the smallest
> practical instance, monitor billing, and delete resources after the
> lab if you do not need them.

Useful official links:

-   [AWS Console](https://console.aws.amazon.com/)
-   [AWS Free Tier](https://aws.amazon.com/free/)
-   [AWS Pricing](https://aws.amazon.com/pricing/)
-   [Amazon EC2 pricing](https://aws.amazon.com/ec2/pricing/)
-   [Amazon Route 53 pricing](https://aws.amazon.com/route53/pricing/)
-   [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)
-   [Amazon CloudWatch
    pricing](https://aws.amazon.com/cloudwatch/pricing/)

## Local workstation

Recommended:

-   SSH client
-   Browser
-   Git
-   AWS CLI
-   Optional: `dig`, `nslookup`, `curl`

------------------------------------------------------------------------

# 🌐 4. Domain Strategy

You need either:

### Option A --- Buy a new domain

Example:

``` text
secureflow-demo.com
```

### Option B --- Use a domain you already own

Example:

``` text
secureflow.your-real-domain.com
```

### Important

Do **not** try to register:

``` text
secureflow.example
```

The `.example` domain is only used as documentation notation.

For a real hands-on lab, replace it with a domain you control.

AWS Route 53 can register domains and can also host DNS for domains
registered elsewhere. When a domain is registered through Route 53, AWS
creates a hosted zone for the domain and assigns name servers.\
Official documentation: [Registering a new domain with Route
53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-register.html)

------------------------------------------------------------------------

# 🏷️ 5. Recommended Naming Convention

Use consistent names because this will become your future GRC asset
inventory.

  Resource               Name
  ---------------------- -----------------------------------
  Project                `secureflow-saas`
  VPC                    `secureflow-vpc`
  Subnet                 `secureflow-public-subnet-1`
  Security Group         `secureflow-web-sg`
  EC2                    `secureflow-web-01`
  IAM Role               `secureflow-ec2-role`
  S3                     `secureflow-evidence-<unique-id>`
  CloudWatch Log Group   `/secureflow/ec2/nginx`
  Domain                 `YOUR_DOMAIN`
  Hostname               `app.YOUR_DOMAIN`

------------------------------------------------------------------------

# 🏗️ 6. Architecture

## Basic architecture

``` text
                    INTERNET
                       |
                       v
             +-------------------+
             |   Your Domain     |
             | app.example.com   |
             +---------+---------+
                       |
                       v
             +-------------------+
             |    Route 53       |
             |       DNS         |
             +---------+---------+
                       |
                       v
             +-------------------+
             |   AWS VPC         |
             |                   |
             |  Public Subnet    |
             |       |           |
             |       v           |
             |  +----------+     |
             |  |   EC2    |     |
             |  | Amazon   |     |
             |  | Linux 23 |     |
             |  +----+-----+     |
             |       |           |
             |       v           |
             |    Nginx          |
             |       |           |
             |       v           |
             | SecureFlow App    |
             +-------------------+
```

------------------------------------------------------------------------

# ☁️ 7. Create the AWS VPC

You can use the **VPC console** and choose a simple VPC configuration.

Recommended lab network:

``` text
VPC
CIDR: 10.10.0.0/16

Public Subnet
CIDR: 10.10.1.0/24

Internet Gateway
Attached to VPC
```

For Stage 1, one public subnet is acceptable because the objective is a
simple demonstrable web workload.

> Production SaaS architecture should normally use a more resilient
> multi-AZ design. That is intentionally deferred to Stage 2.

### VPC documentation

[Amazon VPC User
Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)

------------------------------------------------------------------------

# 🌍 8. Create the Internet Gateway

Create:

``` text
secureflow-igw
```

Attach it to:

``` text
secureflow-vpc
```

The Internet Gateway provides a path between the VPC and the internet.

------------------------------------------------------------------------

# 🛣️ 9. Configure the Public Route Table

Create or use:

``` text
secureflow-public-rt
```

Route:

``` text
Destination       Target
0.0.0.0/0         Internet Gateway
```

Associate the route table with:

``` text
10.10.1.0/24
```

------------------------------------------------------------------------

# 🔐 10. Create the Security Group

Name:

``` text
secureflow-web-sg
```

## Inbound rules

Recommended starting point:

  Protocol     Port Source              Purpose
  ---------- ------ ------------------- --------------------
  SSH            22 Your public IP/32   Administration
  HTTP           80 0.0.0.0/0           Redirect to HTTPS
  HTTPS         443 0.0.0.0/0           Secure web traffic

### 🚨 Do NOT use

``` text
SSH 22 → 0.0.0.0/0
```

unless you intentionally use it as a temporary security-control
demonstration.

The GRC lab should demonstrate the **secure state**, not leave the
vulnerable state running.

### AWS Security Group documentation

[Security groups for your
VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)

------------------------------------------------------------------------

# 🖥️ 11. Launch EC2

Recommended:

``` text
Name:
secureflow-web-01

AMI:
Amazon Linux 2023

Instance type:
Smallest practical instance for your workload

Network:
secureflow-vpc

Subnet:
secureflow-public-subnet-1

Auto-assign Public IPv4:
Enabled

Security Group:
secureflow-web-sg

IAM Role:
secureflow-ec2-role
```

Amazon Linux 2023 is AWS's current Amazon Linux generation.\
Official documentation: [Amazon Linux
Documentation](https://docs.aws.amazon.com/linux/)

------------------------------------------------------------------------

# 👤 12. Create the EC2 IAM Role

Create:

``` text
secureflow-ec2-role
```

Attach only the permissions required by the workload.

For the initial lab, if you plan to use Systems Manager, attach:

``` text
AmazonSSMManagedInstanceCore
```

Avoid creating an IAM user and storing permanent AWS access keys on the
EC2 machine.

### GRC principle

``` text
Requirement
     ↓
Least privilege
     ↓
IAM role
     ↓
Temporary credentials
     ↓
Reduced credential exposure
```

### IAM documentation

[IAM documentation](https://docs.aws.amazon.com/IAM/latest/UserGuide/)

------------------------------------------------------------------------

# 🔑 13. Connect to EC2

You can use:

-   SSH
-   AWS Systems Manager Session Manager

### SSH example

``` bash
ssh -i your-key.pem ec2-user@<EC2_PUBLIC_IP>
```

Verify:

``` bash
whoami
hostname
uname -a
cat /etc/os-release
```

Expected:

``` text
ec2-user
secureflow-web-01
Amazon Linux 2023
```

------------------------------------------------------------------------

# 🛡️ 14. Initial Server Hardening

Update packages:

``` bash
sudo dnf update -y
```

Check listening ports:

``` bash
sudo ss -tulpn
```

Check running services:

``` bash
systemctl --type=service --state=running
```

Check disk:

``` bash
df -h
```

Check memory:

``` bash
free -h
```

Check OS:

``` bash
cat /etc/os-release
```

### Evidence to capture

Take screenshots/output showing:

-   OS version
-   hostname
-   package update
-   open ports
-   running services

These become future **GRC evidence artifacts**.

------------------------------------------------------------------------

# 🌐 15. Install Nginx

On Amazon Linux 2023:

``` bash
sudo dnf install nginx -y
```

Start it:

``` bash
sudo systemctl enable --now nginx
```

Check:

``` bash
sudo systemctl status nginx
```

Test locally:

``` bash
curl http://localhost
```

Test from your browser:

``` text
http://<EC2_PUBLIC_IP>
```

You should see the Nginx default page.

### Nginx documentation

[nginx.org](https://nginx.org/en/docs/)

------------------------------------------------------------------------

# 🧪 16. Create the SecureFlow SaaS Demo Application

For Stage 1, a simple HTML application is enough.

Create:

``` bash
sudo mkdir -p /var/www/secureflow
```

Create the page:

``` bash
sudo tee /var/www/secureflow/index.html > /dev/null <<'EOF'
<!DOCTYPE html>
<html>
<head>
    <title>SecureFlow SaaS</title>
    <meta charset="UTF-8">
</head>
<body>
    <h1>SecureFlow SaaS</h1>
    <h2>GRC Lab - Stage 1</h2>

    <p>Welcome to the SecureFlow SaaS demonstration environment.</p>

    <h3>Application Capabilities</h3>
    <ul>
        <li>User Management</li>
        <li>Document Management</li>
        <li>Role-Based Access Control</li>
        <li>Audit Logging</li>
        <li>Secure HTTPS Access</li>
    </ul>

    <p>Environment: AWS EC2</p>
    <p>Web Server: Nginx</p>
</body>
</html>
EOF
```

------------------------------------------------------------------------

# ⚙️ 17. Configure Nginx for SecureFlow

Create:

``` bash
sudo vi /etc/nginx/conf.d/secureflow.conf
```

Configuration:

``` nginx
server {
    listen 80;
    server_name app.YOUR_DOMAIN;

    root /var/www/secureflow;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    access_log /var/log/nginx/secureflow_access.log;
    error_log  /var/log/nginx/secureflow_error.log;
}
```

Replace:

``` text
app.YOUR_DOMAIN
```

with your real hostname.

Test configuration:

``` bash
sudo nginx -t
```

Reload:

``` bash
sudo systemctl reload nginx
```

------------------------------------------------------------------------

# 🌐 18. Configure Route 53

Open:

**Route 53 → Hosted zones → Your domain**

Create:

``` text
Type:
A

Name:
app

Value:
EC2 Public IPv4 address

TTL:
300
```

Result:

``` text
app.YOUR_DOMAIN
        |
        v
Route 53
        |
        v
EC2 Public IP
        |
        v
Nginx
```

AWS documentation:

[Routing internet traffic for your
domain](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/welcome-dns-service.html)

------------------------------------------------------------------------

# 🔎 19. Test DNS

From your workstation:

``` bash
nslookup app.YOUR_DOMAIN
```

or:

``` bash
dig app.YOUR_DOMAIN
```

Test:

``` bash
curl -I http://app.YOUR_DOMAIN
```

Expected:

``` text
HTTP/1.1 200 OK
```

------------------------------------------------------------------------

# 🔐 20. HTTPS / TLS

This is one of the most important parts of the lab.

You have two good approaches.

## Option A --- Nginx + Let's Encrypt

This is the simplest approach when Nginx terminates TLS directly on EC2.

Use Certbot and its Route 53 DNS integration.

Official resources:

-   [Let's Encrypt](https://letsencrypt.org/)
-   [Certbot](https://certbot.eff.org/)
-   [Certbot Route 53
    documentation](https://eff-certbot.readthedocs.io/en/stable/using.html#dns-plugins)

The objective is:

``` text
HTTP :80
   |
   v
Redirect
   |
   v
HTTPS :443
   |
   v
Nginx
   |
   v
SecureFlow SaaS
```

### Certificate evidence

Capture:

``` bash
sudo openssl s_client -connect app.YOUR_DOMAIN:443 -servername app.YOUR_DOMAIN
```

Also check:

``` bash
curl -Iv https://app.YOUR_DOMAIN
```

Record:

-   Certificate issuer
-   Subject
-   SAN
-   Expiration date
-   TLS protocol
-   Cipher
-   Certificate chain

------------------------------------------------------------------------

# 🟦 Option B --- AWS ACM + Application Load Balancer

For a more AWS-native architecture, use:

``` text
User
 |
HTTPS
 |
Route 53
 |
ALB
 |
ACM Certificate
 |
HTTP/HTTPS
 |
EC2
 |
Nginx
 |
Application
```

AWS recommends DNS validation for ACM certificates when you can manage
the DNS records. ACM supplies a CNAME record used to prove domain
control, and the validation record can support automatic renewal while
it remains in DNS.\
Official documentation: [ACM DNS
validation](https://docs.aws.amazon.com/acm/latest/userguide/dns-validation.html)

Request certificate:

``` text
YOUR_DOMAIN
*.YOUR_DOMAIN
```

Choose:

``` text
Validation:
DNS
```

ACM public certificates must have domain ownership/control validated
before issuance.\
Official documentation: [Validate domain ownership for ACM
certificates](https://docs.aws.amazon.com/acm/latest/userguide/domain-ownership-validation.html)

### Important architecture decision

If you use **ALB + ACM**, the public TLS termination occurs at the ALB.

If you use **Nginx + Let's Encrypt**, TLS terminates directly on the
EC2/Nginx host.

For **Stage 1**, Option A is simpler.

For **Stage 2**, build the ALB + ACM architecture.

------------------------------------------------------------------------

# 🔁 21. Force HTTPS

After HTTPS is working, configure HTTP to redirect.

Example:

``` nginx
server {
    listen 80;
    server_name app.YOUR_DOMAIN;

    return 301 https://$host$request_uri;
}
```

Test:

``` bash
curl -I http://app.YOUR_DOMAIN
```

Expected:

``` text
HTTP/1.1 301 Moved Permanently
Location: https://app.YOUR_DOMAIN/...
```

------------------------------------------------------------------------

# 🧪 22. TLS Security Validation

Run:

``` bash
curl -Iv https://app.YOUR_DOMAIN
```

Check:

``` text
TLS version
Certificate
Certificate chain
HTTP status
```

Optional:

``` bash
openssl s_client \
  -connect app.YOUR_DOMAIN:443 \
  -servername app.YOUR_DOMAIN
```

You can also use:

-   [SSL Labs SSL Server Test](https://www.ssllabs.com/ssltest/)
-   [Mozilla Observatory](https://observatory.mozilla.org/)

> Only scan systems and domains you own or have authorization to test.

------------------------------------------------------------------------

# 📊 23. Install CloudWatch Agent

The CloudWatch agent can collect metrics and logs from EC2 instances and
provides deeper monitoring than basic EC2 monitoring.\
Official documentation: [CloudWatch
Agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html)

Recommended logs:

``` text
/var/log/messages
/var/log/nginx/access.log
/var/log/nginx/error.log
```

Create a log group:

``` text
/secureflow/ec2/nginx
```

Recommended retention for a lab:

``` text
7 or 14 days
```

Choose a longer retention only when you intentionally want to
demonstrate a retention policy.

------------------------------------------------------------------------

# 📝 24. Nginx Logging

Your Nginx configuration should generate:

``` text
Access logs
Error logs
```

Check:

``` bash
sudo tail -f /var/log/nginx/access.log
```

and:

``` bash
sudo tail -f /var/log/nginx/error.log
```

Generate traffic:

``` bash
curl https://app.YOUR_DOMAIN
```

You should see the request in the access log.

------------------------------------------------------------------------

# 🗄️ 25. Create an Evidence S3 Bucket

For the GRC project, create a dedicated bucket such as:

``` text
secureflow-evidence-<unique-id>
```

Recommended security settings:

``` text
Block Public Access:
ON

Bucket versioning:
ON

Encryption:
ON
```

Do not upload customer or production-sensitive information to this
training bucket.

Use it for:

``` text
evidence/
architecture/
screenshots/
logs/
configuration/
risk-register/
```

Example:

``` text
secureflow-evidence/
│
├── architecture/
├── dns/
├── ec2/
├── nginx/
├── tls/
├── iam/
├── cloudwatch/
└── grc/
```

------------------------------------------------------------------------

# 📋 26. Start Your GRC Evidence Register

Create this table in your project documentation.

  Evidence ID   Control Area       Evidence               Status
  ------------- ------------------ ---------------------- --------
  SF-001        Asset Management   EC2 inventory          ⬜
  SF-002        Network Security   Security Group         ⬜
  SF-003        DNS                Route 53 record        ⬜
  SF-004        Server Security    Amazon Linux version   ⬜
  SF-005        Web Security       Nginx configuration    ⬜
  SF-006        Cryptography       TLS certificate        ⬜
  SF-007        Access Control     IAM role               ⬜
  SF-008        Logging            CloudWatch logs        ⬜
  SF-009        Monitoring         CloudWatch dashboard   ⬜
  SF-010        Backup             S3 evidence            ⬜

Do not mark a control **PASS** simply because you created the resource.

The future GRC process will ask:

``` text
Control requirement
       ↓
Implementation
       ↓
Evidence
       ↓
Testing
       ↓
PASS / FAIL / PARTIAL
```

------------------------------------------------------------------------

# 🔍 27. Create an Asset Inventory

This becomes your first GRC asset register.

  ---------------------------------------------------------------------------------------
  Asset ID    Asset         Type            Owner         Environment       Criticality
  ----------- ------------- --------------- ------------- ----------------- -------------
  SF-A001     SecureFlow    DNS             Platform      Production-like   High
              domain                                                        

  SF-A002     Route 53      DNS             Platform      Production-like   High
              hosted zone                                                   

  SF-A003     EC2 web       Compute         Platform      Production-like   High
              server                                                        

  SF-A004     Nginx         Web server      Platform      Production-like   High

  SF-A005     SecureFlow    Application     Engineering   Production-like   High
              application                                                   

  SF-A006     TLS           Cryptographic   Security      Production-like   High
              certificate   asset                                           

  SF-A007     CloudWatch    Logging         Security      Production-like   Medium
              logs                                                          

  SF-A008     S3 evidence   Storage         Security      GRC               Medium
              bucket                                                        
  ---------------------------------------------------------------------------------------

------------------------------------------------------------------------

# 🌊 28. Create the Initial Data Flow

For Stage 1:

``` text
Customer
   |
   | HTTPS
   | Login / application requests
   v
SecureFlow Domain
   |
   | DNS
   v
Route 53
   |
   | IP resolution
   v
EC2
   |
   v
Nginx
   |
   v
SecureFlow Application
   |
   +----> Application logs
   |
   +----> CloudWatch
   |
   +----> S3 evidence/backup
```

### Data classification

Start with:

  Data                         Classification
  ---------------------------- -------------------------------------------
  Username                     Internal
  Application configuration    Internal
  Authentication information   Confidential
  Customer documents           Confidential
  TLS private key              Sensitive
  IAM credentials              Sensitive
  Audit logs                   Internal / Sensitive depending on content

------------------------------------------------------------------------

# ⚠️ 29. Initial Risk Register

  ------------------------------------------------------------------------------------
  Risk ID        Risk               Likelihood     Impact         Initial Control
  -------------- ------------------ -------------- -------------- --------------------
  R-001          Unauthorized SSH   Medium         High           Restrict SG source
                 access                                           

  R-002          HTTP traffic       Medium         High           TLS + HTTPS redirect
                 interception                                     

  R-003          Certificate        Medium         High           Certificate
                 expiration                                       monitoring/renewal

  R-004          Excessive IAM      Medium         High           Least privilege
                 privilege                                        

  R-005          Missing            Medium         Medium         CloudWatch
                 application logs                                 

  R-006          Data exposure in   Medium         High           Block public access
                 S3                                               

  R-007          Unpatched EC2      Medium         High           Patch management

  R-008          DNS                Low            High           Route 53 governance
                 misconfiguration                                 
  ------------------------------------------------------------------------------------

This is **not yet the final GRC assessment**.

It is your Stage 1 starting risk register.

------------------------------------------------------------------------

# 🧪 30. Security Validation Checklist

Run these tests.

## DNS

``` bash
dig app.YOUR_DOMAIN
```

Expected:

``` text
Correct public IP / target
```

## HTTP

``` bash
curl -I http://app.YOUR_DOMAIN
```

Expected:

``` text
301 redirect
```

## HTTPS

``` bash
curl -I https://app.YOUR_DOMAIN
```

Expected:

``` text
200 OK
```

## Nginx

``` bash
sudo nginx -t
```

Expected:

``` text
syntax is ok
test is successful
```

## Service

``` bash
sudo systemctl is-enabled nginx
sudo systemctl is-active nginx
```

Expected:

``` text
enabled
active
```

## Ports

``` bash
sudo ss -tulpn
```

Expected public web ports:

``` text
80
443
```

SSH should be restricted through the Security Group.

------------------------------------------------------------------------

# 🧾 31. Evidence Collection Checklist

Capture screenshots or command output for:

-   [ ] AWS account/region used for lab
-   [ ] VPC
-   [ ] Subnet
-   [ ] Internet Gateway
-   [ ] Route table
-   [ ] Security Group
-   [ ] EC2 instance
-   [ ] EC2 IAM role
-   [ ] Amazon Linux version
-   [ ] Nginx installation
-   [ ] Nginx configuration
-   [ ] Route 53 hosted zone
-   [ ] DNS A record
-   [ ] Domain resolution
-   [ ] TLS certificate
-   [ ] HTTPS test
-   [ ] HTTP-to-HTTPS redirect
-   [ ] CloudWatch log group
-   [ ] Nginx logs
-   [ ] S3 bucket
-   [ ] S3 Block Public Access
-   [ ] S3 encryption
-   [ ] Asset inventory
-   [ ] Initial risk register
-   [ ] Data flow diagram

------------------------------------------------------------------------

# 🧑‍💻 32. Suggested GitHub Repository

Create:

``` text
secureflow-saas-grc-lab
```

Recommended structure:

``` text
secureflow-saas-grc-lab/
│
├── README.md
│
├── architecture/
│   ├── architecture.md
│   └── data-flow.md
│
├── application/
│   └── index.html
│
├── nginx/
│   └── secureflow.conf
│
├── scripts/
│   ├── install-nginx.sh
│   ├── hardening-check.sh
│   └── validation.sh
│
├── evidence/
│   ├── dns/
│   ├── ec2/
│   ├── nginx/
│   ├── tls/
│   ├── iam/
│   └── cloudwatch/
│
└── grc/
    ├── asset-register.md
    ├── risk-register.md
    └── evidence-register.md
```

GitHub:

[GitHub](https://github.com/)

------------------------------------------------------------------------

# 🏆 33. Stage 1 Definition of Done

Stage 1 is complete only when all of these are true:

### Infrastructure

-   [ ] AWS VPC created
-   [ ] Public subnet created
-   [ ] Internet Gateway attached
-   [ ] Route table configured
-   [ ] EC2 deployed
-   [ ] Security Group configured
-   [ ] IAM role configured

### Application

-   [ ] Amazon Linux 2023 running
-   [ ] Nginx installed
-   [ ] SecureFlow demo application deployed
-   [ ] Application accessible

### DNS

-   [ ] Domain registered or existing domain delegated
-   [ ] Route 53 hosted zone configured
-   [ ] DNS record created
-   [ ] DNS resolution verified

### TLS

-   [ ] Certificate issued
-   [ ] HTTPS enabled
-   [ ] HTTP redirected to HTTPS
-   [ ] Certificate details captured
-   [ ] Renewal strategy documented

### Monitoring

-   [ ] CloudWatch configured
-   [ ] Nginx access logs available
-   [ ] Nginx error logs available
-   [ ] Basic EC2 monitoring enabled

### GRC

-   [ ] Asset register created
-   [ ] Initial data flow created
-   [ ] Data classification started
-   [ ] Risk register created
-   [ ] Evidence register created
-   [ ] Screenshots/evidence captured

------------------------------------------------------------------------

# 🥇 34. Stage 1 Achievement Badge

When all checks pass, mark:

![Stage 1
Complete](https://img.shields.io/badge/STAGE_1-COMPLETE-success?style=for-the-badge)

``` text
╔══════════════════════════════════════════╗
║     🛡️ SECUREFLOW SaaS GRC LAB          ║
║                                          ║
║       STAGE 1 — COMPLETED                ║
║                                          ║
║  ☑ Cloud Infrastructure                  ║
║  ☑ Domain & DNS                          ║
║  ☑ EC2 & Linux                            ║
║  ☑ Nginx                                  ║
║  ☑ Demo SaaS Application                  ║
║  ☑ HTTPS / TLS                            ║
║  ☑ IAM                                    ║
║  ☑ Monitoring                             ║
║  ☑ Evidence Collection                    ║
║                                          ║
║  BUILD → SECURE → OBSERVE → EVIDENCE     ║
╚══════════════════════════════════════════╝
```

------------------------------------------------------------------------

# 📚 35. Official Documentation Links

## AWS

-   [AWS Documentation](https://docs.aws.amazon.com/)
-   [Amazon EC2](https://docs.aws.amazon.com/ec2/)
-   [Amazon Linux 2023](https://docs.aws.amazon.com/linux/)
-   [Amazon VPC](https://docs.aws.amazon.com/vpc/)
-   [IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/)
-   [Route
    53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/)
-   [CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)
-   [S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/)
-   [AWS Certificate
    Manager](https://docs.aws.amazon.com/acm/latest/userguide/)

## Web / TLS

-   [Nginx Documentation](https://nginx.org/en/docs/)
-   [Let's Encrypt](https://letsencrypt.org/)
-   [Certbot](https://certbot.eff.org/)
-   [SSL Labs](https://www.ssllabs.com/ssltest/)
-   [Mozilla Observatory](https://observatory.mozilla.org/)

## GRC

-   [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
-   [NIST SP
    800-53](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final)
-   [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
-   [ISO/IEC
    27001](https://www.iso.org/isoiec-27001-information-security.html)
-   [AICPA
    SOC](https://www.aicpa-cima.com/resources/landing/system-and-organization-controls-soc-suite-of-services)

------------------------------------------------------------------------

# 🧠 36. What You Should Be Able to Explain in an Interview

After completing this stage, you should be able to answer:

### Cloud

**Q: Why did you choose EC2?**

> For Stage 1, EC2 provides a simple and transparent compute layer where
> I can demonstrate operating-system hardening, Nginx configuration,
> application deployment, logging, and evidence collection.

### DNS

**Q: What happens when the customer enters your domain?**

``` text
Browser
 ↓
DNS resolver
 ↓
Route 53
 ↓
A/alias record
 ↓
AWS endpoint / EC2
 ↓
Nginx
 ↓
Application
```

### Security

**Q: How did you protect the server?**

> I restricted administrative access through the Security Group, exposed
> only required web ports, used HTTPS, applied OS updates, used an IAM
> role rather than permanent AWS credentials, and enabled centralized
> monitoring.

### GRC

**Q: Why is this a GRC lab?**

> Because I am not only deploying infrastructure. I am identifying
> assets, mapping the data flow, classifying data, identifying risks,
> implementing controls, collecting evidence, and preparing the
> environment for control testing and framework mapping.

### Certificate

**Q: How do you manage certificates?**

> I use a defined certificate lifecycle: request → validate → issue →
> deploy → monitor → renew → retire. For Stage 1 I demonstrate public
> HTTPS; later stages extend this into centralized certificate lifecycle
> management.

------------------------------------------------------------------------

# 🚀 37. What Comes Next

Once Stage 1 is successfully completed, **do not immediately destroy
it**.

Stage 2 will build the security layer around this foundation:

``` text
STAGE 1
Cloud + Domain + Nginx + App + HTTPS
                 │
                 ▼
STAGE 2
IAM + KMS + Secrets + WAF + GuardDuty
                 │
                 ▼
STAGE 3
RDS + S3 + Backup + DR + Monitoring
                 │
                 ▼
STAGE 4
Data Flow + Classification + Threat Modeling
                 │
                 ▼
STAGE 5
Risk Register + Control Mapping
                 │
                 ▼
STAGE 6
ISO 27001 + NIST CSF + SOC 2 + CIS
                 │
                 ▼
STAGE 7
Evidence + Control Testing + Remediation
                 │
                 ▼
🏆 END-TO-END GRC LAB
```

------------------------------------------------------------------------

## 🏁 Final Goal

The final project should allow you to demonstrate:

> **"I designed and implemented a fictional SaaS environment in AWS,
> mapped its infrastructure and data flows, secured the workload using
> cloud and application security controls, implemented HTTPS and
> certificate management, centralized monitoring and evidence
> collection, and then used that environment as the foundation for GRC
> risk assessment and compliance control mapping."**

That is the difference between **"I know GRC concepts"** and **"I can
demonstrate GRC using a working cloud environment."**

------------------------------------------------------------------------

## 🔖 Project Status

**Current stage:** Stage 1 --- Hands-On Cloud Infrastructure

**Status:** `NOT STARTED`

**Target:** Working SecureFlow SaaS environment with DNS + EC2 + Nginx +
HTTPS + monitoring + initial GRC evidence.

**Next milestone:** Complete every checkbox in **Definition of Done**
before moving to Stage 2.
