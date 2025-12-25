# Migrate Prod environment to Auto Scaling Group

## Goals

- Move wordpress source code from EBS to EFS to mount to multiple instances in the ASG.
- Migrate the existing production environment to use an Auto Scaling Group (ASG) successfully.
- Update the CodePipeline to deploy to the new ASG setup.
- Stress test to ensure the new setup can handle expected traffic loads and scale appropriately.
- Backup schedule for EFS and RDS
- Blue-green
- Switch and test within a maintenance window (in an evening)

## SMART Objectives

1. **Provision production ASG infrastructure using CDK by January 10, 2026** to enable high-availability WordPress hosting with auto-scaling capabilities matching staging environment architecture

2. **Complete WordPress source migration from EBS to EFS by January 15, 2026** using AWS DataSync with zero data loss and file permission preservation for multi-instance access

3. **Deploy production-ready CI/CD pipeline by January 17, 2026** to enable automated saigon-booking plugin deployments to EFS-mounted ASG instances

4. **Execute blue-green cutover to new ASG within 2-hour maintenance window by January 22, 2026** switching CloudFront origin from single EC2 to new ALB with rollback capability

5. **Validate production performance under 2x expected peak traffic load by January 24, 2026** through stress testing confirming <2s response time and successful auto-scaling from 1 to 3 instances

6. **Implement automated backup policies for EFS and RDS by January 26, 2026** with daily snapshots, 30-day retention, and verified restoration procedures

7. **Decommission legacy production EC2 instance by January 31, 2026** after 7-day monitoring period confirms stable ASG operation and successful backup verification

## Context

### Current production setup

Cloudfront -> ALB -> Single EC2 instance (running docker php-fpm container + apache2 container) with EBS (WordPress source code) + RDS (WordPress database)

## Solution

phase 1: provision & test - use CDK to provision resources in lib/stacks/staging (exclude ElastiCache)

- use datasync to clone wp source from ebs to efs
- test (any solution to avoid downtime)

phase 2: migration (perform within 1-2 hours)

- run datasync to sync data again to efs
- switch cloudfront to the new load balancer

phase 3: stop, then backup & cutoff unused resources
