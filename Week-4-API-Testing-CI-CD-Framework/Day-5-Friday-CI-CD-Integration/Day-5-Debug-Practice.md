# DAY 5 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

CI/CD pipelines fail for many reasons: syntax errors, environment issues, permission problems, or test failures. As an SDET, you'll spend significant time debugging pipeline failures in production environments. These challenges simulate real-world scenarios you'll encounter at product companies.

**Skills You'll Build:**
- Reading and interpreting Jenkins/GitHub Actions error messages
- Identifying syntax errors in Jenkinsfile and YAML
- Troubleshooting environment variable issues
- Debugging permission and authentication problems
- Fixing build failures and test execution errors

---

## Challenge 1: Jenkinsfile Syntax Error

**Scenario:**
Your teammate committed this Jenkinsfile, but the pipeline fails to start with a syntax error.

**Broken Code:**
```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Test')
            steps {
                sh 'mvn test'
            }
        }
    }
}
```

**Error Message:**
```
WorkflowScript: 12: Expected a stage block @ line 12, column 9.
           stage('Test')
           ^
```

**Your Task:**
1. Identify the syntax error
2. Explain why it causes a problem
3. Fix the code

**Hints:**
- Look at the structure of the 'Build' stage vs 'Test' stage
- Every `stage` block needs something after the name

---

**Solution:**

**Bug Location:** Line 12 - Missing opening brace `{` after `stage('Test')`

**Explanation:**
In declarative pipelines, every `stage('Name')` must be followed by an opening brace `{`. The code is missing the brace between `stage('Test')` and `steps`.

**Fixed Code:**
```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Test') {              // Added { here
            steps {
                sh 'mvn test'
            }
        }
    }
}
```

**Key Learning:**
Declarative pipeline syntax is strict:
```groovy
stage('Name') {    // Must have opening brace
    steps {
        // commands
    }
}                  // Must have closing brace
```

Common mistake: Forgetting braces after `stage('Name')`, `parallel`, or `script` blocks.

---

## Challenge 2: GitHub Actions YAML Indentation Error

**Scenario:**
Your GitHub Actions workflow fails with "Invalid workflow file" error.

**Broken Code:**
```yaml
name: API Tests

on:
  push:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

    - name: Run tests
      run: mvn test
```

**Error Message:**
```
.github/workflows/test.yml:
  line 14: mapping values are not allowed in this context
```

**Your Task:**
1. Find the indentation error
2. Explain why YAML is so sensitive to indentation
3. Fix the workflow

**Hints:**
- YAML uses spaces (not tabs) for indentation
- All items in a list must be at the same indentation level
- The `-` character denotes a list item

---

**Solution:**

**Bug Location:** Line 14 - Incorrect indentation for "Setup Java" step

**Explanation:**
YAML requires consistent indentation for list items. Steps in GitHub Actions are a list (denoted by `-`). The "Setup Java" step is indented too far (6 spaces instead of 4), making it appear as a child of the "Checkout" step rather than a sibling.

**Fixed Code:**
```yaml
name: API Tests

on:
  push:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout                    # 6 spaces before -, 8 before name
        uses: actions/checkout@v4

      - name: Setup Java                  # Same indentation as Checkout
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'

      - name: Run tests
        run: mvn test
```

**Indentation Rules:**
```yaml
steps:              # Parent (4 spaces)
  - name: Step1     # List item (6 spaces for -, 8 for name)
    uses: action    # Properties (8 spaces)
  - name: Step2     # Next list item (same 6 spaces for -)
    run: command    # Properties (8 spaces)
```

**Key Learning:**
YAML indentation errors are the #1 cause of GitHub Actions failures. Use a YAML validator (yamllint.com) or IDE with YAML support to catch these early.

**Pro Tip:** Configure your editor to show whitespace characters and set "spaces: 2" for YAML files.

---

## Challenge 3: Environment Variable Not Found

**Scenario:**
Your pipeline fails during test execution because the API_BASE_URL environment variable is not set.

**Broken Code:**
```groovy
pipeline {
    agent any

    environment {
        TEST_ENV = 'QA'
    }

    stages {
        stage('Test') {
            steps {
                sh 'mvn test -DAPI_BASE_URL=${API_URL}'
            }
        }
    }
}
```

**Error Message:**
```
[ERROR] Failed to execute goal: The parameter 'API_BASE_URL' is required but was not set.
Tests run: 0, Failures: 0, Errors: 0, Skipped: 0
BUILD FAILURE
```

**Your Task:**
1. Identify why API_BASE_URL is not being set
2. Explain the difference between defining and using environment variables
3. Fix the pipeline

