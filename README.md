# Fleet GitOps

This is the starter repository for using [Fleet](https://fleetdm.com) with a GitOps workflow.

[Why use GitOps?](https://fleetdm.com/guides/sysadmin-diaries-gitops-a-strategic-advantage#basic-article)

## GitHub setup

1. Clone the [GitHub repository](https://github.com/fleetdm/fleet-gitops), create your own GitHub repository, and push your clone to your new repo. Note that a workflow will run once and fail because the required variables haven't been added (step 2 and 3).

2. Add `FLEET_URL` and `FLEET_API_TOKEN` secrets to your new repository's secrets. Learn how [here](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-a-repository). Set `FLEET_URL` to your Fleet instance's URL (ex. https://organization.fleet.com). [Create an API-only user](https://fleetdm.com/docs/using-fleet/fleetctl-cli#create-api-only-user) with the "GitOps" role and set `FLEET_API_TOKEN` to your user's API token. If you're using Fleet Free, set the API-only user's role to global admin.

4. If you are using secrets to manage SSO metadata for Fleet SSO login or MDM SSO login, uncomment lines 22 and 23 in `gitops.sh`.
   - If you are using different variable names for your secrets, edit the appropriate line to reflect the correct variable name. 

5. In GitHub, enable the `Apply latest configuration to Fleet` GitHub Actions workflow, and run workflow manually. Now, when anyone pushes a new commit to the default branch, the action will run and update Fleet. For pull requests, the workflow will do a dry run only.

## GitLab setup

1. Clone the [GitLab repository](https://gitlab.com/fleetdm/fleet-gitops), create your own GitLab repository, and push your clone to your new repo. Note that a pipeline will run once and fail because the required variables haven't been added (step 2 and 3).

2. Add `FLEET_URL` and `FLEET_API_TOKEN` as masked CI/CD variables. Learn how [here](https://docs.gitlab.com/ee/ci/variables/#define-a-cicd-variable-in-the-ui). Set `FLEET_URL` to your Fleet instance's URL (ex. https://organization.fleet.com). Set `FLEET_API_TOKEN` to an API token for an API-only user in Fleet. Learn how [here](https://fleetdm.com/docs/using-fleet/fleetctl-cli#create-api-only-user), then, grant it the `GitOps` role via the **Settings** > **Users** page so it can make changes.

3. Add `FLEET_GLOBAL_ENROLL_SECRET` secret as a masked CI/CD variable. The enroll secret must be an alphanumeric string of at least 32 and at most 255 characters.
   - If you have a Premium Fleet license, also add `FLEET_WORKSTATIONS_ENROLL_SECRET` and `FLEET_WORKSTATIONS_CANARY_ENROLL_SECRET`.
   - If you do not have a Premium Fleet license, delete the `teams` directory.
     
4. If you are using secrets to manage SSO metadata for Fleet SSO login or MDM SSO login, uncomment lines 22 and 23 in `gitops.sh`.
   - If you are using different variable names for your secrets, edit the appropriate line to reflect the correct variable name. 

5. Now, when anyone pushes a new commit to the default branch, the pipeline will run and update Fleet. For merge requests, the pipeline will do a dry run only.

6. (Optional) To ensure your Fleet configuration stays up to date even when there are no new commits, set up a scheduled pipeline:
   - In your GitLab project, go to the left sidebar and navigate to **Build > Pipeline schedules**. (In some GitLab versions, this may appear as **CI/CD > Schedules**.)
   - Click **Create a new pipeline schedule** (or **Schedule a new pipeline**).
   - Fill in the form:
      - **Description**: e.g., `Daily GitOps sync`
      - **Cron timezone**: e.g., `[UTC 0] UTC`
      - **Interval pattern**: e.g., Custom: `0 6 * * *` (runs nightly at 6AM UTC)
      - **Target branch or tag**: your default branch (e.g., `main`)
   - Click **Create pipeline schedule**.

## Configuration options

For all configuration options, go to the [YAML files reference](https://fleetdm.com/docs/using-fleet/gitops) in the Fleet docs.

## Fleet UI

Once you're set up with GitOps in Fleet, you can optionally put the UI in GitOps mode. This prevents you from making changes in the UI that would be overridden by GitOps workflows. 

An admin can enable GitOps mode in **Settings** > **Integrations** > **Change management**.

Note that this is a UI-only setting. API permissions are restricted based on user role.

