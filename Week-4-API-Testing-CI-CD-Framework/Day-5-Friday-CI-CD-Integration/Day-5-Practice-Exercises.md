# DAY 5 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master CI/CD setup for Java API automation by configuring Jenkins pipelines and GitHub Actions workflows, implementing scheduled test execution, and integrating test reporting with notifications.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Jenkins Docker Setup
**Objective:** Install and configure Jenkins locally using Docker

**Task:**
1. Pull the Jenkins Docker image
2. Run Jenkins container with proper port mappings
3. Complete initial setup wizard
4. Install required plugins (Maven Integration, GitHub, Allure)

**Commands to Execute:**
```bash
# Pull Jenkins image
docker pull jenkins/jenkins:lts

# Run Jenkins container
docker run -d \
  --name jenkins-cicd \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts

# Get initial admin password
docker exec jenkins-cicd cat /var/jenkins_home/secrets/initialAdminPassword
```

**Verification Steps:**
1. Access http://localhost:8080
2. You should see Jenkins unlock screen
3. Enter the admin password
4. Install suggested plugins
5. Create admin user (username: admin, password: admin123)
6. Go to "Manage Jenkins" → "Manage Plugins" → "Available"
7. Install: Maven Integration, GitHub, Allure

**Expected Output:**
```
Jenkins is ready!
Dashboard should show "Welcome to Jenkins!"
```

