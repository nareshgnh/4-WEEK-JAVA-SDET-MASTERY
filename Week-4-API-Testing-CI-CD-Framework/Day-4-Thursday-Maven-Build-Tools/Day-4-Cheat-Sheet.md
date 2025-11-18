# DAY 4 CHEAT SHEET: MAVEN & BUILD TOOLS

## ⚡ Quick Command Reference

### Essential Maven Commands

```bash
# Clean build (delete target/)
mvn clean

# Compile main code
mvn compile

# Compile tests
mvn test-compile

# Run all tests
mvn test

# Clean and run tests
mvn clean test

# Package into JAR/WAR
mvn package

# Install to local repository
mvn clean install

# Skip tests during build
mvn package -DskipTests

# Skip test compilation and execution
mvn package -Dmaven.test.skip=true

# Run specific test class
mvn test -Dtest=UserAPITest

# Run specific test method
mvn test -Dtest=UserAPITest#testGetUser

# Run multiple test classes
mvn test -Dtest=UserAPITest,ProductAPITest

# Run tests matching pattern
mvn test -Dtest=*APITest

# Run with specific profile
mvn test -Pqa

# Run multiple profiles
mvn test -Pqa,smoke

# Force update dependencies (re-download)
mvn clean install -U

# Offline mode (no internet required)
mvn test -o

# Debug mode (verbose output)
mvn clean test -X

# Show errors with stack traces
mvn test -e

# Generate project from archetype
mvn archetype:generate

# View dependency tree
mvn dependency:tree

# View dependency tree for specific dependency
mvn dependency:tree -Dincludes=io.rest-assured

# Analyze dependencies
mvn dependency:analyze

# Check for dependency updates
mvn versions:display-dependency-updates

# Check for plugin updates
mvn versions:display-plugin-updates

# Show effective POM (after inheritance)
mvn help:effective-pom

# Show active profiles
mvn help:active-profiles

# Describe a plugin
mvn help:describe -Dplugin=surefire
```

---

## 📁 Maven Project Structure

```
project-name/
├── src/
│   ├── main/
│   │   ├── java/              # Application source code
│   │   │   └── com/company/
│   │   │       ├── pages/     # Page Objects (Selenium)
│   │   │       ├── models/    # POJOs (API)
│   │   │       └── utils/     # Utility classes
│   │   └── resources/         # Configuration files
│   │       ├── config.properties
│   │       └── log4j.xml
│   │
│   └── test/
│       ├── java/              # Test source code
│       │   └── com/company/
│       │       ├── tests/     # Test classes
│       │       └── base/      # Base test classes
│       └── resources/         # Test resources
│           ├── testdata/      # Test data files
│           └── testng.xml     # TestNG suite
│
├── target/                    # Build output (GITIGNORE)
│   ├── classes/               # Compiled main code
│   ├── test-classes/          # Compiled test code
│   ├── surefire-reports/      # Test reports
│   └── project-1.0.jar        # Packaged JAR
│
├── pom.xml                    # Maven configuration
└── .gitignore
```

---

