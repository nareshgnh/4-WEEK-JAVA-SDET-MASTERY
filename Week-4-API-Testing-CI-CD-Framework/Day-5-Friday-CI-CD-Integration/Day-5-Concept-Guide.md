# DAY 5: CI/CD INTEGRATION

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Understand CI/CD fundamentals and why SDETs need this knowledge
- [ ] Set up Jenkins locally using Docker
- [ ] Create declarative Jenkins pipelines for API tests
- [ ] Build GitHub Actions workflows for automated testing
- [ ] Implement scheduled test execution (cron jobs)
- [ ] Configure test reporting and notifications (Slack/email)

**Time Required:** 2.5-3 hours
**Difficulty:** Medium
**Prerequisite:** Days 1-4 (RestAssured, Maven, API testing basics)

---

## 📚 Core Concepts

### Concept 1: What is CI/CD and Why It Matters for SDETs

**What is it?**
CI/CD stands for Continuous Integration and Continuous Deployment/Delivery. It's a practice where code changes are automatically built, tested, and deployed to production environments.

- **CI (Continuous Integration):** Developers merge code to main branch frequently (multiple times per day). Each merge triggers automated builds and tests.
- **CD (Continuous Delivery):** Code is always in a deployable state. Deployment to production requires manual approval.
- **CD (Continuous Deployment):** Every code change that passes tests is automatically deployed to production (no manual approval).

**Why does it matter for automation?**
As an SDET in product companies:
- **Tests run automatically:** No manual execution needed
- **Fast feedback:** Developers know within minutes if their code breaks tests
- **Prevents broken deployments:** Tests act as quality gates
- **Shift-left testing:** Bugs caught early in the development cycle
- **Interview expectation:** 90% of product companies expect CI/CD knowledge for SDET roles

**Real Example:**
```java
// Developer commits code at 2:00 PM
// CI/CD Pipeline automatically:
// 1. Pulls latest code from GitHub (2:01 PM)
// 2. Runs Maven build (2:02 PM)
// 3. Executes API tests via RestAssured (2:03 PM)
// 4. Generates test report (2:04 PM)
// 5. Sends Slack notification: "Build #47 PASSED ✅" (2:05 PM)
// Developer gets feedback in 5 minutes instead of next day!
```

**CI/CD Benefits for SDETs:**
| Benefit | Impact |
|---------|--------|
| **Automated execution** | No manual test runs |
| **Parallel execution** | Tests run on multiple environments simultaneously |
| **Consistent environment** | Same Docker container every time |
| **Scheduled runs** | Nightly smoke tests, weekend regression |
| **Immediate feedback** | Slack/email notifications |
| **Test metrics** | Pass rate trends, flaky test detection |

---

### Concept 2: CI/CD Pipeline Fundamentals

**What is a Pipeline?**
A pipeline is a series of automated steps that execute sequentially or in parallel to build, test, and deploy code.

**Typical API Testing Pipeline:**
```
1. Trigger (Push/PR/Schedule)
   ↓
2. Checkout Code (Clone GitHub repo)
   ↓
3. Setup Environment (Install Java, Maven)
   ↓
4. Build Project (mvn clean install)
   ↓
5. Run Tests (mvn test)
   ↓
6. Generate Reports (Allure/Extent Reports)
   ↓
7. Publish Results (To Jenkins/GitHub)
   ↓
8. Send Notifications (Slack/Email)
```

**Pipeline Triggers:**
- **Push Trigger:** Pipeline runs when code is pushed to repository
- **Pull Request:** Pipeline runs when PR is created/updated
- **Scheduled:** Pipeline runs at specific times (cron: 0 2 * * *)
- **Manual:** Developer clicks "Build Now" button

**Example Trigger Scenarios:**
```yaml
# GitHub Actions triggers
on:
  push:                         # On every push to main
    branches: [ main ]
  pull_request:                 # On every PR
    branches: [ main ]
  schedule:                     # Daily at 2 AM
    - cron: '0 2 * * *'
  workflow_dispatch:            # Manual trigger button
```

