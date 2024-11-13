# cd_lab_qbr

Prerequisite
All attendees need to complete the Continuous Delivery Developer certification.

Attendees need to have familiarity with the following:

Harness Platform concepts

Authentication - LDAP and SSO

Harness Delegate

Harness RBAC

Terraform

APIs

Basics of CI/CD

Harness Secret Manager

What to bring for the workshop
Git Repo with Kubernetes Manifests

Credentials to the Git Repo to configure connector for lab use

Docs: Harness Developer Hub  

Provided in the Workshop
Harness Account with a Project

Harness Delegate 

Access to 2 namespaces in K8s Cluster (cd_attendees_xx_dev, cd_attendees_xx_prod

Curriculum
Git Experience

Kubernetes Deployments 

Service

Artifacts

Environment

Templatize Manifest 

Overrides

Deployment Strategy

Values.yaml Overrides

Multi-Service Deployments

Multi-Environment Deployments

Pipeline and Controls

Templates: Stage Template

Run time inputs

Barriers

Stage/Step variables

Conditional Executions

Looping Strategy

Matrix Strategy

Repeat Strategy

Failure Strategies

Delegate Selector

Pipeline Chaining

Triggers 

Notification

Common Steps

Container Step 

Rollback

Application Failure and Rollback Customization

Troubleshooting a Failed Deployment

Delegate Logs

Pipeline Execution Logs

Onboarding using Terraform/APIs

Continuous Verification

Deployments Lab 
Business Case:

Imagine you just bought Harness and you are now responsible for onboarding your team onto Harness. You will need to be able to set your pipeline, perform deployments, and scale your implementation to other core software delivery use cases. 

Project and CD Setup (30 min) 
Step 1: Create your own project. Name the project “Lab_<your_full_name>“ (Terraform automated)

Step 2: Create your Kubernetes Connector within the project, a master URL will be provided with a service account.

Step 3: Create your GitHub connector to access your Kubernetes Manifests / Helm Charts

Step 4: Navigate to Service and configure:

a Kubernetes Service with Manifests being fetched from GitHub

Sample YAML



apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-webapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sample-webapp
  template:
    metadata:
      labels:
        app: sample-webapp
    spec:
      containers:
      - name: webapp
        image: gcr.io/google-samples/hello-app:1.0
        ports:
        - containerPort: 8080
        resources:
          requests:
            cpu: 200m
          limits:
            cpu: 500m
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: sample-webapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: sample-webapp
  minReplicas: 2
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
a Helm Service with Manifests being fetched from a Helm Repo like bitnami 

Helm Charts to deploy cert-manager in Kubernetes 

Step 7: Configure your environments  and infra def

Please configure an environment for Prod and an environment for Dev

In the Prod Environment configure a Kubernetes Infra Definition that maps to the Kubernetes Cluster, provide the namespace as cd_attendee_xx_prod 

For the Dev Environment configure a kubernetes infra definition that maps to the Kubernetes Cluster, provide the namespace as cd_attendee_xx_dev 

Note: Create the namespace using:

@Rohan Gupta 

Step 8: Configure a Service Variable for your Kubernetes Service for replica

Step 9: Configure a Service specific Environment Override for replica to be 1 for Dev Environment and 2 for Prod Environment 

Step 10: Navigate to your Kubernetes Manifest and add the Service Variable to it. 

Note: You will need to Go Template the Manifest

Note: You will need to configure a values.yaml from scratch

 

ETA:  30 minutes.

Kubernetes Deployment Lab 
Lab 1: Sequencing out Kubernetes Manifest Execution and Conditional Executing Manifests

Instructions :  

Using your Kubernetes Service, configure a CD Kubernetes Deploy Stage that performs a rolling deployment. Deployment has to be in sequence. Order of stages should be, dev → Harness Approval → prod. Utilise a stage template to make sure dev and prod deployment follow the same steps

Make sure the HPA object needs to be deployed only to the production environment, not the dev environment.

Name the Pipeline “Lab1_<yourname>”

Run the Pipeline and review the results

ETA: 10 minutes.

Lab 2: Kubernetes Deployments for Multi-Environment Deployment with conditional execution

Instructions:   

In your Kubernetes Service, configure 2 values.yaml: dev-values.yaml and prod-values.yaml

Configure these values files as overrides for the respective environment.

In these values files, ensure that replica is set to the service override variable you configured

Configure a pipeline that performs a multi-environment deployment (Use the same service to deploy to different environments)

Configure a shell script step to print the artifact tag only when the environment name is production

Configure a container step that prints the service variable: replica 

The HPA should still only apply when the environment is in production

Name the Pipeline “Lab2_<yourname>”

Run Pipeline and review the results

Download the Pipeline logs

Screenshot the Rolling Deployment Step Delegate Log

 

ETA: 15 minutes.

Terraform Provider Lab
Lab 1: Automate Onboarding to Harness

Summary: Use a pipeline to create a k8s service, environment, custom webhook trigger. Recreate the same service and environment and prefix with “tf_“ .  

Harness Terraform Provider Documentation: https://registry.terraform.io/providers/harness/harness/latest/docs  

Instructions:

Leveraging the Harness Terraform Provider, create a Pipeline that calls the Harness Terraform Provider via the Terraform Plan, Apply Steps

You can use a Custom Stage or CD Stage to spin this up

GitX  and Template Lab
Lab 1: Using remote pipelines and templates

Instructions:

Create a remote pipeline.

Create a remote template with a shell script with some defined message and link it in the created pipeline.

Execute the pipeline to see the expected message.

Now change the shell script message on the GIT for the given template. Check if template on UI shows the new changes or not.

Execute the pipeline again and it should show the new message in execution.

Lab 2: Onboard pipelines and template onto bidirectional sync

Instructions:

Register webhook for desired repo and path. [Note: For Github token used for webhook creation, we need “repo”, “user” and “admin:hook” perms in it]

Now ensure that the already created pipelines and templates show up as synced entities.

Execute the pipeline.

Change the shell script message again as done previously.

Check what message is shown on the template on UI now.

Execute the pipeline again.

Lab 3: Simultaneously working with synced and non-synced remote entities

Instructions:

Create a new pipeline which doesn’t come under bidirectional sync.

Make changes on GIT for the newly created pipeline.

Check if the changes are reflected on Pipeline UI.

Execute the pipeline to see newly created changes.

Lab 4: Templatise the Deploy Stage 

Take your CD Kubernetes Pipeline and turn the deploy stage into a template

Back your template in GitHub in your GitHub Repo and save to the main branch

Make a change to that template by adding a stage variable and saving it to a different branch - develop

Add a shell script step that prints that stage variable - save it to the develop branch

Configure the Kubernetes Pipeline to deploy the develop branch-backed deploy stage 

Pipeline Controls Lab
Lab 1: Pipeline and Looping Strategy  - Node Status

Instruction:

 Create a pipeline “Lab1_looping_(yourname)“ with custom stage

 Create a stage with a looping strategy, a step group with a looping strategy, and a step with a looping strategy

Write a Shell script in the pipeline that prints the following:

Find the current status of the stage, step group, and step executing through the node

Execute the pipeline to print the results

Lab 2: Pipeline and Looping Strategy  - Looping Status

Given we have a pipeline with a looping strategy at the stage, step group, and step

Instruction

Write a Shell script in the pipeline that prints the following:

 Find the current status of the ongoing looping strategy

Execute the pipeline to print the results

Lab 3: Pipeline and Looping Strategy  - Iteration Count Status

Given we have a pipeline with a looping strategy at the stage,  step group, and step

Instruction

Create a pipeline with a looping strategy at the stage,  step group, and step

Write a Shell script in the pipeline that prints the following:

 Find the current iteration and the total iterations of the ongoing looping strategy

Execute the pipeline to print the results

Lab 4: Pipeline and Repeat Strategy  - Set Range for Repeat 

Given an entity has a repeat looping strategy, of 5 items, ( 1, 2, 3, 4, 5 )

Instruction

Configure the pipeline that has a step that repeats over 5 items 

Please configure the repeat to start from 1 and skip the element 3.

Execute the pipeline to print the results

Lab 5: Pipeline and Matrix Strategy  - Spawn stages in batches 

Create a Pipeline that has a matrix or repeat looping strategy configured on a stage

Instruction

Configure the pipeline that has a Stage that Matrix Strategy configured with 5 Items

Please configure the stage to spawn 2 stages in parallel.

Execute the pipeline to print the results 

Lab 6: Pipeline and Matrix Strategy  - Matrix with Sequential Execution

Instruction

Create a pipeline “Lab6_looping_<yourname>” that has a matrix / repeat looping strategy configured on a stage

Configure the pipeline that has a Stage that Matrix Strategy configured with 5 Items

Please configure the stage to spawn 1 stage sequentially

Execute the pipeline to print the results

Lab 7: Pipeline and Matrix Strategy - Conditional Execution of Matrix

Instruction

Create a pipeline “Lab6_matrix_<yourname>” that has a matrix looping strategy configured on a simple custom stage.

Configure a runtime input of type number. 

Set up the Matrix so that, if the number entered is less than 10, the Matrix stages are generated and executed. If it is greater than/equal to 10, the Matrix should be skipped without generating the dynamically created stages at all. 

Execute the pipeline once with 3, once with 12 as an input. 

Lab 8: InputSet Evaluation

Take your Kubernetes pipeline and mark the service, environment as runtime input

Configure an input variable in your Shell Script Step and make that a runtime input

Print that variable in your shell script step

Create your input set based on these 3 parameters

Lab 9: GitHub Trigger your Pipeline

Configure a Github Webhook Trigger for your kubernetes pipeline and have it trigger on a PR Action

Pass in the input set you configured in Lab 8

Lab 10: Custom Webhook Trigger your Pipeline

Configure your Custom Webhook Trigger for your Kubernetes pipeline. 

Pass the service identifier, environment identifier and input variable parameter via the custom webhook trigger

Run the curl command to trigger the pipeline from your laptop

Find the payload passed to Harness via the webhook

Lab 11: Pipeline Chaining

Execute the Kubernetes Pipeline and Custom Stage Pipeline in the same Pipeline

Map one of the outputs from the Kubernetes Pipeline to a field of input in the Custom Stage Pipeline

Execute the Pipeline and view your results

Lab 12: Barriers with 2 Pipelines

Create a Pipeline that deploys a Kubernetes service and holds the pipeline execution with a barrier

Configure a shell script step that prints the message “1st Place!” after the barrier

Create a 2nd pipeline that runs the kubectl apply of the HPA and holds with a barrier that runs a shell script saying

“2nd Place!” 

Rollback and Failure Strategy Lab
Configure A Kubernetes Deployment into your namespace

Deploy that service once into your Kubernetes cluster

Configure an HTTP that makes a GET Request to https://harness-cd-canary.ai

Configure a failure strategy on the step that retries 3 times with a 2s interval

After that, the final failure should be a stage rollback

Run the Pipeline

What is the current version of your service? 

Now navigate to the rollback section remove the rollback step and add the delete Kubernetes step that deletes the release name

Now run the pipeline

Can you figure out how to roll back without executing the pipeline?

Estimate : 1 hr

Troubleshooting Lab
Configure A Kubernetes Deployment into your namespace

Deploy that service once into your Kubernetes cluster

Configure an HTTP that makes a GET Request to https://harness-cd-canary.ai

Configure a failure strategy on the step that retries 3 times with a 2s interval

After that, the final failure should be a stage rollback

Run the Pipeline

Check the Delegate Log and provide the results here for the failed step

Download the Step Log for the failed step share the error in the log

Download the Pipeline Log  share the error in the log 

Estimate: 30 mins

Phase 2: Governance Lab
Lab 1: Configure a Policy Step that enforces the namespace that needs to be deployed is your prod namespace

Write an OPA Policy denying the pipeline execution if the namespace isn’t  cd_attendeed_xx_prod 

Configure an OPA Step as the first step in the pipeline that checks for the namespace input after the pipeline is run

Deploy the Kubernetes Pipeline you configured in the Kubernetes Lab to Dev and your dev namespace, what happens? 

Deploy the Kubernetes Pipeline you configured in the Kubernetes Lab to Prod and your prod namespace, what happens? 

 Lab 2: Configure an OPA Policy that enforces the delegate selector configured for the Pipeline to be your delegate

Write an OPA Policy on Pipeline Run for your pipeline that enforces the delegate selector to be your Kubernetes delegates delegate selector

Configure a Pipeline with the Pipeline delegate selector as a runtime input

Run the Pipeline and pass a delegate selector as input

