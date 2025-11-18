# DAY 5 CHEAT SHEET: CI/CD INTEGRATION

## ⚡ Quick Syntax Reference

### Jenkins Declarative Pipeline Structure
```groovy
pipeline {
    agent any                    // Where to run

    tools {                      // Auto-install tools
        maven 'Maven-3.9.5'
        jdk 'JDK-17'
    }

    environment {                // Environment variables
        API_URL = 'https://api.example.com'
    }

    stages {                     // Pipeline stages
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
    }

    post {                       // Post-build actions
        always { junit 'target/surefire-reports/*.xml' }
        success { echo 'Success!' }
        failure { echo 'Failed!' }
    }
}
```

### GitHub Actions Workflow Structure
```yaml
name: API Tests                  # Workflow name

on:                              # Triggers
  push:
    branches: [ main ]
  schedule:
    - cron: '0 2 * * *'

jobs:                            # Jobs (parallel by default)
  test:
    runs-on: ubuntu-latest       # Runner OS

    steps:                       # Sequential steps
      - uses: actions/checkout@v4
      - name: Run tests
        run: mvn test
```

### Docker Jenkins Setup
```bash
# Pull Jenkins image
docker pull jenkins/jenkins:lts

# Run Jenkins container
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts

# Get initial password
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

---

## 🔑 Key Concepts at a Glance

### CI/CD Fundamentals

| Concept | Definition | Example |
|---------|------------|---------|
| **CI** | Continuous Integration - Auto build/test on commit | Merge → Build → Test |
| **CD (Delivery)** | Continuous Delivery - Manual deploy to prod | Test → Approve → Deploy |
| **CD (Deployment)** | Continuous Deployment - Auto deploy to prod | Test → Auto-Deploy |
| **Pipeline** | Automated workflow of stages | Checkout → Build → Test → Deploy |
| **Agent/Runner** | Machine executing pipeline | Jenkins agent, GitHub runner |
| **Artifact** | Files produced by build | JAR, test reports |

### Jenkins vs GitHub Actions

| Aspect | Jenkins | GitHub Actions |
|--------|---------|----------------|
| **Hosting** | Self-hosted | Cloud-hosted |
| **Cost** | Free (infrastructure cost) | Free: 2,000 min/month |
| **Setup** | Manual installation | Zero setup |
| **Language** | Groovy (Jenkinsfile) | YAML (workflow) |
| **Best For** | Large enterprises | Startups, GitHub projects |

---

## 🎯 Essential Commands

### Maven Commands for CI/CD
```bash
# Clean and build (skip tests)
mvn clean install -DskipTests

# Run all tests
mvn test

# Run specific test groups
mvn test -Dgroups=smoke
mvn test -Dgroups=regression

# Run with environment variable
mvn test -Denv=qa

# Generate Allure report
mvn allure:report

# Serve Allure report (opens browser)
mvn allure:serve

# Debug mode
mvn test -X
```

### Jenkins CLI Commands
```bash
# Restart Jenkins
java -jar jenkins-cli.jar -s http://localhost:8080/ restart

# Build job
java -jar jenkins-cli.jar -s http://localhost:8080/ build api-tests

# Get job status
java -jar jenkins-cli.jar -s http://localhost:8080/ get-job api-tests
```

### Git Commands for CI/CD
```bash
# Check current branch
git branch

# Trigger CI by pushing to main
git push origin main

# Tag release (trigger deployment pipeline)
git tag -a v1.0.0 -m "Release 1.0.0"
git push origin v1.0.0
```

---

## 📋 Pipeline Patterns

### Basic Jenkinsfile Pattern
```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps { checkout scm }
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
    }
}
```

### Parallel Execution Pattern
```groovy
stage('Parallel Tests') {
    parallel {
        stage('Smoke') {
            steps { sh 'mvn test -Dgroups=smoke' }
        }
        stage('Regression') {
            steps { sh 'mvn test -Dgroups=regression' }
        }
    }
}
```

### Retry Pattern (Flaky Tests)
```groovy
stage('Test') {
    steps {
        retry(3) {
            sh 'mvn test'
        }
    }
}
```

### Environment-Specific Pattern
```groovy
stage('Test') {
    steps {
        script {
            if (env.BRANCH_NAME == 'main') {
                sh 'mvn test -Denv=prod'
            } else {
                sh 'mvn test -Denv=qa'
            }
        }
    }
}
```

### Basic GitHub Actions Pattern
```yaml
name: API Tests
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'maven'
      - run: mvn clean install -DskipTests
      - run: mvn test
      - uses: actions/upload-artifact@v4
        if: always()
        with:
          name: test-reports
          path: target/surefire-reports/