---

### Concept 3: Jenkins Architecture and Setup

**What is Jenkins?**
Jenkins is an open-source automation server used by 70%+ companies for CI/CD. It's written in Java and has 1,800+ plugins.

**Why Jenkins?**
- Industry standard for enterprise companies
- Highly configurable and extensible
- Supports distributed builds (master-agent architecture)
- Pipeline as Code (Jenkinsfile)
- Free and open-source

**Jenkins Architecture:**
```
┌─────────────────────────────────────────────┐
│           Jenkins Master (Controller)        │
│  - Schedules builds                          │
│  - Monitors agents                           │
│  - Records test results                      │
│  - Sends notifications                       │
└─────────────────┬───────────────────────────┘
                  │
         ┌────────┴────────┬──────────┐
         ▼                 ▼          ▼
    ┌─────────┐      ┌─────────┐  ┌─────────┐
    │ Agent 1 │      │ Agent 2 │  │ Agent 3 │
    │ (Linux) │      │(Windows)│  │  (Mac)  │
    │         │      │         │  │         │
    │ Runs    │      │ Runs    │  │ Runs    │
    │ Tests   │      │ Tests   │  │ Tests   │
    └─────────┘      └─────────┘  └─────────┘
```

**Jenkins vs GitHub Actions:**
| Aspect | Jenkins | GitHub Actions |
|--------|---------|----------------|
| **Hosting** | Self-hosted (your server) | Cloud-hosted (GitHub) |
| **Cost** | Free (you pay for infra) | Free tier: 2,000 mins/month |
| **Setup** | Manual installation | No setup needed |
| **Flexibility** | Highly customizable | Simpler but less flexible |
| **Plugins** | 1,800+ plugins | GitHub Marketplace actions |
| **Best For** | Large enterprises | Startups, GitHub-based projects |

**Docker Jenkins Setup:**
```bash
# Pull Jenkins Docker image
docker pull jenkins/jenkins:lts

# Run Jenkins container
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts

# Get initial admin password
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

**Jenkins First-Time Setup Steps:**
1. Access: http://localhost:8080
2. Enter initial admin password
3. Install suggested plugins
4. Create admin user
5. Configure Jenkins URL
6. Install additional plugins: Maven, GitHub, Allure

---

### Concept 4: Jenkinsfile - Pipeline as Code

**What is a Jenkinsfile?**
A Jenkinsfile is a text file that contains the definition of a Jenkins pipeline. It's checked into source control alongside your code.

**Why Pipeline as Code?**
- Version controlled (track changes over time)
- Code review pipeline changes (via Pull Requests)
- Reusable across projects
- No manual Jenkins UI configuration

**Declarative vs Scripted Pipeline:**
| Aspect | Declarative | Scripted |
|--------|-------------|----------|
| **Syntax** | Simpler, structured | Groovy code, flexible |
| **Learning Curve** | Easy | Moderate |
| **Use Case** | Standard pipelines (90% cases) | Complex custom logic |
| **Recommended** | ✅ Yes (for most SDETs) | Only for advanced cases |

**Declarative Jenkinsfile Structure:**
```groovy
pipeline {
    agent any                           // Where to run (any available agent)

    tools {                             // Tools to auto-install
        maven 'Maven-3.9.5'
        jdk 'JDK-17'
    }

    environment {                       // Environment variables
        API_BASE_URL = 'https://reqres.in/api'
    }

    stages {                            // Pipeline stages
        stage('Checkout') {             // Stage 1: Get code
            steps {
                git 'https://github.com/yourrepo/api-tests.git'
            }
        }

        stage('Build') {                // Stage 2: Build project
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Run API Tests') {        // Stage 3: Execute tests
            steps {
                sh 'mvn test'
            }
        }

        stage('Generate Report') {      // Stage 4: Create report
            steps {
                allure includeProperties: false, results: [[path: 'target/allure-results']]
            }
        }
    }

    post {                              // Post-build actions
        always {
            junit 'target/surefire-reports/*.xml'
        }
        success {
            echo 'All tests passed! ✅'
        }
        failure {
            echo 'Tests failed! ❌'
        }
    }
}
```

**Jenkinsfile Best Practices:**
1. **Keep it simple:** Start with basic stages, add complexity gradually
2. **Use environment variables:** Don't hardcode URLs or credentials
3. **Parallel stages:** Run independent tests simultaneously
4. **Fail fast:** Stop pipeline on first failure to save time
5. **Clean workspace:** Start with fresh environment each run

---

### Concept 5: GitHub Actions Workflows

**What is GitHub Actions?**
GitHub Actions is a CI/CD platform integrated directly into GitHub. Workflows are defined in YAML files stored in `.github/workflows/` directory.

**Why GitHub Actions?**
- Zero setup (no Jenkins installation needed)
- Free for public repos (2,000 minutes/month for private)
- Tight GitHub integration (auto-comments on PRs)
- Marketplace with pre-built actions
- Perfect for open-source projects

**Workflow YAML Structure:**
```yaml
name: API Test Automation                # Workflow name