## 📄 Complete pom.xml Template

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <!-- GAV Coordinates -->
    <groupId>com.company</groupId>
    <artifactId>project-name</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>Project Name</name>
    <description>Project Description</description>

    <!-- Properties (Version Management) -->
    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

        <!-- Dependency Versions -->
        <restassured.version>5.3.0</restassured.version>
        <testng.version>7.7.1</testng.version>
        <selenium.version>4.10.0</selenium.version>

        <!-- Environment Properties -->
        <base.url>http://localhost:8080</base.url>
        <environment>DEV</environment>
    </properties>

    <!-- Dependencies -->
    <dependencies>
        <!-- RestAssured -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>${restassured.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- TestNG -->
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>${testng.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- Selenium -->
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>${selenium.version}</version>
        </dependency>
    </dependencies>

    <!-- Build Configuration -->
    <build>
        <plugins>
            <!-- Compiler Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>11</source>
                    <target>11</target>
                </configuration>
            </plugin>

            <!-- Surefire Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0</version>
                <configuration>
                    <suiteXmlFiles>
                        <suiteXmlFile>testng.xml</suiteXmlFile>
                    </suiteXmlFiles>
                    <systemPropertyVariables>
                        <baseUrl>${base.url}</baseUrl>
                    </systemPropertyVariables>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <!-- Profiles -->
    <profiles>
        <profile>
            <id>dev</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <base.url>http://localhost:8080</base.url>
                <environment>DEV</environment>
            </properties>
        </profile>

        <profile>
            <id>qa</id>
            <properties>
                <base.url>https://qa.company.com</base.url>
                <environment>QA</environment>
            </properties>
        </profile>
    </profiles>

</project>
```

---

## 🔑 Key Concepts at a Glance

### Maven Coordinates (GAV)
```xml
<groupId>com.company</groupId>        <!-- Organization (reverse domain) -->
<artifactId>project-name</artifactId>  <!-- Project name -->
<version>1.0-SNAPSHOT</version>        <!-- Version (SNAPSHOT = dev) -->
```

### Dependency Scopes
| Scope | Compile | Test Compile | Runtime | Test Runtime | Packaged |
|-------|---------|--------------|---------|--------------|----------|
| **compile** | ✅ | ✅ | ✅ | ✅ | ✅ |
| **test** | ❌ | ✅ | ❌ | ✅ | ❌ |
| **provided** | ✅ | ✅ | ❌ | ❌ | ❌ |
| **runtime** | ❌ | ❌ | ✅ | ✅ | ✅ |

**Usage:**
```xml
<!-- Test frameworks - scope=test -->
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.0</version>
    <scope>test</scope>
</dependency>

<!-- Compile-time only - scope=provided -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.28</version>
    <scope>provided</scope>
</dependency>

<!-- Runtime only - scope=runtime -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.33</version>
    <scope>runtime</scope>
</dependency>
```

### Build Lifecycle Phases
```
validate → compile → test → package → verify → install → deploy
```

**What each phase does:**
```bash
mvn compile    # validate + compile
mvn test       # validate + compile + test-compile + test
mvn package    # All above + package (create JAR)
mvn install    # All above + install to ~/.m2
mvn deploy     # All above + deploy to remote repo
```

---

## 🔧 Common Plugins Configuration

### Maven Compiler Plugin
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.11.0</version>
    <configuration>
        <source>11</source>
        <target>11</target>
        <encoding>UTF-8</encoding>
    </configuration>
</plugin>
```

### Maven Surefire Plugin (TestNG)
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.0.0</version>
    <configuration>
        <!-- TestNG suite -->
        <suiteXmlFiles>
            <suiteXmlFile>testng.xml</suiteXmlFile>
        </suiteXmlFiles>

        <!-- Parallel execution -->
        <parallel>methods</parallel>
        <threadCount>5</threadCount>

        <!-- Include/Exclude -->
        <includes>
            <include>**/*Test.java</include>
        </includes>

        <!-- System properties -->
        <systemPropertyVariables>
            <environment>QA</environment>
        </systemPropertyVariables>
    </configuration>
</plugin>
```

### Maven JAR Plugin
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

---

## 🎯 Profiles Quick Reference

### Define Profiles
```xml
<profiles>
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <base.url>http://localhost:8080</base.url>
            <test.suite>testng-smoke.xml</test.suite>
        </properties>
    </profile>

    <profile>
        <id>qa</id>
        <properties>
            <base.url>https://qa.company.com</base.url>
            <test.suite>testng-regression.xml</test.suite>
        </properties>
    </profile>
</profiles>
```

### Use Profiles
```bash
# Run with specific profile
mvn test -Pqa

# Multiple profiles
mvn test -Pqa,smoke

# Check active profiles
mvn help:active-profiles -Pqa
```

---

## 📊 Dependency Management

### View Dependencies
```bash
# Full dependency tree
mvn dependency:tree

# Specific dependency
mvn dependency:tree -Dincludes=io.rest-assured

# Verbose (show conflicts)
mvn dependency:tree -Dverbose

# Export to file
mvn dependency:tree > dependencies.txt
```

### Exclude Transitive Dependencies
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

### Dependency Management Section
```xml
<!-- Parent POM or top-level -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>7.7.1</version>
        </dependency>
    </dependencies>
</dependencyManagement>

<!-- Child POMs or later -->
<dependencies>
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <!-- Version inherited from dependencyManagement -->
    </dependency>
</dependencies>
```

---

## ❌ Common Mistakes Quick Fix

| Mistake | Symptom | Fix |
|---------|---------|-----|
| No Java version | "Source option 5 not supported" | Add `maven.compiler.source=11` |
| Wrong scope | Package doesn't exist | Change to `<scope>test</scope>` |
| No Surefire config | "No tests to run" | Add Surefire plugin with TestNG suite |
| Stale build | Old code executes | Run `mvn clean test` |
| Dependency conflict | NoSuchMethodError | Use `mvn dependency:tree` and exclude |
| Missing default property | Null values in tests | Add default in `<properties>` |
| Wrong resource path | FileNotFoundException | Use `getResourceAsStream()` |

---

## 🐍 Python vs Maven Quick Comparison

| Task | Python | Maven |
|------|--------|-------|
| **Dependency file** | requirements.txt | pom.xml |
| **Install dependencies** | `pip install -r requirements.txt` | `mvn install` (auto) |
| **Virtual env** | `python -m venv venv` | Maven local repo (~/.m2) |
| **Run tests** | `pytest` | `mvn test` |
| **Package** | `python setup.py sdist` | `mvn package` |
| **Build tool** | setuptools, poetry | Maven, Gradle |
| **Repository** | PyPI | Maven Central |

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ Maven project structure and conventions
- ✅ pom.xml anatomy (GAV, dependencies, plugins, profiles)
- ✅ Dependency management (scopes, transitive dependencies)
- ✅ Build lifecycle (compile, test, package, install)
- ✅ Maven plugins (Compiler, Surefire, JAR)
- ✅ Multi-environment setup using profiles
- ✅ Command-line test execution
- ✅ Debugging Maven issues

**Critical for Automation:**
- 🎯 Maven is the standard build tool for Java projects
- 🎯 pom.xml is the brain of the project
- 🎯 Profiles enable multi-environment testing
- 🎯 Surefire plugin controls test execution
- 🎯 Command-line execution required for CI/CD

---

## 🎤 Interview Phrases

**When asked about Maven, say:**

*"Maven is a build automation and dependency management tool for Java projects. In our API automation framework, we use Maven to manage all dependencies like RestAssured and TestNG, configure the Surefire plugin for test execution, and define profiles for different environments like dev, QA, and production. This allows us to run the same test suite against multiple environments using simple Maven commands like `mvn test -Pqa`. We also integrate Maven with our CI/CD pipeline in Jenkins, where builds are triggered on every commit and tests run automatically using `mvn clean test`."*

**When asked about pom.xml, say:**

*"The pom.xml is Maven's Project Object Model file that contains all project configuration. It includes the GAV coordinates that uniquely identify the project, dependencies with their scopes, build plugins like maven-compiler-plugin and maven-surefire-plugin, and profiles for environment-specific configurations. For example, in our QA profile, we set the base URL to our QA environment and configure it to run regression tests, while the prod profile uses production URL and only runs smoke tests."*

**When asked about dependency management, say:**

*"We use Maven's dependency management to centralize library versions and handle transitive dependencies automatically. I define dependency versions in properties for consistency, use appropriate scopes like 'test' for RestAssured and 'provided' for Lombok, and resolve conflicts using exclusions and the dependency:tree command. This ensures all team members use the same library versions and prevents runtime errors from version mismatches."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **Maven automates everything:** Dependencies download automatically, tests run via simple commands, profiles handle environments, and the build lifecycle ensures consistent builds. Learn the pom.xml structure and Maven commands - they're the foundation of Java automation frameworks.

---

## 🔮 Tomorrow's Preview

**Day 5 Topic:** TestNG Deep Dive

**What to review tonight:**
- Maven Surefire plugin configuration
- TestNG suite files (testng.xml)
- How Maven runs TestNG tests

**What to think about:**
- How do you organize tests into suites?
- How do you run tests in parallel?
- How do you handle test dependencies?

**Connection:**
Today you learned Maven builds the project and runs tests. Tomorrow you'll master TestNG - the framework that organizes and executes those tests.

---

## 🎯 Day 4 Achievement Unlocked!

**Today's Achievement:**
- ✅ Learned Maven fundamentals
- ✅ Created professional pom.xml
- ✅ Built multi-environment framework
- ✅ Mastered command-line execution
- ✅ 3 hours of Maven practice

**Cumulative Progress - Week 4:**
- Days completed: 4/7 (57%)
- Topics mastered:
  - ✅ Day 1: API Testing Basics
  - ✅ Day 2: RestAssured Deep Dive
  - ✅ Day 3: Advanced API Testing
  - ✅ Day 4: Maven & Build Tools
- Framework components: POJO models, authentication, Maven setup ✅
- Interview readiness: Can explain Maven in depth ✅

**Skills Unlocked:**
- 🔧 Maven project setup
- 📦 Dependency management
- 🌍 Multi-environment configuration
- 🚀 CI/CD pipeline readiness
- 💼 Enterprise-level framework design

**Momentum Statement:**

*You've now mastered the build foundation that every Java SDET must know. Your framework can run tests against any environment with a simple command. This is exactly what product companies expect. You're not just learning - you're building production-ready skills that will land you that 20-25 LPA role. Tomorrow, you'll add advanced TestNG features to make your framework even more powerful.*

**Week 4 Progress:** 57% Complete → 3 more days to framework mastery! 🚀

---

## 📚 Quick Reference Cards

### Maven Commands Card
```bash
# Build
mvn clean compile      # Compile code
mvn clean test         # Run tests
mvn clean package      # Create JAR
mvn clean install      # Install locally

# Test Execution
mvn test -Pqa                    # With profile
mvn test -Dtest=UserAPITest      # Specific test
mvn test -Dgroups=smoke          # By group

# Dependency Management
mvn dependency:tree              # View tree
mvn dependency:analyze           # Analyze usage
mvn versions:display-dependency-updates  # Check updates

# Debugging
mvn clean test -X               # Debug mode
mvn clean test -e               # Show errors
mvn help:effective-pom          # Final POM
```

### pom.xml Sections Card
```xml
<!-- 1. Coordinates -->
<groupId>com.company</groupId>
<artifactId>project</artifactId>
<version>1.0</version>

<!-- 2. Properties -->
<properties>
    <maven.compiler.source>11</maven.compiler.source>
    <restassured.version>5.3.0</restassured.version>
</properties>

<!-- 3. Dependencies -->
<dependencies>
    <dependency>
        <groupId>...</groupId>
        <artifactId>...</artifactId>
        <version>...</version>
        <scope>test</scope>
    </dependency>
</dependencies>

<!-- 4. Build Plugins -->
<build>
    <plugins>
        <plugin>...</plugin>
    </plugins>
</build>

<!-- 5. Profiles -->
<profiles>
    <profile>
        <id>qa</id>
        <properties>...</properties>
    </profile>
</profiles>
```

### Troubleshooting Card
```bash
# Problem: Tests not running
→ Check Surefire plugin configuration
→ Verify TestNG suite file exists
→ Run: mvn test -X (debug mode)

# Problem: Compilation error
→ Check Java version in properties
→ Run: mvn clean compile -e

# Problem: Dependency conflict
→ Run: mvn dependency:tree -Dverbose
→ Add exclusions in pom.xml

# Problem: Stale build
→ Run: mvn clean test
→ Delete target/ manually if needed

# Problem: Wrong environment
→ Check active profile: mvn help:active-profiles
→ Run with correct profile: mvn test -Pqa
```

---

## 🎓 Certification-Ready Knowledge

**You can now answer these Maven interview questions:**

1. ✅ What is Maven and why use it?
2. ✅ Explain Maven project structure
3. ✅ What are Maven coordinates (GAV)?
4. ✅ Explain dependency scopes
5. ✅ What is Maven build lifecycle?
6. ✅ How do you run tests from command line?
7. ✅ How do you handle dependency conflicts?
8. ✅ What are Maven profiles?
9. ✅ How do you integrate Maven with CI/CD?
10. ✅ What is Maven Surefire plugin?

**Practical Demonstration:**
- Create Maven project from scratch ✅
- Configure multi-environment setup ✅
- Run tests with different profiles ✅
- Debug build failures ✅
- Explain pom.xml line by line ✅

---

**Maven Mastery Complete!** 🎉

Keep this cheat sheet handy - you'll reference it constantly in your SDET career.

**Tomorrow:** TestNG Deep Dive - Annotations, Groups, Listeners, and Advanced Test Management! 🚀
