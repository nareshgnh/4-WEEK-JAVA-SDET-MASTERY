# DAY 4: MAVEN & BUILD TOOLS

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Understand Maven fundamentals and why it's critical for Java projects
- [ ] Master Maven project structure (src/main/java, src/test/java, pom.xml)
- [ ] Deep dive into pom.xml anatomy (dependencies, plugins, properties, profiles)
- [ ] Manage dependencies effectively (versions, scopes, resolving conflicts)
- [ ] Use Maven plugins (Surefire, Compiler, Assembly, JAR)
- [ ] Understand Maven build lifecycle (compile, test, package, install, deploy)
- [ ] Create multi-environment setups using profiles
- [ ] Run tests from command line like a pro

**Time Required:** 2.5-3 hours
**Difficulty:** Medium
**Prerequisite:** Day 1-3 - RestAssured API testing, Java basics

---

## 📚 Core Concepts

### Concept 1: What is Maven and Why Every SDET Needs It

**What is it?**
Maven is a build automation and dependency management tool for Java projects. Think of it as the "package manager + build system" for Java - like pip + setuptools in Python, but more powerful.

**Why does it matter for automation?**
As an SDET in product companies:
- 100% of enterprise Java projects use Maven or Gradle
- Interview questions always include Maven knowledge
- You can't run automation frameworks without understanding build tools
- CI/CD pipelines rely on Maven commands
- Team collaboration requires standardized project structure

**The Problem Maven Solves:**

Before Maven (nightmare scenario):
```bash
# Download JARs manually
# Copy to lib folder
# Set classpath manually
# Compile: javac -cp "lib/*" src/Test.java
# Run: java -cp "lib/*:src" Test
# Dependencies conflict? Good luck! 😱
```

With Maven (professional way):
```xml
<!-- Just add this to pom.xml -->
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
</dependency>
```
```bash
# Maven handles everything
mvn clean test
```

**Real Example:**
```java
// Your test automation project needs:
// - RestAssured for API testing
// - TestNG for test execution
// - Jackson for JSON handling
// - Extent Reports for reporting
// - Log4j for logging

// Without Maven: Download 50+ JAR files manually (each dependency has its own dependencies)
// With Maven: Add 5 dependencies in pom.xml, Maven downloads everything (transitive dependencies)
```

**Explanation:**
Maven automatically downloads libraries from Maven Central Repository, manages versions, handles transitive dependencies (dependencies of dependencies), and ensures everyone on the team uses the same versions.

---

### Concept 2: Maven Project Structure - Convention Over Configuration

**What is it?**
Maven follows a standardized directory structure. When you follow this convention, Maven knows where to find your code, tests, resources without any configuration.

**Standard Maven Directory Structure:**
```
my-automation-project/
│
├── src/
│   ├── main/
│   │   ├── java/              # Application source code (Page Objects, utilities)
│   │   │   └── com/company/
│   │   │       ├── pages/
│   │   │       ├── utils/
│   │   │       └── config/
│   │   └── resources/          # Configuration files (log4j.xml, config.properties)
│   │       ├── log4j.xml
│   │       └── config.properties
│   │
│   └── test/
│       ├── java/               # Test source code (Test classes)
│       │   └── com/company/
│       │       ├── tests/
│       │       └── runners/
│       └── resources/          # Test resources (test data, JSON files)
│           ├── testdata/
│           └── payloads/
│
├── target/                     # Build output (compiled classes, reports) - GITIGNORE THIS
│   ├── classes/                # Compiled main code
│   ├── test-classes/           # Compiled test code
│   ├── surefire-reports/       # Test execution reports
│   └── my-project-1.0.jar      # Packaged JAR file
│
├── pom.xml                     # Maven configuration - THE BRAIN
└── .gitignore                  # Ignore target/, IDE files
```

**Why does it matter for automation?**
1. **Consistency:** Every Java project follows this structure
2. **IDE Integration:** IntelliJ/Eclipse recognize this structure automatically
3. **Build Tools:** Maven/Jenkins/GitHub Actions know where to find tests
4. **Team Collaboration:** No confusion about where to place files