```

### Scheduled Run Pattern
```yaml
on:
  schedule:
    - cron: '0 2 * * *'      # Daily at 2 AM UTC
    - cron: '0 9 * * 1'      # Monday at 9 AM UTC
  workflow_dispatch:          # Manual trigger
```

### Matrix Build Pattern
```yaml
strategy:
  matrix:
    java-version: [11, 17, 21]
    os: [ubuntu-latest, windows-latest]
runs-on: ${{ matrix.os }}
steps:
  - uses: actions/setup-java@v4
    with:
      java-version: ${{ matrix.java-version }}
```

---

## ⏰ Cron Expression Reference

### Common Schedules

| Schedule | Cron Expression | Use Case |
|----------|-----------------|----------|
| Every hour | `0 * * * *` | Smoke tests |
| Every 30 minutes | `*/30 * * * *` | Health checks |
| Daily at 2 AM | `0 2 * * *` | Nightly regression |
| Weekdays at 9 AM | `0 9 * * 1-5` | Start of day tests |
| Weekdays at 6 PM | `0 18 * * 1-5` | End of day tests |
| Monday at 8 AM | `0 8 * * 1` | Weekly sanity |
| First day of month | `0 0 1 * *` | Monthly full suite |

### Cron Syntax
```
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of month (1 - 31)
│ │ │ ┌───────────── month (1 - 12)
│ │ │ │ ┌───────────── day of week (0 - 6) (Sunday=0)
│ │ │ │ │
* * * * *
```

### Timezone Conversion
```
GitHub Actions uses UTC only!
IST (UTC+5:30): 9:00 AM IST = 3:30 AM UTC → '30 3 * * *'
PST (UTC-8):    9:00 AM PST = 5:00 PM UTC → '0 17 * * *'
EST (UTC-5):    9:00 AM EST = 2:00 PM UTC → '0 14 * * *'
```

---

## 📊 Test Reporting

### Allure Report Setup
```xml
<!-- pom.xml -->
<dependency>
    <groupId>io.qameta.allure</groupId>
    <artifactId>allure-testng</artifactId>
    <version>2.24.0</version>
</dependency>

<plugin>
    <groupId>io.qameta.allure</groupId>
    <artifactId>allure-maven</artifactId>
    <version>2.12.0</version>
</plugin>
```

### Jenkins Allure Integration
```groovy
stage('Publish Report') {
    steps {
        allure includeProperties: false,
               results: [[path: 'target/allure-results']]
    }
}
```

### GitHub Actions Allure Integration
```yaml
- name: Generate Allure Report
  uses: simple-elf/allure-report-action@master
  if: always()
  with:
    allure_results: target/allure-results
    allure_history: allure-history
```

---

## 💬 Slack Notification Templates

### Jenkins Slack Notification
```groovy
post {
    failure {
        slackSend(
            channel: '#qa-alerts',
            color: 'danger',
            message: """
                ❌ Build #${env.BUILD_NUMBER} FAILED
                Job: ${env.JOB_NAME}
                Console: ${env.BUILD_URL}console
            """,
            tokenCredentialId: 'slack-webhook'
        )
    }
}
```

### GitHub Actions Slack Notification
```yaml
- name: Slack notification
  if: failure()
  uses: slackapi/slack-github-action@v1.24.0
  with:
    payload: |
      {
        "text": "❌ Tests Failed",
        "blocks": [
          {
            "type": "section",
            "text": {
              "type": "mrkdwn",
              "text": "*Build:* ${{ github.run_number }}\n*Branch:* ${{ github.ref }}"
            }
          }
        ]
      }
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

---

## 🔧 Environment Variables