on:                                       # Triggers
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 2 * * *'                   # Daily at 2 AM UTC

jobs:                                     # Jobs run in parallel by default
  api-tests:                              # Job ID
    runs-on: ubuntu-latest                # Runner OS

    steps:                                # Sequential steps
      - name: Checkout code               # Step 1
        uses: actions/checkout@v4

      - name: Set up JDK 17               # Step 2
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'maven'

      - name: Build with Maven            # Step 3
        run: mvn clean install -DskipTests

      - name: Run API Tests               # Step 4
        run: mvn test
        env:
          API_BASE_URL: ${{ secrets.API_BASE_URL }}

      - name: Publish Test Report         # Step 5
        if: always()
        uses: dorny/test-reporter@v1
        with:
          name: API Test Results
          path: target/surefire-reports/*.xml
          reporter: java-junit

      - name: Upload Allure Results       # Step 6
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: allure-results
          path: target/allure-results
```

**GitHub Actions vs Jenkins Comparison:**
```yaml
# GitHub Actions (YAML)
- name: Run Tests
  run: mvn test
```

```groovy
// Jenkins (Groovy)
stage('Run Tests') {
    steps {
        sh 'mvn test'
    }
}
```

**Common GitHub Actions:**
- `actions/checkout@v4` - Clone repository
- `actions/setup-java@v4` - Install Java
- `actions/cache@v4` - Cache Maven dependencies
- `actions/upload-artifact@v4` - Upload test reports
- `peaceiris/actions-gh-pages@v3` - Deploy reports to GitHub Pages

---

### Concept 6: Scheduled Test Execution (Cron Jobs)

**What are Cron Expressions?**
Cron is a time-based job scheduler. Cron expressions define when jobs should run.

**Cron Syntax:**
```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6) (Sunday=0)
│ │ │ │ │
* * * * *
```

**Common Cron Examples:**
| Schedule | Cron Expression | Use Case |
|----------|-----------------|----------|
| Every hour | `0 * * * *` | Smoke tests |
| Daily at 2 AM | `0 2 * * *` | Full regression |
| Every Monday at 9 AM | `0 9 * * 1` | Weekly sanity |
| Every 30 minutes | `*/30 * * * *` | Health checks |
| Weekdays at 6 PM | `0 18 * * 1-5` | Post-deployment tests |

**Jenkins Cron:**
```groovy
pipeline {
    triggers {
        cron('0 2 * * *')  // Daily at 2 AM
    }
    // ... rest of pipeline
}
```

**GitHub Actions Cron:**
```yaml
on:
  schedule:
    - cron: '0 2 * * *'  # Daily at 2 AM UTC
