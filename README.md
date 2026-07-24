# Deploying a Minecraft Server with Spacelift, OpenTofu & Ansible

**Hands-on workshop — Toronto AWS User Group Meetup (July 2026)**

A complete GitOps-style deployment pipeline: infrastructure provisioned with **OpenTofu**, configuration managed with **Ansible**, and the whole workflow orchestrated end-to-end by **Spacelift** — with a running Minecraft server on AWS as the proof it all worked.

---

## What This Project Demonstrates

- **Multi-tool IaC orchestration** — using Spacelift to coordinate an OpenTofu stack (infrastructure) and an Ansible stack (configuration) as a single, dependency-aware pipeline
- **Secure cloud integration** — connecting Spacelift to AWS using an IAM role with a **custom trust policy** (`sts:AssumeRole` scoped with a `sts:ExternalId` condition) instead of long-lived credentials
- **Stack dependencies** — configuring the Ansible stack to trigger automatically only after the OpenTofu stack successfully provisions the infrastructure it depends on
- **Environment/context management** — using Spacelift Contexts and environment variables to share configuration across stacks
- **Day 2 operations** — making changes to already-deployed infrastructure through the same pipeline, the way real teams operate after initial deployment
- **Responsible teardown** — cleaning up all AWS resources at the end of the exercise

## Architecture

```
GitHub (forked workshop repo)
        │
        ▼
   Spacelift  ──────────────┐
        │                   │
        ▼                   ▼
  OpenTofu Stack      Ansible Stack
  (provisions AWS     (configures the
  infrastructure:      Minecraft server
  EC2, networking)     on the instance)
        │                   ▲
        └── stack ──────────┘
          dependency
        (Ansible runs only after
         OpenTofu succeeds)
        │
        ▼
  ✅ Running Minecraft server on AWS
```

## Tools Used

| Tool | Role in the pipeline |
|---|---|
| **Spacelift** | Orchestration platform — runs, sequences, and manages both stacks |
| **OpenTofu** | Open-source IaC (Terraform fork) — provisions the AWS infrastructure |
| **Ansible** | Configuration management — installs and configures the Minecraft server |
| **AWS (EC2, IAM)** | Cloud platform — compute for the server, IAM role for secure access |
| **GitHub** | Source of truth — Spacelift tracks the repo and triggers runs from it |

## What I Did

1. **Created a Spacelift account** and integrated it with my GitHub as the source-code provider
2. **Forked the workshop repository** containing the OpenTofu and Ansible code
3. **Integrated Spacelift with AWS** by creating an IAM role with a custom trust policy — scoping `sts:AssumeRole` with a `StringLike` condition on `sts:ExternalId` tied to my Spacelift account, and attaching the workshop's access policy
4. **Created a Context** in Spacelift to hold shared configuration
5. **Created the OpenTofu stack** pointing at the infrastructure code
6. **Created the Ansible stack** for server configuration, and added the required environment variables
7. **Added a stack dependency** so the Ansible stack runs automatically after the OpenTofu stack succeeds
8. **Triggered the pipeline** — OpenTofu provisioned the infrastructure, the dependency fired, and Ansible configured the Minecraft server
9. **Verified the deployment** — confirmed both stacks succeeded and the Minecraft server was live
10. **Explored Day 2 operations** — pushing changes through the same pipeline against already-running infrastructure
11. **Cleaned up all AWS resources** at the end of the session

## Key Takeaways

- **Orchestration is the missing piece between "I can write Terraform" and "a team can operate infrastructure."** Running `tofu apply` locally works for one person; Spacelift-style orchestration is how the same code becomes a controlled, auditable, multi-tool pipeline.
- **Provisioning and configuration are different jobs.** OpenTofu builds the house; Ansible furnishes it. Stack dependencies are what let each tool do its job in the right order without manual coordination.
- **Trust policies beat static credentials.** The IAM `AssumeRole` + `ExternalId` pattern meant Spacelift never held long-lived AWS keys — a pattern that maps directly to how mature teams grant third-party platforms access.
- **OpenTofu felt immediately familiar** coming from Terraform — same HCL, same workflow — which made the open-source fork an easy tool to add to the kit.

## Screenshots

Screenshots from my run-through are in [`/screenshots`](./screenshots):

- IAM role creation with the custom trust policy
- Spacelift stack configuration (OpenTofu + Ansible)
- Stack dependency setup
- Successful pipeline runs
- The verified Minecraft server deployment

## Credits

Built during the **Spacelift workshop at the Toronto AWS User Group meetup**, using the workshop materials provided by Spacelift and the AWS User Group Toronto organizers. Workshop infrastructure ran in a sandboxed AWS Workshop Studio account.

---

*Part of my ongoing Infrastructure-as-Code portfolio — see also my [Terraform + AWS VPC labs](https://github.com/Inderpreet2311/aws-terraform-vpc-fundamentals) and [AWS serverless projects](https://github.com/Inderpreet2311/AWS-Projects).*