### Jenkins Environment Variables
```groovy
environment {
    API_URL = 'https://api.example.com'
    CREDENTIAL = credentials('api-key')  // From Jenkins credentials
}

steps {
    sh 'echo ${API_URL}'               // Shell interpolation
    echo env.API_URL                   // Groovy echo
}

// Built-in variables
${env.BUILD_NUMBER}
${env.JOB_NAME}
${env.BUILD_URL}
${env.GIT_BRANCH}
${env.WORKSPACE}
```

### GitHub Actions Environment Variables
```yaml
env:
  API_URL: https://api.example.com
  API_KEY: ${{ secrets.API_KEY }}      # From GitHub secrets

steps:
  - run: echo $API_URL                 # Shell
  - run: echo ${{ env.API_URL }}       # GitHub expression

# Built-in variables
${{ github.run_number }}
${{ github.repository }}
${{ github.ref }}
${{ github.actor }}
${{ github.sha }}
```

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Missing braces** | `stage('Test') steps {` | `stage('Test') { steps {` |
| **Wrong indentation** | YAML with 3 spaces | YAML with 2 or 4 spaces |
| **Hardcoded secrets** | `API_KEY = 'abc123'` | `API_KEY = credentials('id')` |
| **No cache** | Build downloads deps every time | Use `cache: 'maven'` |
| **No `if: always()`** | Reports not published on failure | Add `if: always()` |
| **Forgot to build** | `mvn test` only | `mvn clean install` then `mvn test` |
| **Wrong timezone** | Cron at local time | Convert to UTC |
| **Missing chmod** | `./script.sh` permission denied | `chmod +x script.sh` |

---

## 🐛 Debugging Commands

### Check Pipeline Syntax
```bash
# Validate Jenkinsfile locally
curl -X POST -F "jenkinsfile=<Jenkinsfile" http://localhost:8080/pipeline-model-converter/validate

# Validate GitHub Actions YAML
# Use: https://rhysd.github.io/actionlint/
actionlint .github/workflows/api-tests.yml
```

### Debug Environment
```groovy
// Jenkins
stage('Debug') {
    steps {
        sh 'printenv | sort'              // All env vars
        sh 'java -version'                // Java version
        sh 'mvn -version'                 // Maven version
        sh 'pwd'                          // Current directory
        sh 'ls -la'                       // List files
    }
}
```

```yaml
# GitHub Actions
- name: Debug
  run: |
    printenv | sort
    java -version
    mvn -version
    pwd
    ls -la
```

### Enable Verbose Logging
```bash
# Maven debug mode
mvn test -X

# Maven show full stack traces
mvn test -e
```

```yaml
# GitHub Actions debug mode
# Settings → Secrets → Add:
# ACTIONS_STEP_DEBUG = true
```

---

## 💡 Pro Tips

### Speed Up Builds
```yaml
# Cache Maven dependencies
- uses: actions/setup-java@v4
  with:
    cache: 'maven'        # 10x faster builds!
```