```

**Best Practices for Scheduled Tests:**
1. **Off-hours execution:** Run heavy tests at night (less server load)
2. **Timezone awareness:** GitHub Actions uses UTC, Jenkins uses server timezone
3. **Stagger schedules:** Don't run all tests at same time
4. **Smoke vs Regression:** Frequent smoke (hourly), occasional regression (nightly)
5. **Alert fatigue:** Only notify on failures, not every success

---

### Concept 7: Test Reporting in CI/CD

**Why Test Reports Matter?**
- **Visibility:** Stakeholders see test results without accessing logs
- **Trends:** Track pass/fail rates over time
- **Debugging:** Screenshots, logs, request/response data
- **Compliance:** Audit trail for regulated industries

**Popular Reporting Tools:**
| Tool | Description | Best For |
|------|-------------|----------|
| **Allure** | Beautiful HTML reports with history | API & UI tests |
| **Extent Reports** | Customizable reports, screenshots | Detailed debugging |
| **JUnit/TestNG** | XML reports (built-in) | Jenkins integration |
| **Cucumber** | BDD-style reports | Stakeholder communication |

**Allure Report Features:**
- Test execution timeline
- Success/failure trends (historical data)
- Categories of failures (UI, API, DB)
- Request/response logs
- Screenshots and videos
- Test duration analysis

**Generating Allure Report:**
```xml
<!-- pom.xml -->
<dependency>
    <groupId>io.qameta.allure</groupId>
    <artifactId>allure-rest-assured</artifactId>
    <version>2.24.0</version>
</dependency>
```

```bash
# Generate report locally
mvn clean test
mvn allure:serve  # Opens report in browser
```

**Jenkins Allure Integration:**
```groovy
stage('Publish Allure Report') {
    steps {
        allure includeProperties: false,
               jdk: '',
               results: [[path: 'target/allure-results']]
    }
}
```

**GitHub Actions Allure Integration:**
```yaml
- name: Generate Allure Report
  uses: simple-elf/allure-report-action@master
  if: always()
  with:
    allure_results: target/allure-results
    allure_history: allure-history
```

---

### Concept 8: Notifications (Slack, Email, Teams)

**Why Notifications?**
- **Immediate awareness:** Know about failures within seconds
- **Right audience:** Notify only relevant teams
- **Actionable info:** Include build number, failure reason, logs link
- **Avoid noise:** Only notify on failures or status changes

**Slack Notification Example:**
```
🚨 Build #47 FAILED
Repository: api-automation
Branch: feature/user-api
Triggered by: john.doe
Failed Tests: 3/25
Duration: 2m 45s
View Report: http://jenkins.company.com/job/api-tests/47
```

**Jenkins Slack Integration:**
```groovy
post {
    failure {
        slackSend(
            channel: '#qa-alerts',
            color: 'danger',
            message: """
                ❌ API Tests FAILED
                Job: ${env.JOB_NAME}
                Build: ${env.BUILD_NUMBER}
                URL: ${env.BUILD_URL}
            """
        )
    }
    success {
        slackSend(
            channel: '#qa-alerts',
            color: 'good',
            message: "✅ API Tests PASSED - Build #${env.BUILD_NUMBER}"
        )
    }
}
```

**GitHub Actions Slack Integration:**
```yaml
- name: Slack Notification
  if: failure()
  uses: slackapi/slack-github-action@v1.24.0
  with:
    payload: |
      {
        "text": "❌ API Tests Failed",
        "blocks": [
          {
            "type": "section",
            "text": {
              "type": "mrkdwn",
              "text": "*Build*: ${{ github.run_number }}\n*Branch*: ${{ github.ref }}\n*Author*: ${{ github.actor }}"
            }
          }
        ]
      }
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

**Email Notifications:**
```groovy
// Jenkins email
post {
    failure {
        emailext(
            to: 'qa-team@company.com',
            subject: "FAILED: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
            body: """
                Build failed. Check console output at:
                ${env.BUILD_URL}console
            """,
            attachLog: true
        )
    }
}
```

