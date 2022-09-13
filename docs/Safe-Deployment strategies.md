
#**Safe-Deployment strategies**#
OES allows you to safely and consistently deploy applications in Kubernetes with built-in, 
ready-to-use deployment strategies such as Blue-Green, Canary, A/B, and Rolling Update. 
OES treats Kubernetes as first-class citizens and supports Kubernetes resources and operators for custom strategies. 

1. **Canary and Blue-Green Deployment:** Avoid production downtime and mitigate risk of introducing new software 
by using in-built strategies like Canary and Blue-Green deployments. OES has native istio support, which makes 
traffic splitting between various kubernetes pods such as baseline and canary. 

2. **Custom Stages for Verification:** OES allows DevOps managers to create custom stages in the deployment pipeline 
for verifying performance and quality of new software release in the production.

3. **Rollback:** In case of any regression, OES can notify stakeholders like SRE who can automatically rollback to 
the previous version with a single click to avoid degradation of customer experience.

4. **Approvals Gates:** DevOps managers can use the manual judgement stage in the pipeline where release managers 
can go through aggregated information to approve or reject deployment pipeline progression.




 

