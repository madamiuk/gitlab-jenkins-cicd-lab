# GitLab + Jenkins CI/CD Lab

A practical **CI/CD laboratory built from scratch on Debian 13**, integrating GitLab CE, Jenkins and Nginx into a complete automated deployment workflow.

The project covers not only installation and configuration, but also real-world administration topics such as TLS, access control, protected branches, webhooks, Linux permissions, troubleshooting and service auditing.

> 📘 **Full technical documentation:**
> **https://madamiuk.github.io/gitlab-jenkins-cicd-lab/**

---

## 🎯 Project Goal

The goal of this laboratory was to build a working CI/CD environment capable of automatically deploying a simple web application after changes are merged into the main branch.

The original requirements included:

* configuring an additional 20 GB disk as Linux SWAP,
* installing Jenkins,
* installing GitLab CE,
* creating a Git repository with an example HTML application,
* configuring Jenkins to retrieve the latest source code,
* deploying the application to `/var/www`,
* publishing the application through Nginx.

The environment was later extended with HTTPS, a private Certificate Authority, GitLab webhooks, protected branches and a Merge Request workflow.

---

## 🏗️ Architecture

```text
Developer
    │
    │ feature branch / push
    ▼
GitLab CE
    │
    │ Merge Request
    ▼
Protected main branch
    │
    │ HTTPS Webhook
    ▼
Jenkins
    │
    │ deployment
    ▼
/var/www/web-demo
    │
    ▼
Nginx
    │
    ▼
Web Application
```

---

## 🛠️ Technology Stack

| Component        | Technology                      |
| ---------------- | ------------------------------- |
| Operating System | Debian GNU/Linux 13             |
| Architecture     | ARM64 / aarch64                 |
| Source Control   | Git                             |
| Git Server       | GitLab CE 19.4.1                |
| CI/CD            | Jenkins 2.580.1                 |
| Reverse Proxy    | Nginx                           |
| Java Runtime     | OpenJDK 21                      |
| TLS              | Private CA + X.509 certificates |
| Deployment       | Bash / Jenkins                  |
| Web Application  | HTML                            |
| Access Control   | GitLab roles + Protected Branch |
| Automation       | GitLab Webhook → Jenkins        |

---

## 🔄 CI/CD Workflow

The final workflow follows a controlled development process:

```text
Developer
    │
    ▼
feature branch
    │
    ▼
GitLab
    │
    ▼
Merge Request
    │
    ▼
Protected main
    │
    ▼
GitLab Webhook
    │
    ▼
Jenkins
    │
    ▼
Deployment
    │
    ▼
Nginx
```

A developer cannot push directly to the protected `main` branch.

Changes must be submitted through a feature branch and Merge Request.

After the Merge Request is accepted, GitLab automatically sends an HTTPS webhook to Jenkins.

Jenkins retrieves the new version of the repository and deploys the application to:

```text
/var/www/web-demo
```

Nginx immediately serves the updated version.

---

## 🔐 Security

Several security mechanisms were implemented as part of the laboratory.

### HTTPS

GitLab and Jenkins are available through Nginx reverse proxy using certificates issued by a private laboratory Certificate Authority.

```text
https://gitlab.domain
https://jenkins.domain
```

The private CA is trusted by both the client system and GitLab.

TLS verification remains enabled.

### Jenkins access to GitLab

Jenkins uses a dedicated **GitLab Project Access Token** with minimal permissions:

```text
Role: Reporter
Scope: read_repository
```

### GitLab Webhook

Communication in the opposite direction uses a separate webhook Secret Token:

```text
GitLab → HTTPS Webhook → Jenkins
```

### Protected Branch

The `main` branch is protected.

A Developer can:

```text
create branch
      ↓
push changes
      ↓
create Merge Request
```

but cannot directly push to `main`.

---

## 🧪 Deployment Test

The web application evolved through several versions:

```text
1.0
 ↓
1.1
 ↓
1.2
 ↓
1.3
```

The final version was deployed using the complete workflow:

```text
feature branch
      ↓
Merge Request
      ↓
merge to main
      ↓
GitLab webhook
      ↓
Jenkins build
      ↓
automatic deployment
      ↓
Nginx
      ↓
Version 1.3
```

No manual Jenkins build was required.

---

## 🔍 Troubleshooting

The laboratory intentionally documents not only the final configuration but also the problems encountered during implementation.

Examples include:

* DNS configuration after switching from DHCP to static IPv4,
* Jenkins and GitLab port conflicts,
* integration of GitLab with an external Nginx instance,
* Linux group permissions for Jenkins deployments,
* GitLab outbound request restrictions for private network destinations,
* TLS trust between GitLab and Jenkins,
* Protected Branch push rejection,
* GitLab Alertmanager port exposure analysis.

One particularly interesting issue involved the GitLab → Jenkins webhook.

Although both applications worked correctly from the client, GitLab initially rejected the webhook destination because:

```text
jenkins.domain → 192.168.64.21
```

resolved to a private network address.

Instead of globally allowing GitLab access to the local network, only the required destination was allowed:

```text
jenkins.domain:443
```

The private laboratory CA was also added to the GitLab trust store.

After both conditions were satisfied:

```text
Outbound destination allowed
            +
Private CA trusted
            ↓
Webhook executed successfully: HTTP 200
```

---

## 📊 Final Result

```text
[PASS] Additional 20 GB disk → Linux SWAP
[PASS] Jenkins → HTTPS
[PASS] GitLab CE → HTTPS
[PASS] Git repository + HTML application
[PASS] Jenkins repository checkout
[PASS] Automatic deployment to /var/www
[PASS] Nginx → HTTP 200
[PASS] GitLab Webhook → Jenkins
[PASS] Protected Branch
[PASS] Merge Request workflow
```

### Result

**All original requirements completed + automated CI/CD bonus implemented.**

---

## 📚 Full Documentation

The complete technical documentation contains **13 chapters** describing the environment from initial Debian configuration through the final CI/CD tests.

It includes:

* commands used during installation,
* configuration files,
* verification procedures,
* troubleshooting,
* architecture diagrams,
* security decisions,
* port and service audit,
* final acceptance tests.

👉 **[Open the full CI/CD Lab documentation](https://madamiuk.github.io/gitlab-jenkins-cicd-lab/)**

---

## 🚀 Future Improvements

Possible next steps for this laboratory include:

* Jenkins Pipeline as Code using `Jenkinsfile`,
* infrastructure automation with Ansible,
* centralized monitoring,
* firewall hardening,
* GitLab and Jenkins backup procedures,
* separate DEV / TEST / PROD environments,
* automated application testing before deployment.

---

## 👤 Author

**Miroslaw Adamiuk**

Linux System Engineer focused on Linux administration, infrastructure automation, CI/CD and DevOps technologies.

GitHub: **[@madamiuk](https://github.com/madamiuk)**