**Notification Best Practices:**
1. **Fail-only alerts:** Don't spam on every success (alert fatigue)
2. **Include context:** Build number, branch, author, error message
3. **Actionable links:** Direct link to console output/report
4. **Right channel:** Critical failures → #engineering, smoke tests → #qa-alerts
5. **Quiet hours:** Mute notifications at night (use `if: github.event_name != 'schedule'`)

---

## 🐍 Python vs Java CI/CD Comparison

### CI/CD in Python (What You Know)
```yaml
# GitHub Actions for Python + pytest
name: Python Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          pip install -r requirements.txt

      - name: Run pytest
        run: |
          pytest tests/ --html=report.html

      - name: Upload report
        uses: actions/upload-artifact@v4
        with:
          name: pytest-report
          path: report.html
```

### CI/CD in Java (What You're Learning)
```yaml
# GitHub Actions for Java + Maven + RestAssured
name: Java API Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'maven'              # Cache Maven dependencies

      - name: Build project
        run: mvn clean install -DskipTests

      - name: Run API tests
        run: mvn test

      - name: Generate Allure Report
        if: always()
        run: mvn allure:report
```

**Key Differences:**
| Aspect | Python CI/CD | Java CI/CD |
|--------|--------------|------------|
| **Package Manager** | pip (requirements.txt) | Maven (pom.xml) |
| **Build Step** | No build needed | `mvn clean install` required |
| **Test Command** | `pytest tests/` | `mvn test` |
| **Caching** | Cache pip packages | Cache Maven `.m2` directory |
| **Report Tool** | pytest-html, Allure | Allure, Extent, TestNG |
| **Speed** | Faster (no build) | Slower (compile step) |

**Mental Model Shift:**
In Python, tests run immediately (`pytest`). In Java, you must **build first** (`mvn clean install`), then test (`mvn test`). This two-step process is critical in CI/CD pipelines.

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Running Tests Without Building First
❌ **Wrong:**
```groovy
stage('Run Tests') {
    steps {
        sh 'mvn test'  // ERROR: Classes not compiled yet!
    }
}
```

✅ **Correct:**
```groovy
stage('Build') {
    steps {
        sh 'mvn clean install -DskipTests'  // Compile code first
    }
}
stage('Run Tests') {
    steps {
        sh 'mvn test'  // Now classes are compiled
    }
}
```

**Why it matters:** Java is a compiled language. You must compile code before running tests. Python skips this step.

---

### Mistake 2: Hardcoding Credentials in Jenkinsfile
❌ **Wrong:**
```groovy
environment {
    API_KEY = 'sk-1234567890abcdef'  // NEVER commit secrets!
}
```

✅ **Correct:**
```groovy
environment {
    API_KEY = credentials('api-key-secret')  // Stored in Jenkins credentials
}
```

**GitHub Actions Correct:**
```yaml
env:
  API_KEY: ${{ secrets.API_KEY }}  # Stored in GitHub Secrets
```

**Why it matters:** Credentials in source control = security breach. Use Jenkins Credentials or GitHub Secrets.

---

### Mistake 3: Not Using `if: always()` for Reports
❌ **Wrong:**
```yaml
- name: Publish Test Report
  uses: dorny/test-reporter@v1  # Only runs if tests pass
```

✅ **Correct:**
```yaml
- name: Publish Test Report
  if: always()  # Runs even if tests fail
  uses: dorny/test-reporter@v1
```

**Why it matters:** If tests fail, you NEED the report to debug. Without `if: always()`, the report step is skipped.

---

### Mistake 4: Wrong Cron Timezone Assumption
❌ **Wrong:**
```yaml
# Expecting 2 AM IST (India), but runs at 2 AM UTC!
schedule:
  - cron: '0 2 * * *'
```

✅ **Correct:**
```yaml
# For 2 AM IST (UTC+5:30), use 8:30 PM UTC (previous day)
schedule:
  - cron: '30 20 * * *'
```

**Why it matters:** GitHub Actions uses UTC. Jenkins uses server timezone. Always verify actual execution time.

---

