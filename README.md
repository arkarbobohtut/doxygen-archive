# **Jenkins Doxygen Documentation Pipeline**

This project uses a Jenkins Pipeline to automatically generate Doxygen documentation from the grpc source code and archive the generated documentation as a Jenkins build artifact.

```
Pipeline Flow
Checkout Repository
        ↓
Generate Doxyfile
        ↓
Configure Doxyfile
        ↓
Run Doxygen
        ↓
Create doc.tar.gz
        ↓
Archive Artifact in Jenkins
Requirements
```

The Jenkins agent should have the following tools installed:

Git Doxygen tar

You can check them with:

```
git --version
doxygen --version
tar --version
Repository
```

The pipeline checks out the following public repository:

https://github.com/arkarbobohtut/grpc.git

It uses the master branch.

## **Pipeline Stages**

### **1. Checkout**

The pipeline downloads the source code from the GitHub repository.

stage('Checkout')

The Git checkout timeout is set to 60 minutes because the gRPC repository is relatively large.

timeout: 60

Tags are not downloaded because they are not required for generating the documentation.

### **2. Generate Doxyfile**

Doxygen creates a default configuration file using:

doxygen -g Doxyfile

This creates a file named:

Doxyfile

### **3. Configure Doxyfile**

The pipeline changes some Doxygen settings.

The source directory is configured as:

INPUT = src

The documentation output directory is:

OUTPUT_DIRECTORY = docs

Recursive source scanning is enabled:

RECURSIVE = YES

LaTeX documentation is disabled:

GENERATE_LATEX = NO

### **4. Run Doxygen**

Doxygen generates the documentation using the configured Doxyfile:

doxygen Doxyfile

The generated HTML documentation will be located under:

docs/html/

### **5. Create Archive**

The generated HTML documentation is compressed into:

doc.tar.gz

The command used is:

tar -czf doc.tar.gz -C docs html

### **6. Archive Artifact**

Finally, Jenkins archives doc.tar.gz as a build artifact:

archiveArtifacts artifacts: 'doc.tar.gz', fingerprint: true

After a successful build, the file can be downloaded from the Jenkins build page under Artifacts.

### **Expected Workspace**

After the pipeline finishes successfully, the workspace should look approximately like this:

```
workspace/
├── Doxyfile
├── doc.tar.gz
├── docs/
│   └── html/
│       ├── index.html
│       ├── ...
├── src/
└── ...
```

The main output of the pipeline is:

doc.tar.gz

### **How to Run**

```
Create a new Pipeline job in Jenkins.
Add the Jenkinsfile to the pipeline configuration.
Make sure the Jenkins agent has Git, Doxygen, and tar installed.
Click Build Now.
Wait for all pipeline stages to complete.
Open the completed build.
Download doc.tar.gz from the Artifacts section.
Result

The pipeline automatically:

Downloads the source code.
Creates a Doxygen configuration.
Generates HTML documentation.
Compresses the documentation.
Stores the compressed file as a Jenkins artifact.

This makes the Doxygen documentation available from Jenkins after each successful build.
```