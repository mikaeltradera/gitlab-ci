
# Section 3: Running Scripts in Your .gitlab-ci.yml

In GitLab CI/CD, the `.gitlab-ci.yml` file orchestrates the pipeline's activities. One of its core functionalities is to trigger scripts that facilitate automated processes such as testing, building, deploying, among others. This section delves into various methods to run scripts within this file.

## 3.1 The "In-File" Script

Directly embed scripts within the `.gitlab-ci.yml` file for straightforward tasks.

### Advantages:
- **Simplicity:** Best for simple actions without separate files.
- **Visibility:** Direct visibility of script actions in the CI configuration.

### Disadvantages:
- **Clutter:** Longer scripts can make `.gitlab-ci.yml` hard to read.
- **Reusability:** In-line scripts are less reusable across jobs or projects.

### Example:
```yaml
build_job:
  script:
    - echo "Compiling the source code..."
    - gcc -o output_file source_file.c
    - echo "Compilation completed."
```

### Best Practices:
- Ideal for succinct scripts.
- Document each script step for clarity.

## 3.2 The "Other-File" Script

Write scripts in separate files for complex operations, then reference them in `.gitlab-ci.yml`.

### Advantages:
- **Organization:** Keeps CI configuration clean and manageable.
- **Reusability:** Facilitates script use across different scenarios.

### Disadvantages:
- **Separation:** Script logic is detached from the CI configuration.

### Example:
Create `deploy_script.sh` and reference it in `.gitlab-ci.yml`:
```yaml
deploy_job:
  script:
    - chmod +x ./deploy_script.sh
    - ./deploy_script.sh
```

### Best Practices:
- Store scripts in the repository or a script repository.
- Ensure script executability (`chmod +x`).
- Use descriptive script file names.

## 3.3 The "Other-File-AND-Function" Script

For advanced scenarios, source external scripts and call specific functions within your CI pipeline.

### Advantages:
- **Flexibility:** Execute only required script parts as needed.
- **Modularity:** Maintain organizational clarity by using functions.

### Disadvantages:
- **Complexity:** Demands a solid grasp of scripting and CI mechanics.

### Example:
Define functions in `script_library.sh` and invoke them in `.gitlab-ci.yml`:
```bash
#!/bin/bash
function deploy_to_production {
  echo "Deploying to production server..."
  # Deployment commands here
}
function rollback {
  echo "Rolling back deployment..."
  # Rollback commands here
}
```
```yaml
production_deploy_job:
  script:
    - source script_library.sh
    - deploy_to_production

rollback_job:
  script:
    - source script_library.sh
    - rollback
```

### Best Practices:
- Utilize descriptive function names.
- Document each function's purpose in the script.
- Test scripts locally before CI/CD integration.