### Mistake 5: No Cleanup Between Builds
❌ **Wrong:**
```groovy
stage('Build') {
    steps {
        sh 'mvn install'  // Old artifacts remain from previous build
    }
}
```

✅ **Correct:**
```groovy
stage('Build') {
    steps {
        sh 'mvn clean install'  // 'clean' removes old artifacts
    }
}
```

**Why it matters:** Without `clean`, stale compiled classes can cause false positives/negatives.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Parallel Execution Saves Time**
Run independent test suites in parallel to reduce total pipeline time.
```groovy
stage('Parallel Tests') {
    parallel {
        stage('Smoke Tests') {
            steps { sh 'mvn test -Dgroups=smoke' }
        }
        stage('Regression Tests') {
            steps { sh 'mvn test -Dgroups=regression' }
        }
    }
}
```

**Tip 2: Matrix Builds for Multi-Environment Testing**
Test against multiple environments (dev, qa, staging) simultaneously.
```yaml
strategy:
  matrix:
    environment: [dev, qa, staging]
steps:
  - run: mvn test -DAPI_URL=${{ matrix.environment }}.api.com
```

**Tip 3: Cache Dependencies to Speed Up Builds**
Cache Maven dependencies to avoid re-downloading every build.
```yaml
- name: Cache Maven packages
  uses: actions/cache@v4
  with:
    path: ~/.m2
    key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}
```

**Tip 4: Use Build Numbers in Test Data**
Avoid conflicts when multiple builds run simultaneously.
```java
String uniqueEmail = "user" + System.getenv("BUILD_NUMBER") + "@test.com";
```

**Tip 5: Separate CI (Fast) from CD (Slow) Tests**
Run quick smoke tests on every commit. Full regression nightly.
```groovy
when {
    branch 'main'
    triggeredBy 'SCMTrigger'  // Only on code commit
}
```

---

## 🔍 Deep Dive: Pipeline Optimization Strategies

**What experienced SDETs know:**
Fast pipelines = faster feedback = happier developers. A 30-minute pipeline that could run in 5 minutes costs the company significantly in wasted developer time.

**Optimization Techniques:**

**1. Fail Fast Strategy**
```groovy
stage('Smoke Tests') {
    steps {
        sh 'mvn test -Dgroups=smoke'
    }
    post {
        failure {
            error("Smoke tests failed. Stopping pipeline.")
        }
    }
}
// If smoke fails, regression never runs (saves time)
```

**2. Test Result Caching**
```groovy
// Skip tests if no code changed
when {
    changeset "src/**/*.java"
}
```

**3. Docker Layer Caching**
```dockerfile
# Cache Maven dependencies in Docker layer
FROM maven:3.9.5-eclipse-temurin-17
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline  # Downloads all dependencies
COPY src ./src
RUN mvn package
```

**4. Distributed Testing**
```groovy
// Run tests across 5 agents in parallel
stage('Distributed Tests') {
    agent { label 'test-runner' }
    steps {
        sh 'mvn test -DsuiteXmlFile=testng-${NODE_NAME}.xml'
    }
}
```

**When to use this:**
- Pipeline takes >10 minutes: Investigate optimizations
- Tests fail frequently: Add smoke test stage first
- Running on paid CI minutes: Cache aggressively
- Multiple teams waiting: Implement parallel/distributed execution

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **CI/CD** | Continuous Integration/Continuous Deployment | Automated build, test, deploy |
| **Pipeline** | Automated workflow of build/test/deploy stages | Checkout → Build → Test → Deploy |
| **Jenkinsfile** | Pipeline definition file (Groovy syntax) | `pipeline { agent any ... }` |
| **Workflow** | GitHub Actions equivalent of pipeline | `.github/workflows/test.yml` |
| **Agent/Runner** | Machine that executes pipeline steps | Ubuntu VM, Docker container |
| **Stage** | Logical grouping of steps in pipeline | Build stage, Test stage |
| **Step** | Individual command in a stage | `sh 'mvn test'` |
| **Artifact** | Files produced by build (JAR, reports) | `target/myapp.jar` |
| **Cron** | Time-based job scheduler | `0 2 * * *` = 2 AM daily |
| **Webhook** | HTTP callback triggered by events | GitHub push → Jenkins build |
| **Blue Ocean** | Modern Jenkins UI | Visual pipeline editor |
| **Matrix Build** | Run same tests with different parameters | Test on Java 11, 17, 21 |