### Fail Fast
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
// Regression never runs if smoke fails
```

### Parallel Execution
```groovy
parallel {
    stage('API Tests') { steps { sh 'mvn test -Dgroups=api' } }
    stage('UI Tests')  { steps { sh 'mvn test -Dgroups=ui' } }
}
// Cuts total time in half!
```

### Unique Test Data
```java
// Avoid conflicts when multiple builds run
String uniqueEmail = "user" + System.getenv("BUILD_NUMBER") + "@test.com";
```

### Conditional Stages
```groovy
when {
    branch 'main'                        // Only on main branch
    environment name: 'DEPLOY', value: 'true'
    triggeredBy 'SCMTrigger'            // Only on code commit
}
```

---

## 🎤 Interview Phrases

**When asked about CI/CD, say:**

*"CI/CD is the practice of automatically building, testing, and deploying code. I've implemented Jenkins pipelines and GitHub Actions workflows for API automation. My setup includes parallel test execution to reduce runtime, multi-environment support for dev/qa/prod, Allure reporting for visibility, and Slack notifications for immediate feedback."*

**Example:**
*"In my recent project, I created a Jenkins pipeline that runs smoke tests on every commit (5 minutes) and full regression nightly (30 minutes). We use parallel execution to test against multiple environments simultaneously, reducing total pipeline time by 60%. Allure reports are automatically generated and published, and Slack alerts notify the team of failures within seconds."*

---

## 🎯 Key Takeaways

**Today You Learned:**
- ✅ CI/CD fundamentals (CI vs CD vs CD)
- ✅ Jenkins installation and configuration (Docker setup)
- ✅ Jenkinsfile syntax (declarative pipelines)
- ✅ GitHub Actions workflows (YAML configuration)
- ✅ Scheduled execution (cron expressions)
- ✅ Test reporting (Allure integration)
- ✅ Notifications (Slack/email setup)
- ✅ Multi-environment testing (dev/qa/prod)
- ✅ Parallel execution (optimization strategies)

**Critical for Automation:**
- 🎯 Pipeline as Code = Version controlled, reviewable, reusable
- 🎯 Fail Fast Strategy = Smoke tests before regression
- 🎯 Caching = 10x faster builds (cache Maven dependencies)
- 🎯 Parallel Execution = Reduced total pipeline time
- 🎯 Proper Notifications = Right audience, actionable info

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **"Pipeline as Code (Jenkinsfile/YAML) is treated like application code—version controlled, code reviewed, tested, and deployed. This makes CI/CD reliable, repeatable, and auditable."**

---

## 🔮 Weekend Preview

**Week 4 Wrap-Up:**
- Days 1-4: Built API automation framework with RestAssured
- Day 5: Integrated with CI/CD (Jenkins + GitHub Actions)
- Weekend: Weekend project to solidify all Week 4 concepts

**What to review tonight:**
- Jenkinsfile syntax (focus on stages, steps, post)
- GitHub Actions YAML (focus on jobs, steps, triggers)
- Cron expressions (practice timezone conversions)

**What to think about:**
- How would you explain your CI/CD setup in an interview?
- What metrics would you track (pass rate, execution time, flaky tests)?
- How would you convince stakeholders to invest in CI/CD?

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- [x] Learned CI/CD fundamentals
- [x] Set up Jenkins locally (Docker)
- [x] Created declarative Jenkinsfile
- [x] Built GitHub Actions workflow
- [x] Configured scheduled execution
- [x] Integrated Allure reporting
- [x] Set up Slack notifications
- [x] ~3 hours of CI/CD practice

**Cumulative Progress:**
- Days completed: 5/7 (Week 4)
- Weeks completed: 4/4 (Full program!)
- Skills mastered: Java, Selenium, TestNG, API Testing, CI/CD
- Interview readiness: 90% 🚀

**Momentum Statement:**
*"You've now completed the CI/CD integration module! You can design, implement, and maintain production-grade CI/CD pipelines for API automation. This is a critical skill for SDET roles at product companies. Combined with your Java, Selenium, and API testing knowledge, you're now ready for 20-25 LPA roles!"*

---

## 📚 Quick Reference URLs

**Tools:**
- Jenkins: https://www.jenkins.io/
- GitHub Actions: https://docs.github.com/actions
- Allure: https://docs.qameta.io/allure/

**Validation:**
- Cron expression validator: https://crontab.guru/
- YAML validator: https://www.yamllint.com/
- Jenkinsfile validator: http://localhost:8080/pipeline-model-converter/validate

**Learning:**
- Jenkins Pipeline Syntax: https://www.jenkins.io/doc/book/pipeline/syntax/
- GitHub Actions Workflow Syntax: https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions

---

## 📊 Progress Tracker

**Week 4 Progress:**
```
Day 1: API Testing Basics          ✅ Complete
Day 2: RestAssured Deep Dive       ✅ Complete
Day 3: Advanced API Testing        ✅ Complete
Day 4: Maven & Build Tools         ✅ Complete
Day 5: CI/CD Integration           ✅ Complete (You are here!)
Day 6: Framework Design            ⏳ Next
Day 7: Week 4 Project              ⏳ Weekend
```

**Overall Journey:**
```
Week 1: Java Fundamentals          ✅ 100%
Week 2: Selenium WebDriver         ✅ 100%
Week 3: TestNG & Frameworks        ✅ 100%
Week 4: API Testing & CI/CD        ⏳ 71% (5/7 days)
```

**You're 96% done with the entire 4-week program! 🎉**

---
