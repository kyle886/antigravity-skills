---
name: gitlab-ci-patterns
description: Build GitLab CI/CD pipelines with multi-stage workflows, caching, and distributed runners for scalable automation. Use when implementing GitLab CI/CD, optimizing pipeline performance, or setting up automated testing and deployment.
---

# GitLab CI Patterns

Comprehensive GitLab CI/CD pipeline patterns for automated testing, building, and deployment.

## Do not use this skill when

- The task is unrelated to gitlab ci patterns
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

## Purpose

Create efficient GitLab CI pipelines with proper stage organization, caching, and deployment strategies.

## Use this skill when

- Automate GitLab-based CI/CD
- Implement multi-stage pipelines
- Configure GitLab Runners
- Deploy to Kubernetes from GitLab
- Implement GitOps workflows

## Basic Pipeline Structure

```yaml
stages:
  - build
  - test
  - deploy

variables:
  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: "/certs"

build:
  stage: build
  image: node:20
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 hour
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/

test:
  stage: test
  image: node:20
  script:
    - npm ci
    - npm run lint
    - npm test
  coverage: '/Lines\s*:\s*(\d+\.\d+)%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

deploy:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl apply -f k8s/
    - kubectl rollout status deployment/my-app
  only:
    - main
  environment:
    name: production
    url: https://app.example.com
```

## Docker Build and Push

```yaml
build-docker:
  stage: build
  image: docker:24
  services:
    - docker:24-dind
  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker build -t $CI_REGISTRY_IMAGE:latest .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
    - docker push $CI_REGISTRY_IMAGE:latest
  only:
    - main
    - tags
```

## Multi-Environment Deployment

```yaml
.deploy_template: &deploy_template
  image: bitnami/kubectl:latest
  before_script:
    - kubectl config set-cluster k8s --server="$KUBE_URL" --insecure-skip-tls-verify=true
    - kubectl config set-credentials admin --token="$KUBE_TOKEN"
    - kubectl config set-context default --cluster=k8s --user=admin
    - kubectl config use-context default

deploy:staging:
  <<: *deploy_template
  stage: deploy
  script:
    - kubectl apply -f k8s/ -n staging
    - kubectl rollout status deployment/my-app -n staging
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - develop

deploy:production:
  <<: *deploy_template
  stage: deploy
  script:
    - kubectl apply -f k8s/ -n production
    - kubectl rollout status deployment/my-app -n production
  environment:
    name: production
    url: https://app.example.com
  when: manual
  only:
    - main
```

## Terraform Pipeline

```yaml
stages:
  - validate
  - plan
  - apply

variables:
  TF_ROOT: ${CI_PROJECT_DIR}/terraform
  TF_VERSION: "1.6.0"

before_script:
  - cd ${TF_ROOT}
  - terraform --version

validate:
  stage: validate
  image: hashicorp/terraform:${TF_VERSION}
  script:
    - terraform init -backend=false
    - terraform validate
    - terraform fmt -check

plan:
  stage: plan
  image: hashicorp/terraform:${TF_VERSION}
  script:
    - terraform init
    - terraform plan -out=tfplan
  artifacts:
    paths:
      - ${TF_ROOT}/tfplan
    expire_in: 1 day

apply:
  stage: apply
  image: hashicorp/terraform:${TF_VERSION}
  script:
    - terraform init
    - terraform apply -auto-approve tfplan
  dependencies:
    - plan
  when: manual
  only:
    - main
```

## Security Scanning

```yaml
include:
  - template: Security/SAST.gitlab-ci.yml
  - template: Security/Dependency-Scanning.gitlab-ci.yml
  - template: Security/Container-Scanning.gitlab-ci.yml

trivy-scan:
  stage: test
  image: aquasec/trivy:latest
  script:
    - trivy image --exit-code 1 --severity HIGH,CRITICAL $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
  allow_failure: true
```

## Caching Strategies

```yaml
# Cache node_modules
build:
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
    policy: pull-push

# Global cache
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - .cache/
    - vendor/

# Separate cache per job
job1:
  cache:
    key: job1-cache
    paths:
      - build/

job2:
  cache:
    key: job2-cache
    paths:
      - dist/
```

## Dynamic Child Pipelines

```yaml
generate-pipeline:
  stage: build
  script:
    - python generate_pipeline.py > child-pipeline.yml
  artifacts:
    paths:
      - child-pipeline.yml

trigger-child:
  stage: deploy
  trigger:
    include:
      - artifact: child-pipeline.yml
        job: generate-pipeline
    strategy: depend
```

## Reference Files

- `assets/gitlab-ci.yml.template` - Complete pipeline template
- `references/pipeline-stages.md` - Stage organization patterns

## Best Practices

1. **Use specific image tags** (node:20, not node:latest)
2. **Cache dependencies** appropriately
3. **Use artifacts** for build outputs
4. **Implement manual gates** for production
5. **Use environments** for deployment tracking
6. **Enable merge request pipelines**
7. **Use pipeline schedules** for recurring jobs
8. **Implement security scanning**
9. **Use CI/CD variables** for secrets
10. **Monitor pipeline performance**

## Related Skills

- `github-actions-templates` - For GitHub Actions
- `deployment-pipeline-design` - For architecture
- `secrets-management` - For secrets handling

## Resources

- `resources/playbook.md` for detailed patterns, checklists, and code templates.

## Cross-References

- [bazel-build-optimization](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/bazel-build-optimization/SKILL.md) - Optimize Bazel builds for large-scale monorepos. Use when configuring Bazel, implementing remote exe...
- [deployment-pipeline-design](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/deployment-pipeline-design/SKILL.md) - Design multi-stage CI/CD pipelines with approval gates, security checks, and deployment orchestratio...
- [performance-testing-review-multi-agent-review](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/performance-testing-review-multi-agent-review/SKILL.md) - Use when working with performance testing review multi agent review
- [turborepo-caching](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/turborepo-caching/SKILL.md) - Configure Turborepo for efficient monorepo builds with local and remote caching. Use when setting up...
- [nx-workspace-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/nx-workspace-patterns/SKILL.md) - Configure and optimize Nx monorepo workspaces. Use when setting up Nx, configuring project boundarie...
- [vite-modern-patterns](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/vite-modern-patterns/SKILL.md) - Master Vite build tool configuration, plugins, optimization, and deployment patterns. Use when setti...
- [website-deployment-pipeline](file:///Users/kylehutchin/Developer/Github/antigravity-skills/skills/website-deployment-pipeline/SKILL.md) - Design and implement end-to-end website deployment pipelines including build, test, deploy, CDN purg...

