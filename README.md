# Central Tekton Tasks Repository

This repository contains reusable Tekton tasks that can be referenced from individual application pipelines using the Git resolver.

## Available Tasks

### git-clone (v1.0.0)
Clone a git repository to a workspace.

**Location**: `tasks/git-clone/git-clone-v1.yaml`

**Key Parameters**:
- `url`: Repository URL to clone
- `revision`: Branch, tag, or commit SHA (default: "main")
- `depth`: Shallow clone depth (default: "1")

### run-tests-rust (v1.0.0)
Run Rust tests using cargo.

**Location**: `tasks/run-tests/run-tests-rust-v1.yaml`

**Key Parameters**:
- `test-command`: Cargo test command (default: "cargo test")
- `rust-version`: Rust version (default: "1.75")
- `additional-args`: Extra arguments for cargo test

### kaniko-build (v1.0.0)
Build container images using Kaniko (without Docker daemon).

**Location**: `tasks/docker-build/kaniko-build-v1.yaml`

**Key Parameters**:
- `image`: Full image reference (registry/repo:tag)
- `dockerfile`: Path to Dockerfile (default: "./Dockerfile")
- `context`: Build context (default: "./")

### push-to-ecr (v1.0.0)
Build and push container images to AWS ECR.

**Location**: `tasks/push-to-ecr/push-to-ecr-v1.yaml`

**Key Parameters**:
- `image`: Full ECR image reference
- `dockerfile`: Path to Dockerfile
- `aws-region`: AWS region (default: "us-east-1")

**Required Workspace**: `aws-credentials` (Secret with `aws_access_key_id` and `aws_secret_access_key`)

## Usage in Pipelines

Reference tasks from your pipeline using the Git resolver:

```yaml
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  name: my-pipeline
spec:
  tasks:
    - name: clone-repo
      taskRef:
        resolver: git
        params:
          - name: url
            value: https://github.com/YOUR_ORG/tekton-tasks
          - name: revision
            value: v1.0.0  # Pin to specific version
          - name: pathInRepo
            value: tasks/git-clone/git-clone-v1.yaml
      params:
        - name: url
          value: https://github.com/YOUR_ORG/my-app
        - name: revision
          value: main
      workspaces:
        - name: output
          workspace: shared-workspace
```

## Versioning

Tasks follow semantic versioning (v1.0.0, v1.1.0, v2.0.0).

- **v1.0.0**: Initial stable release
- Use Git tags to pin pipeline references to specific versions
- Breaking changes will increment major version

## Contributing

To add or update tasks:

1. Create task YAML in appropriate category directory
2. Follow naming convention: `<task-name>-v<version>.yaml`
3. Update this README with task documentation
4. Tag repository with new version: `git tag v1.1.0`
5. Push tags: `git push --tags`
# tekton-tasks
