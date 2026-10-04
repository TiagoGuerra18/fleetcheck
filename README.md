# FleetCheck – Build Systems Lab

This project is intentionally incomplete. Follow the worksheet in the order given.

Expected final application output:

```
FleetCheck 1.0
Vehicles loaded: 4
Vehicles requiring service: 2
Average mileage: 37000 km
```

Do not copy the solution POM. The objective is to observe how each build change alters the result.

## Step 1 - Build Failure Evidence
Execution command: `mvn clean package`

Output summary:
```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.16.0:compile (default-compile) on project fleetcheck: Compilation failure:
[ERROR] /src/main/java/pt/upt/fleetcheck/App.java:[3,39] package com.fasterxml.jackson.core.type does not exist
[ERROR] /src/main/java/pt/upt/fleetcheck/App.java:[4,38] package com.fasterxml.jackson.databind does not exist
[ERROR] /src/main/java/pt/upt/fleetcheck/App.java:[11,9] cannot find symbol class ObjectMapper
[ERROR] /src/main/java/pt/upt/fleetcheck/App.java:[20,25] cannot find symbol class TypeReference
```
## Step 2 - Behavioural Failure Evidence and Question
Execution command: `mvn clean package`

Output summary:
```
[INFO] --- surefire:3.5.5:test (default-test) @ fleetcheck ---
[INFO] Running pt.upt.fleetcheck.FleetServiceTest
[ERROR] Failures: 
[ERROR]   FleetServiceTest.testFleetServiceLogic:15 O teste falhou: deteção de defeito comportamental na frota. ==> expected: <2> but was: <0>
[INFO] Tests run: 1, Failures: 1, Errors: 0, Skipped: 0
[INFO] BUILD FAILURE
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:3.5.5:test (default-test) on project fleetcheck: There are test failures.
```

## Step 3 - Dependency Tree Analysis
Execution command: `mvn dependency:tree`

Output tree:
```
[INFO] pt.upt.softwarequality:fleetcheck:jar:1.0.0
[INFO] +- com.fasterxml.jackson.core:jackson-databind:jar:2.22.2:compile
[INFO] |  +- com.fasterxml.jackson.core:jackson-annotations:jar:2.22:compile
[INFO] |  \- com.fasterxml.jackson.core:jackson-core:jar:2.22.2:compile
[INFO] \- org.junit.jupiter:junit-jupiter:jar:5.14.4:test
[INFO]    +- org.junit.jupiter:junit-jupiter-api:jar:5.14.4:test
[INFO]    |  \- org.opentest4j:opentest4j:jar:1.3.0:test
[INFO]    +- org.junit.jupiter:junit-platform-commons:jar:1.14.4:test
[INFO]    |  \- org.apiguardian:apiguardian-api:jar:1.1.2:test
[INFO]    +- org.junit.jupiter:junit-jupiter-params:jar:5.14.4:test
[INFO]    \- org.junit.jupiter:junit-jupiter-engine:jar:5.14.4:test
[INFO]       \- org.junit.platform:junit-platform-engine:jar:1.14.4:test
```
**Direct Dependencies:**
- `com.fasterxml.jackson.core:jackson-databind`
- `org.junit.jupiter:junit-jupiter`

**Transitive Dependencies (via jackson-databind):**
- `com.fasterxml.jackson.core:jackson-annotations`
- `com.fasterxml.jackson.core:jackson-core`

## Step 4 - Deliverable Artifact (Shade Plugin)
Execution command: `java -jar target/fleetcheck-1.0.0-all.jar`

Application output:
```
FleetCheck 1.0
Vehicles loaded: 4
Vehicles requiring service: 2
Average mileage: 37000 km
```
## Step 5 - Maven Wrapper and Reproducible Builds
Command used to generate the wrapper: `mvn wrapper:wrapper`
Verification command: `.\mvnw.cmd clean verify -DskipTests`

**Question: Which hidden environmental assumption did the wrapper remove?**
The Maven Wrapper removes the assumption that the developer (or CI/CD runner) has a compatible Maven distribution 
pre-installed and properly configured in the system's `PATH`. It guarantees that any machine or pipeline uses
the exact same Maven version specified in `.mvn/wrapper/maven-wrapper.properties`, eliminating build inconsistencies
caused by version mismatches between environments.
