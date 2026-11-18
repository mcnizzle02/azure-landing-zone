# azure-landing-zone

Project 04 – AZ-900 Landing Zone & Web Monitor
2026-11-16 · Lee McNutt
Scenario
Magnolia Bay Legal Group needs to know within minutes when its public website goes down, without paying for a commercial monitoring tool. As the cloud admin, you build a serverless uptime monitor in Azure inside a governed, tagged, budget-capped environment, all scripted in PowerShell.
Business requirements
Check the firm’s public website on a schedule (every 5 minutes) and record the result
Email IT when the site fails a check
Keep monthly spend under the budget, with alerts before it is reached
Only approved regions and tagged resources are allowed (governance guardrails)
Staff get least-privilege access: view only, unless they administer the project
The whole build can be torn down and redeployed from code
Why this project: every requirement maps to an AZ-900 objective, and the build doubles as PowerShell practice for the cloud admin path (AZ-104, SC-300).
Project overview
One resource group, two policies, one PowerShell function, one alert: small enough to rebuild in an evening once you know it.
Environment
Item Value
Subscription Pay-As-You-Go (same subscription as the cloud resume)
Resource group rg-webmonitor-dev
Region (fill in)
Budget (fill in amount) per month, alerts at 50% / 80% / 100% actual + forecast
Function language PowerShell
Repo <https://github.com/mcnizzle02/azure-landing-zone>
Notes OneNote, one page per task
AZ-900 domain mapping
Exam domain Weight Covered by tasks
Cloud concepts 25–30% T05, T06, T09 (IaaS vs PaaS vs serverless, shared responsibility, consumption pricing)
Azure architecture and services 35–40% T04, T06, T09, T10 (Entra ID, RBAC, compute, networking, storage)
Azure management and governance 30–35% T01–T03, T07, T08, T11 (cost, tags, Policy, locks, monitoring, IaC tools)
Naming conventions (type-purpose-environment)
Resource Prefix Example
Resource group rg- rg-webmonitor-dev
Function App func- func-webmonitor-dev
Storage account st (no dashes, lowercase) stwebmonitordev
Application Insights appi- appi-webmonitor-dev
Log Analytics workspace log- log-webmonitor-dev
Virtual network vnet- vnet-webmonitor-dev
Network security group nsg- nsg-web-dev
Tags on every resource: env: dev, owner: lee, project: webmonitor
Screenshot scheme: one folder per task (P04-T03), files named P04-T03-01-policy-assignment.png, numbered in the order taken. Take the screenshot before moving to the next step, so notes never fall behind.
Tasks
Twelve tasks, taken one at a time; T01 and T02 are done and T03 (Azure Policy) is next.
Task Title Status Completed
T01 Budget and cost alerts Done 2026-11-16
T02 Resource group and tags Done 2026-11-16
T03 Azure Policy guardrails + PowerShell tests Done 2026-11-17  
T04 Identity and RBAC (least privilege) In Progress  
T05 Write the PowerShell function locally Not started  
T06 Deploy the Function App Not started  
T07 App settings and configuration Not started  
T08 Monitoring and alerting Not started  
T09 Networking basics (VNet, subnets, NSG) Not started  
T10 Storage tiers and redundancy Not started  
T11 Infrastructure as code: tear down and redeploy Not started  
T12 Practice assessment and review Not started  
Each task below uses the same template: Objective, Steps, Verify, Exam tie-in, Screenshots, Notes / issues. Fill in Notes as you go: what broke, the error text, and how you fixed it.
T01 – Budget and cost alerts
Objective: get warned before spend reaches the monthly limit.
Steps: Cost Management → Budgets → Add; set amount; alerts at 50/80/100% actual and forecasted.
Verify: budget listed with alert conditions and your email.
Exam tie-in: a budget only alerts, it never stops spending. Only credit-based subscriptions (Free Trial) have a hard spending limit.
Screenshots: budget settings, alert conditions.
Notes / issues:
T02 – Resource group and tags
Objective: one container for the whole project, tagged for cost tracking.
Steps: create rg-webmonitor-dev in your region with the three standard tags.
Verify: Get-AzResourceGroup -Name rg-webmonitor-dev shows the tags.
Exam tie-in: resource groups share a lifecycle; tags organize and filter costs. Hierarchy: management group → subscription → resource group → resource.
Screenshots: RG overview with tags.
Notes / issues:
T03 – Azure Policy guardrails + PowerShell tests
Objective: block resources outside the approved region or missing the project tag.
Steps: assign Allowed locations and Require a tag on resources scoped to rg-webmonitor-dev; wait for enforcement; run the three New-AzStorageAccount tests in Cloud Shell (no tag, wrong region, correct); remove the test account.
Verify: tests 1 and 2 denied with the policy named in the error; test 3 succeeds.
Exam tie-in: Policy enforces what can be deployed, RBAC controls who can act, locks prevent deletes. Policy inherits downward from its scope.
Screenshots: both assignments, each denial error, the successful deployment.
Notes / issues:
T04 – Identity and RBAC (least privilege)
Objective: a staff member can view the project but not change it.
Steps: create a test user and a group in Entra ID; assign the group Reader on rg-webmonitor-dev; sign in as the test user (private window) and try to delete a resource. Add a CanNotDelete lock to the RG.
Verify: test user can view but is denied on delete; lock blocks deletion even for you until removed.
Exam tie-in: authentication vs. authorization, RBAC scope inheritance, Zero Trust, resource locks.
Screenshots: role assignment, access-denied message, lock settings.
Notes / issues:
T05 – Write the PowerShell function locally
Objective: a timer-triggered PowerShell function that checks the site and logs the result.
Steps: install VS Code, Azure Functions extension, Functions Core Tools, PowerShell 7; create a PowerShell timer-trigger project; use Invoke-WebRequest to check the site; log status code and response time; run locally with func start.
Verify: local run logs a success, and a failure when pointed at a bad URL.
Exam tie-in: serverless compute, event-driven code, consumption-based pricing.
Screenshots: project structure, local run output (success and failure).
Notes / issues:
T06 – Deploy the Function App
Objective: run the function in Azure.
Steps: create func-webmonitor-dev (PowerShell runtime, serverless plan, tagged) with its storage account and Application Insights; publish from VS Code or func azure functionapp publish func-webmonitor-dev; push code to GitHub.
Verify: function runs on schedule; invocations show in the portal.
Exam tie-in: compare IaaS (a VM: you patch the OS), PaaS (App Service), and serverless (Functions: no servers to manage). Shared responsibility model.
Screenshots: Function App overview, invocation history, Policy compliance for new resources.
Notes / issues:
T07 – App settings and configuration
Objective: keep the target URL out of the code.
Steps: add an app setting (e.g. TARGET_URL); read it in PowerShell with $env:TARGET_URL; redeploy.
Verify: changing the setting changes the site checked, with no code change.
Exam tie-in: separating config from code; where secrets would go (Key Vault).
Screenshots: app settings page, log showing the new URL.
Notes / issues:
T08 – Monitoring and alerting
Objective: IT gets an email when a check fails.
Steps: query results in Application Insights / Log Analytics; create an Azure Monitor alert rule with an action group (email); test by pointing at a bad URL. Review Azure Advisor and Service Health.
Verify: alert email arrives; alert shows as fired then resolved.
Exam tie-in: Azure Monitor (your metrics and logs) vs. Advisor (recommendations) vs. Service Health (Azure-side outages).
Screenshots: alert rule, action group, alert email, Advisor recommendations.
Notes / issues:
T09 – Networking basics (VNet, subnets, NSG)
Objective: understand how Azure networks are segmented and filtered.
Steps: create vnet-webmonitor-dev with web and data subnets; attach nsg-web-dev allowing only 80/443 inbound; optionally peer a second VNet in another region (remember the location policy).
Verify: NSG rules applied to the subnet; peering shows Connected.
Exam tie-in: VNets, subnets, NSGs, peering, VPN Gateway vs. ExpressRoute, public vs. private endpoints.
Screenshots: VNet and subnets, NSG rules, peering status.
Notes / issues:
T10 – Storage tiers and redundancy
Objective: see access tiers and redundancy options firsthand.
Steps: upload a blob; move it Hot → Cool → Archive; try to read the archived blob; add a lifecycle rule; review LRS / ZRS / GRS / GZRS options.
Verify: archived blob requires rehydration before reading.
Exam tie-in: access tiers, redundancy options, lifecycle management, AzCopy and Storage Explorer.
Screenshots: tier change, rehydrate prompt, lifecycle rule.
Notes / issues:
T11 – Infrastructure as code: tear down and redeploy
Objective: prove the build is reproducible.
Steps: export the RG template and convert it to Bicep (or write a small Bicep file); delete the RG; redeploy with New-AzResourceGroupDeployment; republish the function.
Verify: monitor runs again with the same names and tags.
Exam tie-in: ARM templates and Bicep, Azure CLI vs. PowerShell vs. portal vs. Cloud Shell.
Screenshots: deployment history, working function after redeploy.
Notes / issues:
T12 – Practice assessment and review
Objective: confirm exam readiness.
Steps: take the free Microsoft Learn AZ-900 practice assessment; list every missed topic; link each one back to the task that covers it or study it separately.
Verify: consistently passing practice scores before booking or sitting the exam.
Exam tie-in: all three domains.
Screenshots: score report.
Notes / issues:
Wrap-up
Fill these in as you go; they become your final review sheet before the exam.
Lessons learned

1. The old web monitor was lost because the Free Trial subscription was disabled after 30 days. Lesson: know your subscription type and keep everything in code and a repo.
Exam review notes (one line per “aha” moment)
1. Budgets alert; they don’t stop spending.
PowerShell cmdlets learned
Cmdlet What it does Task
Get-AzContext Shows the current account and subscription T03
Get-AzResourceGroup Lists a resource group and its tags T02
New-AzStorageAccount Creates a storage account T03
Remove-AzStorageAccount Deletes a storage account T03
Cleanup checklist (when the project is finished)
1. ☐ Decide what stays running (the monitor) and what goes (VNets, test storage, test user)
1. ☐ Remove the RG lock before deleting anything
1. ☐ Delete test users and groups in Entra ID
1. ☐ Check Cost Management for leftover spend a few days later
1. ☐ Final commit to GitHub with README and screenshots