**Real Example - API Automation Project:**
```
restassured-framework/
│
├── src/
│   ├── main/java/
│   │   └── com/automation/
│   │       ├── models/                 # POJOs
│   │       │   ├── User.java
│   │       │   └── LoginRequest.java
│   │       └── utils/
│   │           └── ConfigReader.java
│   │
│   └── test/java/
│       └── com/automation/
│           ├── tests/
│           │   ├── UserAPITest.java
│           │   └── AuthenticationTest.java
│           └── base/
│               └── BaseTest.java
│
├── pom.xml
└── target/
    └── surefire-reports/
        └── TEST-com.automation.tests.UserAPITest.xml
```

**Common Use Cases in Test Automation:**
1. **Page Objects** → `src/main/java/pages/`
2. **Test Classes** → `src/test/java/tests/`
3. **Test Data** → `src/test/resources/testdata/`
4. **Utilities** → `src/main/java/utils/`
5. **Configuration** → `src/main/resources/config.properties`

---

### Concept 3: pom.xml - The Heart of Maven Project

**What is it?**
POM (Project Object Model) is an XML file that contains all project configuration, dependencies, plugins, and build instructions.

**Anatomy of pom.xml:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <!-- 1. BASIC PROJECT INFO -->
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.company</groupId>          <!-- Organization/Company -->
    <artifactId>api-automation</artifactId>  <!-- Project Name -->
    <version>1.0-SNAPSHOT</version>          <!-- Project Version -->
    <packaging>jar</packaging>               <!-- jar/war/pom -->

    <name>API Automation Framework</name>
    <description>RestAssured API Testing Framework</description>

    <!-- 2. PROPERTIES (Variables) -->
    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <restassured.version>5.3.0</restassured.version>
        <testng.version>7.7.1</testng.version>
    </properties>

    <!-- 3. DEPENDENCIES (External Libraries) -->
    <dependencies>
        <!-- RestAssured for API Testing -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>${restassured.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- TestNG for Test Execution -->
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>${testng.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <!-- 4. BUILD PLUGINS -->
    <build>
        <plugins>
            <!-- Maven Compiler Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>11</source>
                    <target>11</target>
                </configuration>
            </plugin>

            <!-- Maven Surefire Plugin (Test Runner) -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0</version>
                <configuration>
                    <suiteXmlFiles>
                        <suiteXmlFile>testng.xml</suiteXmlFile>
                    </suiteXmlFiles>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

**Breaking Down Each Section:**

#### 1. Project Coordinates (GAV)
```xml
<groupId>com.company</groupId>          <!-- Like package name (reverse domain) -->
<artifactId>api-automation</artifactId>  <!-- Project name (lowercase, hyphen-separated) -->
<version>1.0-SNAPSHOT</version>          <!-- Version (SNAPSHOT = under development) -->
```

**Interview Tip:** This is called "Maven Coordinates" or "GAV" (GroupId-ArtifactId-Version). Together they uniquely identify a project in Maven ecosystem.

#### 2. Properties (Variables)
```xml
<properties>
    <restassured.version>5.3.0</restassured.version>  <!-- Define once -->
</properties>

<!-- Use everywhere -->
<version>${restassured.version}</version>              <!-- Reference variable -->
```

**Why use properties?**
- Change version in one place
- Maintain consistency across dependencies
- Easy to upgrade libraries

#### 3. Dependencies
```xml
<dependency>
    <groupId>io.rest-assured</groupId>      <!-- Who made it? -->
    <artifactId>rest-assured</artifactId>   <!-- What is it? -->
    <version>5.3.0</version>                <!-- Which version? -->
    <scope>test</scope>                     <!-- When to use? -->
</dependency>
```

**Interview Question:** Where does Maven download dependencies from?
**Answer:** Maven Central Repository (https://repo.maven.apache.org/maven2/)

---

### Concept 4: Dependency Scopes - When Dependencies Are Available

**What are they?**
Dependency scopes control when a library is available in your project (compile time, test time, runtime).

**The 5 Maven Scopes:**

#### 1. compile (Default)
Available everywhere - main code, test code, runtime.
```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.0</version>
    <scope>compile</scope>  <!-- Can be omitted (default) -->
</dependency>
```
**Use case:** Utilities, models, libraries used in main code

#### 2. test
Available only in test code and test execution. **Most common for test frameworks.**
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
    <scope>test</scope>  <!-- Only in src/test/java -->
</dependency>
```
**Use case:** TestNG, JUnit, RestAssured, Mockito

#### 3. provided
Available at compile time but provided by runtime environment.
```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
    <scope>provided</scope>  <!-- Server provides this -->
</dependency>
```
**Use case:** Servlet API (Tomcat provides it), Lombok (compile-time only)

#### 4. runtime
Not needed for compilation but needed at runtime.
```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
    <scope>runtime</scope>  <!-- Used only when running -->
</dependency>
```
**Use case:** Database drivers, logging implementations

#### 5. system
Similar to provided but you specify JAR path manually. **Avoid using this.**

**Comparison Table:**
| Scope | Compile Time | Test Compile | Runtime | Test Runtime | Packaged in JAR |
|-------|--------------|--------------|---------|--------------|-----------------|
| compile | ✅ | ✅ | ✅ | ✅ | ✅ |
| test | ❌ | ✅ | ❌ | ✅ | ❌ |
| provided | ✅ | ✅ | ❌ | ❌ | ❌ |
| runtime | ❌ | ❌ | ✅ | ✅ | ✅ |

**Real Example - API Automation Project:**
```xml
<dependencies>
    <!-- Compile scope - Used in POJOs and utilities -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.15.0</version>
        <!-- No scope = compile (default) -->
    </dependency>

    <!-- Test scope - Test frameworks -->
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.3.0</version>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.7.1</version>
        <scope>test</scope>
    </dependency>

    <!-- Provided scope - Lombok for POJOs -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.28</version>
        <scope>provided</scope>
    </dependency>
</dependencies>
```

---

### Concept 5: Maven Build Lifecycle - The Build Process

**What is it?**
Maven build lifecycle is a sequence of phases that execute in order. When you run a phase, all previous phases execute automatically.

**Three Built-in Lifecycles:**
1. **default** - Building and deploying
2. **clean** - Cleaning the project
3. **site** - Generating documentation

**Most Important: Default Lifecycle Phases (in order):**

```
validate → compile → test → package → verify → install → deploy
```

#### Phase 1: validate
```bash
mvn validate
```
- Validates project structure
- Checks if pom.xml is correct
- Rarely used explicitly

#### Phase 2: compile
```bash
mvn compile
```
- Compiles source code from `src/main/java`
- Outputs to `target/classes`
- Does NOT compile test code

**Example:**
```bash
$ mvn compile
[INFO] Compiling 15 source files to target/classes
[INFO] BUILD SUCCESS
```

#### Phase 3: test
```bash
mvn test
```
- Compiles main code (compile phase)
- Compiles test code from `src/test/java`
- Runs all tests using Surefire plugin
- Generates reports in `target/surefire-reports`

**Example:**
```bash
$ mvn test
[INFO] --- maven-compiler-plugin:3.11.0:compile ---
[INFO] Compiling 15 source files
[INFO] --- maven-compiler-plugin:3.11.0:testCompile ---
[INFO] Compiling 8 test sources
[INFO] --- maven-surefire-plugin:3.0.0:test ---
[INFO] Running TestSuite
[INFO] Tests run: 25, Failures: 0, Errors: 0, Skipped: 0
[INFO] BUILD SUCCESS
```

#### Phase 4: package
```bash
mvn package
```
- Runs all previous phases (validate, compile, test)
- Packages compiled code into JAR/WAR
- Output: `target/project-name-version.jar`

**Example:**
```bash
$ mvn package
[INFO] Building jar: target/api-automation-1.0.jar
[INFO] BUILD SUCCESS
```

#### Phase 5: install
```bash
mvn install
```
- Runs package phase
- Installs JAR to local Maven repository (~/.m2/repository)
- Makes it available for other local projects

#### Phase 6: deploy
```bash
mvn deploy
```
- Uploads JAR to remote repository (Nexus, Artifactory)
- For sharing with team/organization
- Requires repository configuration

**Clean Lifecycle:**
```bash
mvn clean        # Deletes target/ directory
mvn clean test   # Clean first, then run tests
mvn clean install # Most common combination
```

**Most Used Commands in Test Automation:**
```bash
mvn clean test              # Clean and run all tests
mvn test -Dtest=UserTest    # Run specific test class
mvn test -Dgroups=smoke     # Run tests with @Test(groups="smoke")
mvn clean install           # Build project and install locally
mvn clean package -DskipTests  # Package without running tests
```

---

### Concept 6: Maven Plugins - Extending Maven Functionality

**What are they?**
Plugins are extensions that perform specific tasks during build lifecycle. Maven is essentially a plugin execution framework.

**Core Plugins for Test Automation:**

#### 1. Maven Compiler Plugin (Compiles Java code)
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.11.0</version>
    <configuration>
        <source>11</source>              <!-- Java version for source code -->
        <target>11</target>              <!-- Java version for compiled bytecode -->
        <encoding>UTF-8</encoding>       <!-- Character encoding -->
    </configuration>
</plugin>
```

**Why needed:** Default compiler uses Java 5. Modern projects use Java 8+.

#### 2. Maven Surefire Plugin (Test Runner)
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.0.0</version>
    <configuration>
        <!-- Run TestNG suite -->
        <suiteXmlFiles>
            <suiteXmlFile>testng.xml</suiteXmlFile>
        </suiteXmlFiles>

        <!-- Run specific test classes -->
        <!-- <includes>
            <include>**/*Test.java</include>
        </includes> -->

        <!-- Parallel execution -->
        <parallel>methods</parallel>
        <threadCount>5</threadCount>

        <!-- System properties -->
        <systemPropertyVariables>
            <environment>QA</environment>
        </systemPropertyVariables>
    </configuration>
</plugin>
```

**Common Surefire Configurations:**

**Run specific test:**
```bash
mvn test -Dtest=UserAPITest
mvn test -Dtest=UserAPITest#testGetUser  # Specific method
```

**Run tests matching pattern:**
```bash
mvn test -Dtest=*APITest
```

**Skip tests:**
```bash
mvn package -DskipTests         # Skip test execution
mvn package -Dmaven.test.skip=true  # Skip test compilation and execution
```

#### 3. Maven JAR Plugin (Creates JAR file)
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <version>3.3.0</version>
    <configuration>
        <archive>
            <manifest>
                <mainClass>com.company.Main</mainClass>
            </manifest>
        </archive>
    </configuration>
</plugin>
```

#### 4. Maven Assembly Plugin (Creates executable JAR with dependencies)
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.6.0</version>
    <configuration>
        <archive>
            <manifest>
                <mainClass>com.company.Main</mainClass>
            </manifest>
        </archive>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
    </configuration>
    <executions>
        <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

**Run:**
```bash
mvn clean package
# Creates: target/project-name-1.0-jar-with-dependencies.jar
java -jar target/project-name-1.0-jar-with-dependencies.jar
```

---

### Concept 7: Maven Profiles - Multi-Environment Configuration

**What are they?**
Profiles allow you to customize builds for different environments (dev, QA, staging, prod) or different execution scenarios.

**Why does it matter for automation?**
- Different environments have different URLs, credentials
- Different test suites for different environments
- CI/CD pipelines use profiles to control test execution

**Real Example - Multi-Environment Setup:**

```xml
<profiles>
    <!-- Development Environment -->
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefault>true</activeByDefault>  <!-- Default profile -->
        </activation>
        <properties>
            <base.url>http://localhost:8080</base.url>
            <db.url>jdbc:mysql://localhost:3306/dev_db</db.url>
            <test.suite>testng-smoke.xml</test.suite>
        </properties>
    </profile>

    <!-- QA Environment -->
    <profile>
        <id>qa</id>
        <properties>
            <base.url>https://qa.company.com</base.url>
            <db.url>jdbc:mysql://qa-db.company.com:3306/qa_db</db.url>
            <test.suite>testng-regression.xml</test.suite>
        </properties>
    </profile>

    <!-- Staging Environment -->
    <profile>
        <id>staging</id>
        <properties>
            <base.url>https://staging.company.com</base.url>
            <db.url>jdbc:mysql://staging-db.company.com:3306/staging_db</db.url>
            <test.suite>testng-full.xml</test.suite>
        </properties>
    </profile>

    <!-- Production Environment -->
    <profile>
        <id>prod</id>
        <properties>
            <base.url>https://www.company.com</base.url>
            <db.url>jdbc:mysql://prod-db.company.com:3306/prod_db</db.url>
            <test.suite>testng-smoke.xml</test.suite>
        </properties>
    </profile>
</profiles>

<!-- Surefire plugin uses profile property -->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.0.0</version>
            <configuration>
                <suiteXmlFiles>
                    <suiteXmlFile>${test.suite}</suiteXmlFile>
                </suiteXmlFiles>
                <systemPropertyVariables>
                    <baseUrl>${base.url}</baseUrl>
                </systemPropertyVariables>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Using Profiles from Command Line:**
```bash
# Run with dev profile (default)
mvn test

# Run with QA profile
mvn test -Pqa

# Run with staging profile
mvn test -Pstaging

# Run with prod profile
mvn test -Pprod

# Multiple profiles
mvn test -Pqa,smoke
```

**Accessing Profile Properties in Java:**
```java
public class ConfigReader {
    public static String getBaseUrl() {
        // Reads system property set by Maven profile
        return System.getProperty("baseUrl", "http://localhost:8080");
    }
}
```

**Advanced Profile Example - Different Dependencies:**
```xml
<profiles>
    <profile>
        <id>windows</id>
        <activation>
            <os>
                <family>Windows</family>
            </os>
        </activation>
        <dependencies>
            <dependency>
                <groupId>org.seleniumhq.selenium</groupId>
                <artifactId>selenium-ie-driver</artifactId>
                <version>4.10.0</version>
            </dependency>
        </dependencies>
    </profile>

    <profile>
        <id>mac</id>
        <activation>
            <os>
                <family>Mac</family>
            </os>
        </activation>
        <dependencies>
            <dependency>
                <groupId>org.seleniumhq.selenium</groupId>
                <artifactId>selenium-safari-driver</artifactId>
                <version>4.10.0</version>
            </dependency>
        </dependencies>
    </profile>
</profiles>
```

---

### Concept 8: Dependency Management - Handling Conflicts

**What are Transitive Dependencies?**
When you add a dependency, it brings its own dependencies (transitive dependencies).

**Example:**
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
</dependency>
```

This actually downloads 20+ JARs:
- rest-assured-5.3.0.jar
- groovy-4.0.6.jar
- http-client-4.5.13.jar
- jackson-databind-2.14.1.jar
- ... and many more

**Viewing Dependency Tree:**
```bash
mvn dependency:tree
```

**Output:**
```
[INFO] com.company:api-automation:jar:1.0
[INFO] +- io.rest-assured:rest-assured:jar:5.3.0:test
[INFO] |  +- org.apache.groovy:groovy:jar:4.0.6:test
[INFO] |  +- org.apache.groovy:groovy-xml:jar:4.0.6:test
[INFO] |  +- org.apache.httpcomponents:httpclient:jar:4.5.13:test
[INFO] |  |  +- org.apache.httpcomponents:httpcore:jar:4.4.13:test
[INFO] |  |  \- commons-codec:commons-codec:jar:1.11:test
[INFO] |  \- com.fasterxml.jackson.core:jackson-databind:jar:2.14.1:test
```

**Dependency Conflicts:**
```
Project depends on:
- RestAssured 5.3.0 → uses Jackson 2.14.1
- Your code → uses Jackson 2.15.0

Which version does Maven use? 🤔
```

**Maven Dependency Resolution Rules:**
1. **Nearest Definition Wins** - Shortest path to dependency
2. **First Declaration Wins** - If same depth, first in pom.xml wins

**Resolving Conflicts with Exclusions:**
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
    <exclusions>
        <!-- Exclude specific transitive dependency -->
        <exclusion>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!-- Then explicitly add the version you want -->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.0</version>
</dependency>
```

**Using Dependency Management Section:**
```xml
<dependencyManagement>
    <dependencies>
        <!-- Define versions here -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.15.0</version>
        </dependency>
    </dependencies>
</dependencyManagement>

<!-- Then use without version -->
<dependencies>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <!-- Version inherited from dependencyManagement -->
    </dependency>
</dependencies>
```

---

## 🐍 Python vs Maven Comparison

### Dependency Management in Python (What You Know)
```python
# requirements.txt
requests==2.28.0
pytest==7.3.1
pytest-html==3.2.0

# Install
pip install -r requirements.txt

# Virtual environment
python -m venv venv
source venv/bin/activate  # Isolate dependencies

# Alternative: poetry
[tool.poetry.dependencies]
python = "^3.9"
requests = "^2.28.0"
pytest = "^7.3.1"
```

### Dependency Management in Java (What You're Learning)
```xml
<!-- pom.xml -->
<dependencies>
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.3.0</version>
    </dependency>
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.7.1</version>
    </dependency>
</dependencies>

<!-- Install (automatic) -->
mvn clean install
```

**Key Differences:**
| Aspect | Python | Java (Maven) |
|--------|--------|--------------|
| **Package Manager** | pip / poetry | Maven / Gradle |
| **Dependency File** | requirements.txt / pyproject.toml | pom.xml |
| **Package Repository** | PyPI | Maven Central |
| **Install Command** | `pip install -r requirements.txt` | `mvn install` (automatic) |
| **Virtual Environment** | venv / virtualenv (manual) | Maven local repo (~/.m2) (automatic) |
| **Versioning** | `requests==2.28.0` (exact) | `<version>2.28.0</version>` |
| **Transitive Deps** | Managed by pip | Managed by Maven automatically |
| **Build Tool** | setuptools / poetry | Maven / Gradle |
| **Run Tests** | `pytest` | `mvn test` |

**Mental Model Shift:**
- **Python:** Lightweight, script-focused, install dependencies manually
- **Java/Maven:** Enterprise-grade, project-focused, everything automated

**Python pytest.ini vs Maven pom.xml:**
```python
# pytest.ini (test configuration)
[pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
markers =
    smoke: Quick smoke tests
    regression: Full regression tests
```

```xml
<!-- pom.xml (build + test configuration) -->
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <configuration>
                <includes>
                    <include>**/*Test.java</include>
                </includes>
                <groups>smoke,regression</groups>
            </configuration>
        </plugin>
    </plugins>
</build>
```

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Not Running `mvn clean` Before Important Builds

❌ **Wrong:**
```bash
# Make changes to code
mvn test  # Uses old compiled classes from target/
# Tests pass but shouldn't because of stale classes
```

✅ **Correct:**
```bash
# Make changes to code
mvn clean test  # Deletes target/, recompiles everything
# Accurate test results
```

**Why it matters:** Stale compiled classes can cause false test results. Always clean before critical runs (CI/CD, demo, release).

---

### Mistake 2: Wrong Dependency Scope

❌ **Wrong:**
```xml
<!-- RestAssured in compile scope -->
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
    <!-- Missing scope=test -->
</dependency>
```
**Problem:** RestAssured gets packaged in final JAR, increases size unnecessarily.

✅ **Correct:**
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
    <scope>test</scope>  <!-- Only for testing -->
</dependency>
```

**Why it matters:** Bloated JAR files, dependency conflicts, security issues.

---

### Mistake 3: Hardcoding Versions Multiple Times

❌ **Wrong:**
```xml
<dependency>
    <groupId>org.testng</groupId>
    <artifactId>testng</artifactId>
    <version>7.7.1</version>
</dependency>

<dependency>
    <groupId>org.testng</groupId>
    <artifactId>testng-junit5</artifactId>
    <version>7.7.1</version>  <!-- Duplicated version -->
</dependency>
```

✅ **Correct:**
```xml
<properties>
    <testng.version>7.7.1</testng.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>${testng.version}</version>
    </dependency>

    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng-junit5</artifactId>
        <version>${testng.version}</version>
    </dependency>
</dependencies>
```

**Why it matters:** Version consistency, easy upgrades, maintainability.

---

### Mistake 4: Not Excluding Transitive Dependencies Causing Conflicts

❌ **Wrong:**
```xml
<!-- Both dependencies include different versions of Jackson -->
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.0</version>
</dependency>

<!-- Runtime: NoSuchMethodError due to version conflict -->
```

✅ **Correct:**
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
    <exclusions>
        <exclusion>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.15.0</version>
</dependency>
```

**Why it matters:** Prevents ClassNotFoundException, NoSuchMethodError at runtime.

---

### Mistake 5: Committing target/ Directory to Git

❌ **Wrong:**
```bash
git add .
git commit -m "Added tests"
# Commits target/ with compiled classes, reports
```

✅ **Correct:**
```bash
# .gitignore
target/
.idea/
*.iml
.classpath
.project
.settings/

git add .
git commit -m "Added tests"
# target/ is ignored
```

**Why it matters:** Bloated repository, merge conflicts, build artifacts shouldn't be versioned.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use Maven Wrapper for Team Consistency**
```bash
# Instead of: mvn clean test (requires Maven installed)
# Use Maven Wrapper: ./mvnw clean test (uses specific Maven version)

# Generate wrapper
mvn -N io.takari:maven:wrapper
# Commits mvnw and mvnw.cmd to Git
# Team members don't need to install Maven
```

**Tip 2: Run Tests in Parallel for Speed**
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.0.0</version>
    <configuration>
        <parallel>methods</parallel>
        <threadCount>5</threadCount>
        <suiteXmlFiles>
            <suiteXmlFile>testng.xml</suiteXmlFile>
        </suiteXmlFiles>
    </configuration>
</plugin>
```

**Tip 3: Skip Specific Test Classes**
```bash
# Skip specific test
mvn test -Dtest=!SlowTest

# Run all except integration tests
mvn test -Dtest=!*IntegrationTest
```

**Tip 4: Debug Maven Issues with -X Flag**
```bash
mvn clean test -X  # Debug mode (verbose output)
mvn clean test -e  # Error stack traces
```

**Tip 5: Check for Dependency Updates**
```bash
mvn versions:display-dependency-updates
# Shows available updates for dependencies
```

---

## 🔍 Deep Dive: Maven Archetypes

**What experienced developers know:**
Maven Archetypes are project templates that generate complete project structure with sample code.

**Creating Project from Archetype:**
```bash
# Create Maven project interactively
mvn archetype:generate

# Create from specific archetype
mvn archetype:generate \
  -DgroupId=com.company \
  -DartifactId=api-tests \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DinteractiveMode=false
```

**Popular Archetypes:**
- `maven-archetype-quickstart` - Basic Java project
- `maven-archetype-webapp` - Web application
- Custom company archetypes with pre-configured frameworks

**When to use this:**
- Starting new projects
- Standardizing team project structure
- Quickly spinning up POC projects

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **POM** | Project Object Model - Maven's configuration file | `pom.xml` |
| **GAV** | GroupId-ArtifactId-Version coordinates | `com.company:api-tests:1.0` |
| **Artifact** | File produced by Maven build | `api-tests-1.0.jar` |
| **Repository** | Storage location for artifacts | Maven Central, Local repo |
| **Dependency** | External library your project uses | RestAssured, TestNG |
| **Transitive Dependency** | Dependencies of your dependencies | Jackson (from RestAssured) |
| **Scope** | When dependency is available | test, compile, provided |
| **Plugin** | Extension that adds functionality | Surefire, Compiler |
| **Goal** | Task that a plugin executes | `surefire:test`, `compiler:compile` |
| **Phase** | Stage in build lifecycle | compile, test, package |
| **Profile** | Build configuration variant | dev, qa, prod |
| **SNAPSHOT** | Under-development version | `1.0-SNAPSHOT` |

---

## 🎓 Interview Prep

**Common Interview Questions on Maven:**

**Q1: What is Maven and why do we use it?**
**A:** Maven is a build automation and dependency management tool for Java projects. We use it to:
- Manage project dependencies automatically from Maven Central
- Build and package projects using standard lifecycle (compile, test, package)
- Execute tests using plugins like Surefire
- Maintain consistent project structure across teams
- Integrate with CI/CD pipelines for automated builds

```java
// Example: Instead of manually managing 50+ JAR files
// We just add dependencies in pom.xml and Maven handles everything
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
    <scope>test</scope>
</dependency>
```

---

**Q2: Explain Maven project structure.**
**A:** Maven follows "convention over configuration" with this structure:
- `src/main/java` - Application source code (Page Objects, utilities)
- `src/main/resources` - Configuration files (log4j.xml, properties)
- `src/test/java` - Test classes (API tests, UI tests)
- `src/test/resources` - Test data (JSON payloads, test data files)
- `target/` - Build output (compiled classes, JARs, reports) - Not committed to Git
- `pom.xml` - Project configuration (dependencies, plugins, properties)

This standardization means every Java project has the same structure, making it easy for teams to collaborate.

---

**Q3: What are the different dependency scopes in Maven?**
**A:** Maven has 5 dependency scopes:

1. **compile** (default) - Available everywhere (main code, test code, runtime)
2. **test** - Only available in test code (TestNG, RestAssured)
3. **provided** - Available at compile time but provided by container (Servlet API)
4. **runtime** - Not needed for compilation but needed at runtime (JDBC drivers)
5. **system** - Like provided but you specify JAR path (avoid using)

```xml
<!-- Example -->
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
    <scope>test</scope>  <!-- Only available in src/test/java -->
</dependency>
```

---

**Q4: Explain Maven build lifecycle.**
**A:** Maven has three built-in lifecycles: clean, default, and site. The default lifecycle has these main phases (in order):

1. **validate** - Validate project structure
2. **compile** - Compile source code to bytecode
3. **test** - Run unit tests using Surefire plugin
4. **package** - Package compiled code into JAR/WAR
5. **verify** - Run integration tests
6. **install** - Install JAR to local repository (~/.m2)
7. **deploy** - Deploy JAR to remote repository

When you run `mvn test`, it executes validate → compile → test in order.
When you run `mvn package`, it executes all phases up to package including running tests.

---

**Q5: How do you run tests from command line using Maven?**
**A:**
```bash
# Run all tests
mvn test

# Clean and run tests
mvn clean test

# Run specific test class
mvn test -Dtest=UserAPITest

# Run specific test method
mvn test -Dtest=UserAPITest#testCreateUser

# Run multiple test classes
mvn test -Dtest=UserAPITest,ProductAPITest

# Run tests matching pattern
mvn test -Dtest=*APITest

# Run with specific TestNG suite
mvn test -DsuiteXmlFile=testng-smoke.xml

# Run with Maven profile
mvn test -Pqa

# Skip tests
mvn package -DskipTests
```

---

**Q6: What is the difference between `mvn clean install` and `mvn clean package`?**
**A:**
- **`mvn clean package`** - Deletes target/, compiles code, runs tests, creates JAR in target/ directory
- **`mvn clean install`** - Does everything package does, PLUS installs JAR to local Maven repository (~/.m2/repository) so other local projects can use it

Use `package` for regular builds. Use `install` when you want to share this project with other local projects as a dependency.

---

**Q7: How do you handle dependency conflicts in Maven?**
**A:** Dependency conflicts occur when different libraries include different versions of the same dependency. Solutions:

1. **Use Exclusions:**
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
    <exclusions>
        <exclusion>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

2. **Check Dependency Tree:**
```bash
mvn dependency:tree
```

3. **Use dependencyManagement to enforce versions:**
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.15.0</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

---

**Q8: What is Maven Surefire plugin and how do you configure it?**
**A:** Surefire is the plugin that runs tests during `mvn test`. It supports TestNG and JUnit.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.0.0</version>
    <configuration>
        <!-- Run TestNG suite -->
        <suiteXmlFiles>
            <suiteXmlFile>testng.xml</suiteXmlFile>
        </suiteXmlFiles>

        <!-- Parallel execution -->
        <parallel>methods</parallel>
        <threadCount>5</threadCount>

        <!-- Include/Exclude tests -->
        <includes>
            <include>**/*Test.java</include>
        </includes>

        <!-- Pass system properties to tests -->
        <systemPropertyVariables>
            <environment>QA</environment>
        </systemPropertyVariables>
    </configuration>
</plugin>
```

---

**Q9: What are Maven profiles and how do you use them?**
**A:** Profiles allow you to customize builds for different environments or scenarios. Example:

```xml
<profiles>
    <profile>
        <id>qa</id>
        <properties>
            <base.url>https://qa.company.com</base.url>
            <test.suite>testng-regression.xml</test.suite>
        </properties>
    </profile>

    <profile>
        <id>prod</id>
        <properties>
            <base.url>https://www.company.com</base.url>
            <test.suite>testng-smoke.xml</test.suite>
        </properties>
    </profile>
</profiles>
```

**Usage:**
```bash
mvn test -Pqa    # Run tests against QA environment
mvn test -Pprod  # Run tests against Production environment
```

This is crucial for CI/CD pipelines where the same test code runs against multiple environments.

---

**Q10: How do you integrate Maven with CI/CD tools like Jenkins?**
**A:** Jenkins can execute Maven commands as build steps:

1. **Install Maven on Jenkins server**
2. **Configure Maven in Jenkins Global Tool Configuration**
3. **In Jenkins job, add build step:**
```bash
mvn clean test -Pqa
```

4. **Archive test reports:**
   - Surefire reports: `target/surefire-reports/*.xml`
   - HTML reports: `target/test-reports/index.html`

5. **Trigger builds:**
   - On Git commit (webhook)
   - Scheduled (cron: nightly regression)
   - Manual trigger

**Example Jenkinsfile:**
```groovy
pipeline {
    agent any
    tools {
        maven 'Maven 3.8.6'
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/company/api-tests.git'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'mvn clean test -Pqa'
            }
        }
        stage('Publish Reports') {
            steps {
                junit 'target/surefire-reports/*.xml'
            }
        }
    }
}
```

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. What are the three components of Maven coordinates (GAV)?
2. What is the difference between `compile` and `test` dependency scope?
3. Name the Maven phases that execute when you run `mvn package`.
4. How do you run only smoke tests using Maven?
5. What command would you use to see all transitive dependencies?

**Answers at the end of Practice file.**

---

**Next:** Day-4-Practice-Exercises.md
