
# Rough Mock

Lab – Proposal

High Level Agenda
1. Set the scene
   1. Planning & architecture starting point: ALZ architecture
   1. Implementation option starting point: ALZ  implementation options
   1. Present the trimmed down ‘lecture slides’ (should be used across the lab to cross waiting times)
1. Lab Guidance / Wiki
1. Tools/Prior to this lab (how to manage this? Should this be part of the session description?)
   1. GitHub handle
   1. VSC, extensions
   1. ...

# User guide – how to use CARML
1. Set Prerequisites
   1. Azure
      1. Connect GitHub handle to Azure organization
      1. Create service principal
      1. Create secret
      1. Assign Owner permissions on test subscription
   1. GitHub
      1. Navigate to repository
      1. Fork repository
      1. Deactivate ‘GitHub actions’ for repository to avoid accidental triggering (just in case)
      1. Set credentials up (using information from “Azure” section)
      1. Update
         1. Variable file(s)
            1. Especially deployment variables
            1. ACR vs. TemplateSpec parameters
         1. dependency parameter file(s)
         1. parameter files for all modules that must be unique (for example storage account)
      1. Activate ‘GitHub actions’ again
1. Execute pipelines
   1. Execute dependency pipeline
   1. Execute xzy module pipelines (ideally not excessive ones like SQL Managed Instance, but those we require for a solution deployment)
1. Consume modules
   1. Clone forked repository
   1. Setup ‘solution’ folder structure (for example Tenant  Management-Group  Subscription  Resource Group)
   1. Create solution main/master-template
      1. Add template references
      1. Add input parameters (ideally as objects)
   1. Execute Test-AzDeployment
   1. Execute New-AzDeployment
1. Contribution guide – how to contribute to the repo (should we mention that?)
   1. TBD
