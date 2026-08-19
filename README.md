# Jenkins Doxygen and Log Parser Pipeline

This Jenkins Pipeline generates Doxygen documentation from the gRPC source code and then uses a separate Python repository to parse the Doxygen warning log.

This pipeline is an extension of the previous Doxygen pipeline.

## Pipeline Flow

```
Checkout gRPC Repository
        |
        v
Generate Doxyfile
        |
        v
Configure Doxyfile
        |
        v
Run Doxygen
        |
        v
Checkout Python Parser Repository
        |
        v
Run Python Parser
```

## Repositories

### Repo A - gRPC

The pipeline checks out:

```
https://github.com/arkarbobohtut/grpc.git
```

Branch:

```
master
```

This repository is used as the source code for generating Doxygen documentation.

### Repo C - Python Log Parser

The pipeline checks out:

```
https://github.com/arkarbobohtut/doxygen-log-parse-with-python.git
```

Branch:

```
main
```

This repository contains the Python script used to parse the Doxygen warning log.

## Requirements

The Jenkins agent needs the following software:

- Git
- Doxygen
- Python 3

Check the installed versions:

```
git --version
doxygen --version
python3 --version
```

## Pipeline Stages

### 1. Checkout

The pipeline checks out the gRPC repository from GitHub.

```
stage('Checkout')
```

The repository uses the `master` branch.

The Git checkout timeout is set to 60 minutes because the gRPC repository is relatively large.

```
timeout: 60
```

Tags are not downloaded because they are not required for this pipeline.

---

### 2. Generate Doxyfile

Doxygen generates a default configuration file:

```
doxygen -g Doxyfile
```

This creates:

```
Doxyfile
```

in the Jenkins workspace.

---

### 3. Configure Doxyfile

The pipeline updates several Doxygen settings.

```
INPUT = src
```

The source code is taken from the `src` directory.

```
OUTPUT_DIRECTORY = docs
```

Doxygen documentation is generated under the `docs` directory.

```
RECURSIVE = YES
```

Doxygen searches subdirectories recursively.

```
GENERATE_LATEX = NO
```

LaTeX documentation is disabled because this pipeline only needs the HTML documentation.

```
WARN_LOGFILE = warnings.log
```

Doxygen writes its warning messages to:

```
warnings.log
```

The pipeline also prints the important Doxyfile settings to the Jenkins console for verification.

---

### 4. Run Doxygen

The pipeline runs:

```
doxygen Doxyfile
```

Doxygen generates the documentation and the warning log.

The workspace will contain something similar to:

```
docs/
└── html/
    ├── index.html
    └── ...

warnings.log
```

The `warnings.log` file is important because it will be used by the Python parser in the next part of the pipeline.

---

### 5. Checkout Repo C

The Python parser repository is checked out into a separate directory:

```
parser-repo/
```

The `dir('parser-repo')` block keeps the Python repository separate from the gRPC repository.

The workspace will look approximately like:

```
workspace/
├── Doxyfile
├── docs/
│   └── html/
├── warnings.log
├── src/
└── parser-repo/
    ├── parser.py
    └── ...
```

A shallow clone is used for the parser repository because the complete Git history is not required.

```
shallow: true
depth: 1
```

---

### 6. Run Parser

The pipeline changes into the parser repository:

```
dir('parser-repo')
```

and runs:

```
python3 parser.py ../warnings.log
```

The `../warnings.log` path is used because `warnings.log` is located one directory above the parser repository.

For example:

```
workspace/
│
├── warnings.log
│
└── parser-repo/
    └── parser.py
```

From inside `parser-repo`, the warning log is therefore:

```
../warnings.log
```

The Python parser processes the Doxygen warnings and generates its output.

---

## Jenkins Pipeline Configuration

During development, the pipeline was first tested using:

```
Pipeline script
```

This allows the Jenkinsfile to be tested directly in the Jenkins job configuration without first committing it to Git.

After the pipeline was working correctly, the Jenkinsfile was pushed to Git and tested using:

```
Pipeline script from SCM
```

This allows Jenkins to load the pipeline definition directly from the Git repository.

### Development Approach

The pipeline was developed in two steps:

```
1. Pipeline Script
       |
       v
   Test Jenkinsfile
       |
       v
   Fix issues
       |
       v
2. Push Jenkinsfile to Git
       |
       v
   Pipeline script from SCM
```

This approach makes it easier to test the pipeline first before moving the Jenkinsfile into source control.

## Changes from the Previous Pipeline

This pipeline extends the previous Doxygen pipeline with the following changes.

### Added Doxygen warning log configuration

The following setting was added:

```
WARN_LOGFILE = warnings.log
```

This allows the Doxygen warnings to be saved into a file.

### Added Python parser repository

The pipeline now checks out:

```
doxygen-log-parse-with-python
```

into:

```
parser-repo/
```

### Added Python parser execution

The pipeline runs:

```
python3 parser.py ../warnings.log warnings.csv
```

to process the Doxygen warning log.

### Removed archive stages

The following stages from the previous pipeline were removed:

```
Create Archive
Archive Artifact
```

The current pipeline focuses on generating the documentation, creating the warning log, and processing that log with the Python parser.

## Expected Result

After a successful build, the Jenkins workspace should contain:

```
workspace/
├── Doxyfile
├── docs/
│   └── html/
│       └── index.html
├── warnings.log
└── parser-repo/
    ├── parser.py
    └── ...
```

The main flow is:

```
gRPC Source Code
       |
       v
    Doxygen
       |
       +----> HTML Documentation
       |
       +----> warnings.log
                    |
                    v
             Python Parser
                    |
                    v
             Parsed Result
```

This pipeline provides a simple way to automatically generate documentation and process Doxygen warnings using a separate Python parser project.