**Hints:**
- If port 8080 is busy, use `-p 8081:8080` instead
- Keep the initial password safe (you'll need it)
- Plugin installation takes 5-10 minutes

---

### Exercise 2: Create Freestyle Jenkins Job
**Objective:** Create a simple Jenkins job that runs Maven commands

**Task:**
Create a freestyle job that:
1. Clones a Git repository
2. Runs `mvn clean install`
3. Displays build output

**Steps:**
1. In Jenkins Dashboard, click "New Item"
2. Name: `API-Tests-Freestyle`
3. Type: "Freestyle project"
4. Under "Source Code Management":
   - Select "Git"
   - Repository URL: `https://github.com/rest-assured/rest-assured.git`
5. Under "Build":
   - Add build step: "Invoke top-level Maven targets"
   - Goals: `clean install -DskipTests`
6. Save and click "Build Now"

**Expected Output:**
```
Console Output:
[INFO] BUILD SUCCESS
[INFO] Total time: 45.2 s
Finished: SUCCESS
```

**Hints:**
- Ensure Maven is installed on Jenkins (Manage Jenkins → Global Tool Configuration)
- First build may take time (downloading dependencies)

---

### Exercise 3: Write a Basic Jenkinsfile
**Objective:** Create a declarative Jenkinsfile with 3 stages

**Task:**
Write a Jenkinsfile that:
1. Checks out code
2. Builds the project
3. Runs tests

**Starter Code:**
```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // TODO: Add git checkout command
            }
        }

        stage('Build') {
            steps {
                // TODO: Add Maven build command (skip tests)
            }
        }

        stage('Test') {
            steps {
                // TODO: Add Maven test command
            }
        }
    }
}
```

**Hints:**
- Use `git url: 'https://your-repo-url.git'` for checkout
- Use `sh 'mvn clean install -DskipTests'` for build
- Use `sh 'mvn test'` for test execution
- Save this file as `Jenkinsfile` in your project root

---

## 💪 Core Practice (30 min)

### Exercise 4: Complete Jenkinsfile with Environment Variables
**Objective:** Build a production-ready Jenkinsfile with environment variables and post-actions

**Problem Statement:**
Create a Jenkinsfile for the API automation framework you've been building this week. The pipeline should:
1. Set environment variables (API_BASE_URL, TEST_ENV)
2. Have 4 stages: Checkout, Build, Test, Report
3. Publish test results (JUnit XML)
4. Send notifications on success/failure

**Requirements:**
- Use environment variables for API URL
- Use Maven 3.9.5 and JDK 17
- Generate and publish test reports
- Print custom messages in post-actions

**Starter Code:**
```groovy
pipeline {
    agent any

    tools {
        maven 'Maven-3.9.5'  // Configure in Global Tool Configuration
        jdk 'JDK-17'
    }

    environment {
        // TODO: Add API_BASE_URL
        // TODO: Add TEST_ENV
    }

    stages {
        stage('Checkout') {
            steps {
                // TODO: Checkout code
                echo 'Code checked out successfully'
            }
        }

        stage('Build') {
            steps {
                // TODO: Clean and build project
            }
        }

        stage('Run API Tests') {
            steps {
                // TODO: Execute tests
                echo "Running tests against ${env.API_BASE_URL}"
            }
        }

        stage('Generate Reports') {
            steps {
                // TODO: Generate test reports
            }
        }
    }

    post {
        always {
            // TODO: Publish JUnit results
        }
        success {
            // TODO: Echo success message
        }
        failure {
            // TODO: Echo failure message
        }
    }
}
```

**Test Cases:**
```
Scenario 1: All tests pass
  - Pipeline should complete successfully
  - Success message should appear in console

Scenario 2: One test fails
  - Pipeline should fail at Test stage
  - Failure message should appear in console
  - Test report should still be published
```

**Hints:**
- Environment block: `API_BASE_URL = 'https://reqres.in/api'`
- JUnit publisher: `junit 'target/surefire-reports/*.xml'`
- Post actions run regardless of stage success/failure

---

### Exercise 5: GitHub Actions Workflow for API Tests
**Objective:** Create a complete GitHub Actions workflow with caching and reporting

**Problem Statement:**
Create a GitHub Actions workflow file that:
1. Triggers on push to main and pull requests
2. Runs on Ubuntu latest
3. Sets up Java 17 with Maven caching
4. Builds and tests the project
5. Uploads test reports as artifacts

**Requirements:**
- File location: `.github/workflows/api-tests.yml`
- Use actions/checkout@v4 for code checkout
- Use actions/setup-java@v4 for Java setup
- Cache Maven dependencies (.m2 directory)
- Upload test results even if tests fail

**Starter Code:**
```yaml
name: API Test Automation

on:
  push:
    branches: [ main ]
  # TODO: Add pull_request trigger

jobs:
  api-tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        # TODO: Use actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          # TODO: Specify Java version
          # TODO: Specify distribution (temurin)
          # TODO: Enable Maven cache

      - name: Build project
        run: # TODO: Maven build command

      - name: Run API tests
        run: # TODO: Maven test command

      - name: Upload test reports
        if: # TODO: Run always (even on failure)
        uses: actions/upload-artifact@v4
        with:
          name: test-reports
          path: target/surefire-reports/
```

**Expected Output:**
```yaml
# When you push code, GitHub Actions should:
✓ Checkout code (5s)
✓ Set up JDK 17 (25s)
✓ Build project (45s)
✓ Run API tests (30s)
✓ Upload test reports (10s)

Total: ~2 minutes
```

**Hints:**
- For cache: `cache: 'maven'` (simple as that!)
- For always running: `if: always()`
- Check "Actions" tab in GitHub to see workflow runs

---

### Exercise 6: Scheduled Test Execution with Cron
**Objective:** Configure scheduled pipeline runs using cron expressions

**Problem Statement:**
Modify your GitHub Actions workflow to run:
1. On every push to main (as before)
2. Every day at 2 AM UTC (nightly regression)
3. Every Monday at 9 AM UTC (weekly sanity)

Also, add environment-specific execution:
- Push/PR: Run smoke tests only
- Scheduled: Run full regression

**Requirements:**
- Add cron triggers for both schedules
- Use `github.event_name` to detect trigger type
- Pass different Maven goals based on trigger

**Starter Code:**
```yaml
name: API Test Automation with Schedule

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    # TODO: Add cron for daily 2 AM UTC
    # TODO: Add cron for Monday 9 AM UTC

jobs:
  api-tests:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'maven'

      - name: Build project
        run: mvn clean install -DskipTests

      - name: Run Smoke Tests (on push/PR)
        if: github.event_name != 'schedule'
        run: mvn test -Dgroups=smoke

      - name: Run Full Regression (on schedule)
        if: # TODO: Condition for scheduled runs
        run: # TODO: Maven command for all tests
```

**Test Cases:**
```
Test Case 1: Manual push to main
  - Should run smoke tests only
  - Should complete in 2-3 minutes

Test Case 2: Scheduled run at 2 AM
  - Should run full regression
  - Should complete in 10-15 minutes
```

**Hints:**
- Daily 2 AM: `- cron: '0 2 * * *'`
- Monday 9 AM: `- cron: '0 9 * * 1'`
- Schedule detection: `if: github.event_name == 'schedule'`

---

## 🚀 Challenge Exercise (15 min)

### Exercise 7: Complete CI/CD Pipeline with Slack Notifications
**Objective:** Build end-to-end Jenkins pipeline with parallel execution, reporting, and Slack alerts

**Problem Statement:**
Create a production-ready Jenkinsfile that:
1. Runs smoke and regression tests in parallel
2. Generates Allure reports
3. Sends Slack notifications with build status
4. Includes retry logic for failed tests
5. Archives test artifacts

**Requirements:**
- **Parallel Stages:** Smoke tests and regression tests run simultaneously
- **Allure Reporting:** Generate and publish Allure reports
- **Slack Integration:** Send notifications to #qa-alerts channel
- **Artifacts:** Archive test reports for later review
- **Retry Logic:** Retry failed tests once before marking as failed

**Complete Implementation:**
```groovy
pipeline {
    agent any

    tools {
        maven 'Maven-3.9.5'
        jdk 'JDK-17'
    }

    environment {
        API_BASE_URL = 'https://reqres.in/api'
        SLACK_CHANNEL = '#qa-alerts'
        // TODO: Add more environment variables
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-username/api-automation.git'
                echo "✓ Code checked out from main branch"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
                echo "✓ Project built successfully"
            }
        }

        stage('Parallel Tests') {
            parallel {
                stage('Smoke Tests') {
                    steps {
                        script {
                            try {
                                sh 'mvn test -Dgroups=smoke'
                            } catch (Exception e) {
                                echo "Smoke tests failed. Retrying..."
                                sh 'mvn test -Dgroups=smoke -DretryFailedTestsCount=1'
                            }
                        }
                    }
                }

                stage('Regression Tests') {
                    steps {
                        script {
                            // TODO: Implement regression tests with retry
                        }
                    }
                }
            }
        }

        stage('Generate Allure Report') {
            steps {
                // TODO: Generate Allure report
                echo "✓ Allure report generated"
            }
        }
    }

    post {
        always {
            // TODO: Publish JUnit results
            // TODO: Archive artifacts (reports, logs)
        }

        success {
            script {
                // TODO: Send Slack success notification
                echo "✅ Pipeline completed successfully!"
            }
        }

        failure {
            script {
                // TODO: Send Slack failure notification with details
                echo "❌ Pipeline failed!"
            }
        }
    }
}
```

**Testing Instructions:**
1. Configure Slack webhook in Jenkins (Manage Jenkins → Configure System)
2. Add Slack credential with webhook URL
3. Create a test that intentionally fails
4. Run pipeline and verify Slack notification is received
5. Fix the failing test and verify success notification

**Bonus Challenges:**
- [ ] Add email notifications to qa-team@company.com
- [ ] Implement build timeout (abort if takes >10 minutes)
- [ ] Add stage to deploy test reports to GitHub Pages
- [ ] Create separate pipelines for dev, qa, and prod environments
- [ ] Add input step for manual approval before production deployment

**Expected Slack Message:**
```
✅ Build #23 PASSED
Repository: api-automation
Branch: main
Duration: 3m 45s
Smoke Tests: 15/15 ✓
Regression Tests: 47/47 ✓
Report: http://jenkins.local/job/api-automation/23/allure
```

**Hints:**
- Allure: `allure includeProperties: false, results: [[path: 'target/allure-results']]`
- Archive: `archiveArtifacts artifacts: 'target/surefire-reports/**', allowEmptyArchive: true`
- Slack: Use `slackSend` method with channel, color, and message parameters

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```bash
# Complete setup commands
docker pull jenkins/jenkins:lts

docker run -d \
  --name jenkins-cicd \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts

# Wait 30 seconds for Jenkins to start
sleep 30

# Get password
docker exec jenkins-cicd cat /var/jenkins_home/secrets/initialAdminPassword

# Output: f8e3a9b7c4d2e1f6a5b8c9d0e1f2a3b4
```

**Explanation:**
- `-d`: Run container in detached mode (background)
- `-p 8080:8080`: Map host port 8080 to container port 8080 (Jenkins UI)
- `-p 50000:50000`: Port for Jenkins agent communication
- `-v jenkins_home:/var/jenkins_home`: Persist Jenkins data in Docker volume
- `lts`: Long-term support version (stable)

**Key Takeaways:**
- Always use volumes to persist data (otherwise data lost on container restart)
- Port 8080 is standard for Jenkins, but can be changed if conflicts
- Initial password is auto-generated for security

---

### Exercise 2 Solution
**Step-by-Step Configuration:**

1. **New Item Setup:**
   - Name: `API-Tests-Freestyle`
   - Type: Freestyle project
   - Click OK

2. **Source Code Management:**
   - Select: Git
   - Repository URL: `https://github.com/rest-assured/rest-assured.git`
   - Branch: `*/master`

3. **Build Triggers (Optional):**
   - ☑ GitHub hook trigger for GITScm polling

4. **Build Environment:**
   - ☑ Delete workspace before build starts

5. **Build Steps:**
   - Add: Invoke top-level Maven targets
   - Maven Version: Maven-3.9.5
   - Goals: `clean install -DskipTests`

6. **Post-build Actions:**
   - Add: Archive the artifacts
   - Files to archive: `target/*.jar`

**Explanation:**
Freestyle jobs are simple but limited. They use Jenkins UI configuration (click-based) rather than code. Good for learning but not scalable (can't version control the configuration).

**Key Takeaways:**
- Freestyle = UI-based configuration (not code)
- Limited reusability and version control
- Good for simple jobs, bad for complex pipelines
- Modern approach: Use Pipeline (Jenkinsfile) instead

---

### Exercise 3 Solution
```groovy
pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-username/api-automation.git', branch: 'main'
                echo 'Code checked out successfully'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
                echo 'Project built successfully'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
                echo 'Tests executed'
            }
        }
    }
}
```

**Explanation:**
- `pipeline {}`: Root block for declarative pipeline
- `agent any`: Run on any available Jenkins agent
- `stages {}`: Container for all stages
- `stage('Name') {}`: Logical grouping of steps
- `steps {}`: Actual commands to execute
- `sh 'command'`: Execute shell command (Linux/Mac)
- `bat 'command'`: Use this for Windows agents

**Key Takeaways:**
- Declarative syntax is structured and easier to learn
- Each stage appears as a separate box in Jenkins UI
- `echo` statements help with debugging (visible in console output)

---

### Exercise 4 Solution
```groovy
pipeline {
    agent any

    tools {
        maven 'Maven-3.9.5'
        jdk 'JDK-17'
    }

    environment {
        API_BASE_URL = 'https://reqres.in/api'
        TEST_ENV = 'QA'
        BUILD_USER = 'Jenkins-CI'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-username/api-automation.git', branch: 'main'
                echo "✓ Code checked out from ${env.GIT_BRANCH}"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
                echo '✓ Project compiled and packaged'
            }
        }

        stage('Run API Tests') {
            steps {
                echo "Running ${env.TEST_ENV} tests against ${env.API_BASE_URL}"
                sh 'mvn test -DAPI_BASE_URL=${API_BASE_URL}'
            }
        }

        stage('Generate Reports') {
            steps {
                echo 'Generating test reports...'
                // Allure report generation would go here
                sh 'mvn surefire-report:report'
            }
        }
    }

    post {
        always {
            junit testResults: 'target/surefire-reports/*.xml', allowEmptyResults: true
            echo 'Test results published'
        }

        success {
            echo '🎉 SUCCESS! All tests passed.'
            echo "Build #${env.BUILD_NUMBER} completed by ${env.BUILD_USER}"
        }

        failure {
            echo '❌ FAILURE! Some tests failed.'
            echo "Check console output: ${env.BUILD_URL}console"
        }

        cleanup {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}
```

**Explanation:**
- **tools block:** Auto-installs specified versions of Maven and JDK
- **environment block:** Define variables accessible in all stages via `${env.VARIABLE}`
- **post block:** Actions that run after all stages complete
  - `always`: Runs regardless of success/failure (perfect for publishing reports)
  - `success`: Only runs if all stages succeed
  - `failure`: Only runs if any stage fails
  - `cleanup`: Runs after everything else (workspace cleanup)

**Key Takeaways:**
- Environment variables make pipelines reusable across environments
- `post { always {} }` ensures reports are published even when tests fail
- Use `junit` plugin to visualize test results in Jenkins UI
- `cleanWs()` prevents disk space issues on Jenkins server

---

### Exercise 5 Solution
```yaml
name: API Test Automation

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  api-tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'maven'

      - name: Build project
        run: mvn clean install -DskipTests

      - name: Run API tests
        run: mvn test
        env:
          API_BASE_URL: https://reqres.in/api

      - name: Upload test reports
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-reports
          path: target/surefire-reports/

      - name: Upload Allure results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: allure-results
          path: target/allure-results/
```

**Explanation:**
- **on:** Defines when workflow runs (push, PR, schedule, manual)
- **jobs:** Workflows can have multiple jobs (run in parallel by default)
- **runs-on:** Specifies runner OS (ubuntu-latest, windows-latest, macos-latest)
- **steps:** Sequential actions within a job
- **uses:** Executes a pre-built action from GitHub Marketplace
- **run:** Executes a shell command
- **if: always():** Runs step even if previous steps failed
- **cache: 'maven':** Automatically caches ~/.m2 directory

**Key Takeaways:**
- GitHub Actions YAML is simpler than Jenkinsfile
- Caching drastically reduces build time (5 min → 2 min)
- Upload artifacts to download reports later
- `if: always()` is critical for publishing reports on test failures

---

### Exercise 6 Solution
```yaml
name: API Test Automation with Schedule

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  schedule:
    - cron: '0 2 * * *'      # Daily at 2 AM UTC
    - cron: '0 9 * * 1'      # Monday at 9 AM UTC
  workflow_dispatch:          # Manual trigger

jobs:
  api-tests:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'maven'

      - name: Build project
        run: mvn clean install -DskipTests

      - name: Run Smoke Tests (on push/PR)
        if: github.event_name != 'schedule'
        run: mvn test -Dgroups=smoke

      - name: Run Full Regression (on schedule)
        if: github.event_name == 'schedule'
        run: mvn test

      - name: Upload test reports
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-reports-${{ github.run_number }}
          path: target/surefire-reports/
```

**Explanation:**
- **Cron syntax:** `minute hour day month day-of-week`
  - `0 2 * * *` = At minute 0 of hour 2, every day
  - `0 9 * * 1` = At minute 0 of hour 9, only on Monday (1=Monday)
- **workflow_dispatch:** Adds "Run workflow" button in GitHub UI (manual trigger)
- **github.event_name:** Built-in variable indicating trigger type
  - `push`, `pull_request`, `schedule`, `workflow_dispatch`
- **Conditional steps:** `if: github.event_name == 'schedule'` runs only on cron

**Key Takeaways:**
- Use cron for scheduled runs (nightly regression, hourly smoke tests)
- GitHub Actions cron uses UTC timezone (convert your local time to UTC)
- Run different test suites based on trigger type (fast smoke on PR, full regression nightly)
- Multiple cron schedules allowed (daily + weekly)

---

### Exercise 7 Solution
```groovy
pipeline {
    agent any

    tools {
        maven 'Maven-3.9.5'
        jdk 'JDK-17'
    }

    environment {
        API_BASE_URL = 'https://reqres.in/api'
        SLACK_CHANNEL = '#qa-alerts'
        SLACK_CREDENTIAL_ID = 'slack-webhook'
    }

    options {
        timeout(time: 10, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '30'))
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-username/api-automation.git'
                echo "✓ Code checked out from main branch"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests'
                echo "✓ Project built successfully"
            }
        }

        stage('Parallel Tests') {
            parallel {
                stage('Smoke Tests') {
                    steps {
                        script {
                            try {
                                sh 'mvn test -Dgroups=smoke'
                                echo "✓ Smoke tests passed (15/15)"
                            } catch (Exception e) {
                                echo "⚠ Smoke tests failed. Retrying..."
                                sh 'mvn test -Dgroups=smoke -DretryFailedTestsCount=1'
                            }
                        }
                    }
                }

                stage('Regression Tests') {
                    steps {
                        script {
                            try {
                                sh 'mvn test -Dgroups=regression'
                                echo "✓ Regression tests passed (47/47)"
                            } catch (Exception e) {
                                echo "⚠ Regression tests failed. Retrying..."
                                sh 'mvn test -Dgroups=regression -DretryFailedTestsCount=1'
                            }
                        }
                    }
                }
            }
        }

        stage('Generate Allure Report') {
            steps {
                allure includeProperties: false,
                       jdk: '',
                       results: [[path: 'target/allure-results']]
                echo "✓ Allure report generated"
            }
        }
    }

    post {
        always {
            junit testResults: 'target/surefire-reports/*.xml', allowEmptyResults: true
            archiveArtifacts artifacts: 'target/surefire-reports/**, target/allure-results/**',
                             allowEmptyArchive: true
        }

        success {
            script {
                def duration = currentBuild.durationString.replace(' and counting', '')
                slackSend(
                    channel: env.SLACK_CHANNEL,
                    color: 'good',
                    message: """
                        ✅ *Build #${env.BUILD_NUMBER} PASSED*
                        Repository: api-automation
                        Branch: ${env.GIT_BRANCH}
                        Duration: ${duration}
                        Smoke Tests: 15/15 ✓
                        Regression Tests: 47/47 ✓
                        Report: ${env.BUILD_URL}allure
                    """,
                    tokenCredentialId: env.SLACK_CREDENTIAL_ID
                )
                echo "✅ Pipeline completed successfully!"
            }
        }

        failure {
            script {
                def duration = currentBuild.durationString.replace(' and counting', '')
                slackSend(
                    channel: env.SLACK_CHANNEL,
                    color: 'danger',
                    message: """
                        ❌ *Build #${env.BUILD_NUMBER} FAILED*
                        Repository: api-automation
                        Branch: ${env.GIT_BRANCH}
                        Duration: ${duration}
                        Console: ${env.BUILD_URL}console
                        @channel Please investigate!
                    """,
                    tokenCredentialId: env.SLACK_CREDENTIAL_ID
                )
                echo "❌ Pipeline failed! Check logs."
            }
        }

        cleanup {
            cleanWs()
        }
    }
}
```

**Explanation:**
- **options block:** Pipeline-level settings
  - `timeout`: Abort pipeline if takes >10 minutes (prevents hanging builds)
  - `buildDiscarder`: Keep only last 30 builds (saves disk space)
- **parallel block:** Runs stages simultaneously (cuts execution time in half)
- **script block:** Allows Groovy scripting (for try-catch, variables, etc.)
- **try-catch:** Retry failed tests before marking as failure
- **slackSend:** Sends formatted message to Slack (requires Slack plugin + webhook)
- **currentBuild:** Built-in object with build metadata (duration, number, result)

**Key Takeaways:**
- Parallel stages reduce total pipeline time (if tests are independent)
- Retry logic prevents false negatives from flaky tests
- Rich Slack notifications provide actionable information (link to console, report)
- `@channel` in Slack message alerts entire team (use sparingly!)

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Jenkins Setup | ☐ | __/10 | ___ min | |
| 2 - Freestyle Job | ☐ | __/10 | ___ min | |
| 3 - Basic Jenkinsfile | ☐ | __/10 | ___ min | |
| 4 - Complete Jenkinsfile | ☐ | __/10 | ___ min | |
| 5 - GitHub Actions | ☐ | __/10 | ___ min | |
| 6 - Scheduled Execution | ☐ | __/10 | ___ min | |
| 7 - Full Pipeline | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Areas I struggled with:**
- [ ] Jenkins configuration
- [ ] Jenkinsfile syntax
- [ ] GitHub Actions YAML
- [ ] Cron expressions
- [ ] Slack integration

**Areas I excelled at:**
- [ ] Docker commands
- [ ] Pipeline structure
- [ ] Environment variables
- [ ] Test reporting

---

## ✅ Answers to Self-Check Questions

**Q1:** What's the difference between CI and CD?
**A:** CI (Continuous Integration) = automatically build and test code on every commit. CD (Continuous Delivery) = code is always deployable, manual approval for production. CD (Continuous Deployment) = automatic deployment to production without approval.

**Q2:** Why do SDETs need CI/CD knowledge?
**A:** Tests must run automatically on every code change. SDETs design pipelines, integrate test frameworks, configure test execution schedules, and set up reporting/notifications. 90% of product companies expect this skill.

**Q3:** Jenkins vs GitHub Actions - when to use each?
**A:** **Jenkins:** Large enterprises, need high customization, self-hosted infrastructure, complex pipelines. **GitHub Actions:** Startups, GitHub-based projects, zero setup, simple pipelines, limited free tier.

**Q4:** Cron for "Run tests every weekday at 6 PM":
**A:** `0 18 * * 1-5` (0 minutes, 18th hour, every day, every month, Monday-Friday)

**Q5:** What happens if you run `mvn test` without `mvn clean install` first?
**A:** In a fresh CI environment (no previous builds), it fails with "package does not exist" because Java code isn't compiled yet. In local environment, it might use stale compiled classes from previous run (can cause false positives/negatives).

---