---

## 🎓 Interview Prep

**Common Interview Questions on CI/CD:**

**Q1: Explain CI/CD to a non-technical person.**
**A:** "CI/CD is like having a robot assistant for software testing. Every time a developer writes code, the robot automatically checks if the code works, runs all tests, and reports any problems—all within minutes. This catches bugs early before they reach customers."

**Q2: What's the difference between Continuous Delivery and Continuous Deployment?**
**A:**
```
Continuous Delivery:
  Code → Auto Build → Auto Test → Manual Approval → Deploy

Continuous Deployment:
  Code → Auto Build → Auto Test → Auto Deploy (no manual step)
```
"Delivery requires human approval before production. Deployment is fully automated. Most companies use Delivery for safety."

**Q3: How do you handle flaky tests in CI/CD?**
**A:**
```java
// 1. Retry mechanism
@Test(retryAnalyzer = RetryFailedTests.class)
public void testUserAPI() { ... }

// 2. Mark as known flaky (don't fail pipeline)
@Test(groups = {"flaky"})

// 3. Separate pipeline for flaky tests
mvn test -DexcludedGroups=flaky
```
"First, fix the flakiness (root cause). Temporarily, retry failing tests or exclude from main pipeline but run separately to track them."

**Q4: Show me a simple Jenkinsfile for API tests.**
**A:**
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps { git 'https://github.com/myrepo/api-tests.git' }
        }
        stage('Build') {
            steps { sh 'mvn clean install -DskipTests' }
        }
        stage('Test') {
            steps { sh 'mvn test' }
        }
    }
    post {
        always { junit 'target/surefire-reports/*.xml' }
        failure { emailext to: 'qa@company.com', subject: 'Tests Failed' }
    }
}
```

**Q5: How do you pass secrets (API keys, passwords) to tests in CI/CD?**
**A:**
```groovy
// Jenkins: Use credentials binding
environment {
    API_KEY = credentials('api-key-id')  // Stored in Jenkins
}

// GitHub Actions: Use secrets
env:
  API_KEY: ${{ secrets.API_KEY }}  // Stored in GitHub Settings

// Java test code
String apiKey = System.getenv("API_KEY");
```
"Never hardcode secrets in code or Jenkinsfile. Use Jenkins Credentials or GitHub Secrets. Reference them as environment variables."

**Q6: How do you trigger Jenkins builds from GitHub?**
**A:**
"Three ways:
1. **Webhook:** GitHub sends POST request to Jenkins on every push
2. **Polling:** Jenkins checks GitHub every N minutes for changes (`H/5 * * * *`)
3. **Manual:** Developer clicks 'Build Now' in Jenkins UI

Webhook is best (instant, no polling overhead)."

**Q7: What's the difference between `agent any` and `agent { docker }`?**
**A:**
```groovy
agent any
// Runs on any available Jenkins agent (VM with Java, Maven pre-installed)

agent {
    docker {
        image 'maven:3.9.5-jdk-17'
    }
}
// Runs inside a Docker container (consistent environment, no pre-install needed)
```
"Docker ensures consistent environment across all builds. Preferred for reproducibility."

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What's the difference between CI and CD? Give an example of when you'd use Continuous Delivery vs Deployment.**

2. **Explain why SDETs need to know CI/CD. How does it change the way you work compared to manual test execution?**

3. **Compare Jenkins and GitHub Actions. When would you choose one over the other?**

4. **Write a cron expression for: "Run tests every weekday at 6 PM"**

5. **What happens if you run `mvn test` without running `mvn clean install` first in a fresh CI environment?**

**Answers at the end of Practice file.**

---