**Hints:**
- Look at the environment block - what variable is defined?
- Look at the test command - what variable is referenced?
- Variable names must match exactly (case-sensitive)

---

**Solution:**

**Bug Location:** Line 11 - Variable name mismatch (`API_URL` vs `API_BASE_URL`)

**Explanation:**
The environment block defines `API_BASE_URL` is not declared anywhere. There's a typo: defined as one name, referenced as another. Environment variables are case-sensitive and must match exactly.

**Root Cause:**
```groovy
environment {
    TEST_ENV = 'QA'           // API_BASE_URL is NOT defined here
}

steps {
    sh 'mvn test -DAPI_BASE_URL=${API_URL}'  // Referencing undefined API_URL
}
```

**Fixed Code:**
```groovy
pipeline {
    agent any

    environment {
        TEST_ENV = 'QA'
        API_BASE_URL = 'https://reqres.in/api'    // Define the variable
    }

    stages {
        stage('Test') {
            steps {
                sh 'mvn test -DAPI_BASE_URL=${API_BASE_URL}'  // Use correct name
            }
        }
    }
}
```

**Alternative Fix (Reference Existing Variable):**
If `API_BASE_URL` is defined elsewhere (Jenkins credentials, system env), use it correctly:
```groovy
sh 'mvn test -DAPI_BASE_URL=${env.API_BASE_URL}'
```

**Key Learning:**
**Defining vs Using Environment Variables:**

```groovy
// Define in environment block
environment {
    MY_VAR = 'value'              // Define
}

// Use in steps
steps {
    sh 'echo ${MY_VAR}'           // Use (shell interpolation)
    sh 'echo ${env.MY_VAR}'       // Use (Groovy interpolation)
    echo env.MY_VAR               // Use (Groovy echo)
}
```

**Debugging Environment Variables:**
```groovy
steps {
    sh 'printenv | sort'          // Print all environment variables
    echo "API_BASE_URL = ${env.API_BASE_URL}"  // Debug specific variable
}
```

---

## Challenge 4: Permission Denied Error

**Scenario:**
Your Jenkins pipeline fails when trying to execute a shell script.

**Broken Code:**
```groovy
pipeline {
    agent any

    stages {
        stage('Setup') {
            steps {
                sh 'chmod +x ./scripts/setup.sh'
                sh './scripts/setup.sh'
            }
        }

        stage('Test') {
            steps {
                sh './scripts/run-tests.sh'    // FAILS HERE
            }
        }
    }
}
```

**Error Message:**
```
+ ./scripts/run-tests.sh
/var/jenkins_home/workspace/api-tests@tmp/durable-abc123/script.sh: line 1:
./scripts/run-tests.sh: Permission denied
Build step 'Execute shell' marked build as failure
```

**Your Task:**
1. Identify why the second script fails when the first succeeds
2. Explain file permissions in Unix/Linux
3. Fix the pipeline

**Hints:**
- The first script (`setup.sh`) works after chmod
- The second script (`run-tests.sh`) doesn't have the same chmod
- Each script needs execute permission

---

**Solution:**

**Bug Location:** Line 14 - Missing `chmod +x` for `run-tests.sh`

**Explanation:**
In Unix/Linux, files don't have execute permission by default. The `chmod +x` command grants execute permission. The pipeline gives execute permission to `setup.sh` but forgets to do the same for `run-tests.sh`.

**Fixed Code:**
```groovy
pipeline {
    agent any

    stages {
        stage('Setup') {
            steps {
                sh 'chmod +x ./scripts/setup.sh'
                sh './scripts/setup.sh'
            }
        }

        stage('Test') {
            steps {
                sh 'chmod +x ./scripts/run-tests.sh'    // Add this line
                sh './scripts/run-tests.sh'
            }
        }
    }
}
```

**Better Solution (Make All Scripts Executable at Once):**
```groovy
pipeline {
    agent any

    stages {
        stage('Setup') {
            steps {
                sh 'chmod +x ./scripts/*.sh'    // Make all .sh files executable
                sh './scripts/setup.sh'
            }
        }

        stage('Test') {
            steps {
                sh './scripts/run-tests.sh'    // Already executable from Setup stage
            }
        }
    }
}
```

