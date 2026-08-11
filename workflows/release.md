# Release Workflow

## Overview
Manage the complete release process from preparation to deployment.

## Stages

### 1. Release Preparation
- Create release branch
- Update version numbers
- Generate changelog
- Review dependencies

### 2. Release Testing
- Run full test suite
- Execute smoke tests
- Perform UAT
- Security scan
- Skill: `/testing`

### 3. Release Documentation
- Update release notes
- Document breaking changes
- Update deployment guide
- Prepare rollback plan

### 4. Deployment
- Deploy to staging
- Verify staging deployment
- Deploy to production
- Monitor metrics

### 5. Post-Release
- Tag release in git
- Announce release
- Monitor for issues
- Address hotfixes if needed

## Entry Criteria
- All features complete and merged
- QA sign-off received
- Stakeholder approval

## Exit Criteria
- Successfully deployed to production
- No critical issues
- Release documented
- Team notified