**Best Solution (Don't Rely on Execute Permission):**
```groovy
// Call scripts with bash explicitly (no execute permission needed)
sh 'bash ./scripts/setup.sh'
sh 'bash ./scripts/run-tests.sh'
```

**Key Learning:**
**File Permissions in Unix/Linux:**
```bash
ls -la scripts/
# -rw-r--r--  setup.sh      # Not executable (no 'x')
# -rwxr-xr-x  run-tests.sh  # Executable ('x' present)

chmod +x setup.sh           # Add execute permission
chmod 755 setup.sh          # Same as above (7=rwx, 5=rx)
```

**Permission Denied Errors:**
- `Permission denied` when running `./script.sh` = missing execute permission
- `Permission denied` when writing file = missing write permission
- `Permission denied` when reading file = missing read permission

---

## Challenge 5: Maven Build Failure - Missing Plugin

**Scenario:**
Your GitHub Actions workflow fails during the Maven build step.

**Broken Code:**
```yaml
name: API Tests

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'

      - name: Build
        run: mvn clean install -DskipTests

      - name: Test
        run: mvn test
```

**Error Message:**
```
[ERROR] Plugin org.apache.maven.plugins:maven-compiler-plugin:3.11.0 or one of its dependencies could not be resolved: Failed to read artifact descriptor for org.apache.maven.plugins:maven-compiler-plugin:jar:3.11.0: Could not transfer artifact org.apache.maven.plugins:maven-compiler-plugin:pom:3.11.0 from/to central (https://repo.maven.apache.org/maven2): Connection reset -> [Help 1]
```

**Your Task:**
1. Identify the root cause (not missing plugin, but missing configuration)
2. Understand why this works locally but fails in CI
3. Fix the workflow

**Hints:**
- The error mentions "could not be resolved" (network issue)
- Local machine has cached Maven dependencies
- CI environment starts fresh every time
- Compare with the working examples from exercises

---

**Solution:**

**Bug Location:** Line 13-15 - Missing `distribution` and `cache` in Java setup

**Explanation:**
The error isn't about a missing plugin—it's a network/timeout issue when downloading Maven dependencies. The `setup-java` action needs:
1. **distribution:** Specifies which JDK to use (temurin, adopt, zulu, etc.)
2. **cache:** Caches Maven dependencies between runs (speeds up builds and prevents repeated downloads)

Without caching, CI downloads all dependencies on every run. If Maven Central is slow or times out, the build fails.

**Fixed Code:**
```yaml
name: API Tests

on: [push]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'        # Add this
          cache: 'maven'                 # Add this

      - name: Build
        run: mvn clean install -DskipTests

      - name: Test
        run: mvn test
```

**What Changed:**
- **distribution: 'temurin'** - Uses Eclipse Temurin OpenJDK (free, popular, well-maintained)
- **cache: 'maven'** - Caches `~/.m2/repository` directory between workflow runs

**Key Learning:**
**Maven Caching in CI/CD:**

```yaml
# Without cache: Downloads 200MB+ dependencies every run (5-10 minutes)
- uses: actions/setup-java@v4
  with:
    java-version: '17'

# With cache: Downloads once, reuses cached deps (30 seconds)
- uses: actions/setup-java@v4
  with:
    java-version: '17'
    distribution: 'temurin'
    cache: 'maven'              # Caches ~/.m2/repository
```

**Cache Benefits:**
- ✅ Faster builds (10x speedup)
- ✅ Less network dependency (won't fail if Maven Central is slow)
- ✅ Saves GitHub Actions minutes (cost savings)

**Debugging Maven Issues:**
```yaml
- name: Debug Maven
  run: |
    mvn --version
    mvn dependency:tree          # Show all dependencies
    ls -la ~/.m2/repository      # Check cached dependencies
```

---

## Challenge 6: Cron Expression Not Triggering

**Scenario:**
You want your tests to run every weekday at 9 AM IST (India Standard Time, UTC+5:30), but the workflow never triggers.

**Broken Code:**
```yaml
name: Scheduled API Tests

on:
  schedule:
    - cron: '0 9 * * 1-5'    # Every weekday at 9 AM IST

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run tests
        run: echo "Tests running at $(date)"
```

**Problem:**
The workflow doesn't run at the expected time (9 AM IST). When you check the Actions tab, there are no scheduled runs.

**Your Task:**
1. Identify the timezone issue
2. Convert IST to UTC correctly
3. Fix the cron expression

**Hints:**
- GitHub Actions cron uses UTC timezone (not local time)
- IST = UTC + 5:30 (5 hours 30 minutes ahead of UTC)
- 9:00 AM IST = ? UTC

---

**Solution:**

**Bug Location:** Line 5 - Cron expression assumes UTC but should be adjusted for IST

**Explanation:**
GitHub Actions cron uses **UTC timezone only**. IST is UTC+5:30, so:
- 9:00 AM IST = 9:00 - 5:30 = 3:30 AM UTC

The cron `0 9 * * 1-5` runs at 9:00 AM UTC (which is 2:30 PM IST, not 9:00 AM IST).

**Time Conversion:**
```
Target: 9:00 AM IST
IST = UTC + 5:30
UTC = IST - 5:30
UTC = 9:00 - 5:30 = 3:30 AM
Cron: '30 3 * * 1-5'
```

**Fixed Code:**
```yaml
name: Scheduled API Tests

on:
  schedule:
    - cron: '30 3 * * 1-5'    # 9:00 AM IST = 3:30 AM UTC

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Show execution time
        run: |
          echo "UTC Time: $(date -u)"
          echo "IST Time: $(TZ='Asia/Kolkata' date)"

      - name: Run tests
        run: mvn test
```

**Key Learning:**
**Timezone Conversions for Common Schedules:**

| Local Time | Timezone | UTC Equivalent | Cron Expression |
|------------|----------|----------------|-----------------|
| 9:00 AM IST | UTC+5:30 | 3:30 AM UTC | `30 3 * * *` |
| 6:00 PM PST | UTC-8 | 2:00 AM UTC (next day) | `0 2 * * *` |
| 12:00 PM EST | UTC-5 | 5:00 PM UTC | `0 17 * * *` |
| 2:00 AM GMT | UTC+0 | 2:00 AM UTC | `0 2 * * *` |

**Debugging Cron Schedules:**
```yaml
- name: Debug timezone
  run: |
    date -u                           # UTC time
    TZ='Asia/Kolkata' date            # IST time
    TZ='America/Los_Angeles' date     # PST time
```

**Cron Gotchas:**
1. **GitHub Actions cron is not 100% precise** - May run 5-15 minutes late during high load
2. **Minimum interval: 5 minutes** - Can't run more frequently than `*/5 * * * *`
3. **Scheduled workflows on default branch only** - Must be merged to main/master
4. **Inactive repos may have cron disabled** - GitHub disables cron if no activity for 60 days

**Testing Cron Expressions:**
Use https://crontab.guru/ to validate cron syntax:
- `30 3 * * 1-5` → "At 03:30 on every day-of-week from Monday through Friday"

---

## Challenge 7: Slack Notification Not Sending

**Scenario:**
Your Jenkinsfile includes Slack notifications, but no messages appear in Slack despite pipeline failures.

**Broken Code:**
```groovy
pipeline {
    agent any

    environment {
        SLACK_CHANNEL = '#qa-alerts'
    }

    stages {
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }

    post {
        failure {
            slackSend(
                channel: env.SLACK_CHANNEL,
                message: "Build failed!",
                color: 'danger'
            )
        }
    }
}
```

**Error in Jenkins Console:**
```
[Slack Notifications] failed to send notification
java.lang.IllegalArgumentException: credential not found
```

**Your Task:**
1. Identify what's missing for Slack integration
2. Explain how Jenkins credentials work
3. Fix the pipeline

**Hints:**
- Slack requires authentication (webhook URL or OAuth token)
- Jenkins stores credentials securely (not in Jenkinsfile)
- `slackSend` needs a credential ID

---

**Solution:**

**Bug Location:** Line 18-23 - Missing `tokenCredentialId` parameter

**Explanation:**
The `slackSend` function requires authentication to post messages to Slack. Jenkins stores the Slack webhook URL or token as a credential (for security). The Jenkinsfile must reference this credential by its ID, but it's missing from the code.

**Prerequisites:**
1. **Slack Setup:**
   - Create Slack app: https://api.slack.com/apps
   - Enable "Incoming Webhooks"
   - Create webhook for #qa-alerts channel
   - Copy webhook URL: `https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXX`

2. **Jenkins Setup:**
   - Go to "Manage Jenkins" → "Manage Credentials"
   - Add credential: Type = "Secret text"
   - Secret = Paste webhook URL
   - ID = `slack-webhook` (reference this in Jenkinsfile)

**Fixed Code:**
```groovy
pipeline {
    agent any

    environment {
        SLACK_CHANNEL = '#qa-alerts'
        SLACK_CREDENTIAL_ID = 'slack-webhook'    // Add credential ID
    }

    stages {
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }

    post {
        failure {
            slackSend(
                channel: env.SLACK_CHANNEL,
                message: "❌ Build #${env.BUILD_NUMBER} failed! Check: ${env.BUILD_URL}",
                color: 'danger',
                tokenCredentialId: env.SLACK_CREDENTIAL_ID    // Add this parameter
            )
        }

        success {
            slackSend(
                channel: env.SLACK_CHANNEL,
                message: "✅ Build #${env.BUILD_NUMBER} passed!",
                color: 'good',
                tokenCredentialId: env.SLACK_CREDENTIAL_ID
            )
        }
    }
}
```

**Key Learning:**
**Jenkins Credentials Management:**

```groovy
// ❌ WRONG: Hardcoding secrets in Jenkinsfile
environment {
    SLACK_WEBHOOK = 'https://hooks.slack.com/services/ABC/DEF/XYZ'
}

// ✅ CORRECT: Using Jenkins credentials
environment {
    SLACK_WEBHOOK = credentials('slack-webhook-id')
}
```

**Credential Types:**
| Type | Use Case | Example |
|------|----------|---------|
| **Secret text** | API keys, webhooks | Slack webhook URL |
| **Username/Password** | Login credentials | GitHub username/token |
| **SSH Username with private key** | Git over SSH | Deploy key |
| **Secret file** | Config files | Service account JSON |

**Testing Slack Integration:**
```groovy
stage('Test Slack') {
    steps {
        slackSend(
            channel: '#test',
            message: 'Testing Slack integration from Jenkins',
            tokenCredentialId: 'slack-webhook'
        )
    }
}
```

**Debugging Slack Issues:**
1. **Credential not found:** Check credential ID matches exactly (case-sensitive)
2. **Invalid token:** Regenerate webhook in Slack, update Jenkins credential
3. **Channel not found:** Ensure channel exists and bot is invited to channel
4. **Message not formatted:** Use triple quotes for multi-line messages:
   ```groovy
   message: """
       Build: ${env.BUILD_NUMBER}
       Status: FAILED
   """
   ```

---

## 🎯 Debugging Checklist

When your CI/CD pipeline fails, follow this systematic approach:

### 1. Read the Error Message Carefully
```
✓ What stage/step failed?
✓ What's the exact error message?
✓ Is it a syntax error, runtime error, or test failure?
```

### 2. Check Recent Changes
```
✓ What changed since the last successful build?
✓ New code commits?
✓ Pipeline configuration changes?
✓ Dependency updates?
```

### 3. Verify Environment
```
✓ Are all environment variables set?
✓ Correct Java/Maven versions?
✓ Dependencies downloadable (network issues)?
```

### 4. Test Locally
```bash
# Can you reproduce the error locally?
mvn clean install
mvn test

# Check if scripts are executable
ls -la scripts/
chmod +x scripts/*.sh
```

### 5. Enable Debug Logging
```groovy
// Jenkins: Add debug output
steps {
    sh 'mvn test -X'    // -X = debug mode
    sh 'printenv | sort'    // Print all env vars
}
```

```yaml
# GitHub Actions: Enable debug logs
# Settings → Secrets → Add ACTIONS_STEP_DEBUG = true
```

### 6. Common Quick Fixes
```groovy
// Clean workspace before build
cleanWs()

// Increase timeout
options {
    timeout(time: 30, unit: 'MINUTES')
}

// Retry flaky steps
retry(3) {
    sh 'mvn test'
}
```

---

## 🚀 Pro Tips for Faster Debugging

**Tip 1: Use Pipeline Replay**
In Jenkins, use "Replay" to edit and re-run pipeline without committing changes.

**Tip 2: Comment Out Stages**
Isolate the problem by commenting out stages:
```groovy
// stage('Build') {
//     steps { sh 'mvn install' }
// }

stage('Test') {
    steps { sh 'mvn test' }  // Only run this stage
}
```

**Tip 3: Add Echo Statements Everywhere**
```groovy
steps {
    echo "Starting test stage..."
    echo "API_BASE_URL = ${env.API_BASE_URL}"
    sh 'mvn test'
    echo "Tests completed!"
}
```

**Tip 4: Compare Working vs Broken Pipelines**
Use `git diff` to see exactly what changed:
```bash
git diff HEAD~1 Jenkinsfile
```

**Tip 5: Check Jenkins/GitHub Status Pages**
Sometimes it's not your fault:
- https://www.githubstatus.com/
- Check your Jenkins server health

---

## 📊 Debugging Skills Assessment

Rate your confidence in debugging these issues:

| Issue Type | Before Practice | After Practice | Notes |
|------------|-----------------|----------------|-------|
| Jenkinsfile syntax errors | __/10 | __/10 | |
| YAML indentation issues | __/10 | __/10 | |
| Environment variables | __/10 | __/10 | |
| Permission denied errors | __/10 | __/10 | |
| Maven build failures | __/10 | __/10 | |
| Cron/scheduling issues | __/10 | __/10 | |
| Notification setup | __/10 | __/10 | |

**Goal:** Score 8+ on all items before moving to next week!

---
