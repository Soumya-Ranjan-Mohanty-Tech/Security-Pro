# Security-Pro
Microsoft Entra Agent ID provides specialized identity constructs designed specifically for AI agents operating in enterprise environments. These identity constructs enable secure authentication and authorization patterns that address the unique requirements of autonomous AI systems.


### Introduction
Unit 1/7
The emergence of AI agents as autonomous enterprise systems introduces unique security and operational challenges that existing identity models weren't designed to address. Organizations need a way to distinguish operations performed by AI agents from operations performed by human users or traditional applications, while maintaining appropriate security controls and governance.

Microsoft Entra Agent ID provides specialized identity constructs designed specifically for AI agents operating in enterprise environments. These identity constructs enable secure authentication and authorization patterns that address the unique requirements of autonomous AI systems.

Scenario: Managing AI Agent identities
Imagine you're an Identity and Access Administrator at a large organization that's adopting AI agents across multiple departments. Your marketing team uses AI agents in Microsoft Copilot Studio to answer customer inquiries. Your development team builds agents in Microsoft Foundry that access company data. Your IT team deploys agents in Azure App Service to automate workflows.

Each of these agents needs an identity to authenticate and access resources securely. However, using traditional service principals or managed identities doesn't provide the visibility, governance, or security controls designed for AI agents. You need to:

Distinguish AI agent operations from human user operations in audit logs
Apply consistent security policies to all agents of a particular type
Prevent agents from gaining excessive privileges
Scale identity management to potentially thousands of agents that might be created and destroyed rapidly
Microsoft Entra Agent ID addresses these challenges by providing specialized identity types designed for AI agents.

Availability
Note
Microsoft Entra Agent ID is part of Microsoft Agent 365.

Content description
In this module, you learn about Microsoft Entra Agent ID and how it differs from other identity types like service principals and managed identities. You explore which Microsoft products automatically create agent identities and how to view and manage them through the Microsoft Entra admin center. You also learn about the roles required to manage agent identities and how to query them programmatically using Microsoft Graph.

What is the main goal
By the end of this module, you'll understand what Microsoft Entra Agent ID is. You can explain how it compares to other identity types. You can define which Microsoft products use the Agent ID, and how to view and manage agent identities in your organization.


### Describe agent ID and compare identity types

Summarize

Turn into podcast
Unit 2/7
Microsoft Entra Agent ID is a specialized identity type in Microsoft Entra ID designed specifically for AI agents. It provides a standardized framework for governing, authenticating, and authorizing AI agents across Microsoft services. This framework enables agents to securely access resources, interact with users, and communicate with other systems.

An agent identity is a special service principal in Microsoft Entra ID that represents an identity created and managed by an agent identity blueprint. Agent identities authenticate by presenting access tokens issued to the service or platform on which the agent runs.

Key components: Agent identities and agent identity blueprints
The Agent ID platform introduces two key components:

Agent identity
An agent identity is the primary account used by an AI agent to authenticate to various systems. Key characteristics include:

Unique identifiers: Each agent identity has an object ID and app ID (which have the same value) for authentication and authorization decisions
No passwords: Agent identities don't have passwords or other direct credentials
Token-based authentication: They can only authenticate by presenting an access token issued to the service or platform on which the agent runs
Optional agent user: For scenarios requiring a user object, agents can have an agent user - a secondary account that is a user object in the tenant
Agent identity blueprint
An agent identity blueprint serves as the reusable, governing template from which all associated agent identities are created. It corresponds to a "kind," "type," or "class" of agents. Think of it as the management object for all agent identity instances of that class.

Agent identity blueprints serve four essential purposes:

Type classification: Establishes the category of agent (for example, "Sales Assistant Agent"), enabling administrators to apply policies, disable permissions, or audit agents at scale
Template for creation: Defines common characteristics shared across all agent identities, including description, app roles, verified publisher, and authentication settings
Credential holder: Holds OAuth credentials (client ID, secrets, certificates, or federated credentials) used to request tokens from Microsoft Entra ID
Container for management: Provides a logical container where administrators can apply policies, permissions, and controls that affect all agent identities created from the blueprint
Impersonation model
Agent identities use an impersonation model where the agent identity blueprint obtains tokens for the agent identity. How is the model different from traditional service principals access?

The blueprint has OAuth credentials and uses them to request access tokens from Microsoft Entra ID
The blueprint then presents that token to obtain a token for one of its agent identities
The resulting tokens maintain the agent identity in audit logs while the blueprint orchestrates token flows
Comparing agent identities to service principals
While agent identities are technically a special type of service principal, they have important differences:

Aspect
Service Principal
Agent Identity
Credential management	Manages own credentials (certificates, secrets, managed identities)	No direct credentials; relies on parent blueprint credentials
Token acquisition	Presents credentials to obtain tokens for itself	Blueprint uses impersonation to obtain tokens on its behalf
Relationship model	One-to-one with application	One-to-many (blueprint to multiple identities)
Permission model	Direct assignment or admin consent	Direct assignment + inheritance from parent blueprint
Runtime authentication	Performs direct authentication	Doesn't perform direct authentication



**Comparing agent identities to managed identities**

**Managed identities are another type of identity used for Azure resources:**

| Aspect                    | Managed Identity                                                            | Agent Identity                                           |
| ------------------------- | --------------------------------------------------------------------------- | -------------------------------------------------------- |
| **Purpose**               | Provides identity for Azure resources (Virtual Machines, App Service, etc.) | Provides identity specifically for AI agents             |
| **Lifecycle**             | Tied to an Azure resource (system-assigned) or standalone (user-assigned)   | Created dynamically for AI agents and can be short-lived |
| **Scale**                 | Designed for infrastructure resources                                       | Designed to scale to thousands of ephemeral agents       |
| **Credential Management** | Azure automatically manages credentials                                     | Blueprint manages credentials; agents have none          |
| **Multi-instance**        | One identity per resource or shared across resources                        | One blueprint creates many agent identities              |
| **Use Case**              | Azure service-to-service authentication                                     | AI agent authentication across Microsoft services        |


Security benefits of agent identities
Agent identities provide specific security benefits designed for AI agents:

Distinguish AI operations: Operations performed by AI agents are clearly separated from operations performed by workforce, customer, or traditional workload identities in audit logs and monitoring
Right-sized access: Enable AI agents to gain appropriate access across systems without excessive permissions
Prevent excessive privilege: Block agents from accessing critical security roles and systems
Scale identity management: Support large numbers of AI agents that can be quickly created and destroyed without leaving orphaned credentials
Authentication scenarios
Agent identities support two key authentication scenarios:

Attended (delegated access or on-behalf-of flow)
The agent operates on behalf of a human user, using delegated permissions that the user grants. The agent acts under the user's authority to access resources or APIs as that user. This scenario is common for interactive agents that respond to user prompts in a chat interface.

Unattended (autonomous operation)
The agent acts under its own authority as a service or application identity using its app-assigned roles, RBAC, or Microsoft Graph permissions. Alternatively, it can act as an agentic user - an autonomous identity with user-like claims that allows the agent to authenticate and operate independently.

High-privilege roles blocked for agents
To maintain security, Microsoft Entra Agent ID blocks certain high-privilege roles from being assigned to agent identities:

Global Administrator
Privileged Role Administrator
User Administrator
Only lower-privileged roles can be assigned to agent identities. Custom roles also can't be assigned to agent identities, and agent identities can't be members of role-assignable groups.

When to use agent identities versus other identity types
Use agent identities when:

Building AI agents in Microsoft Copilot Studio, Microsoft Foundry, or Microsoft 365
You need to distinguish AI agent operations from human or traditional application operations
You require centralized governance of AI agent identities
You need to scale identity management to many short-lived agents
Use service principals when:

Building traditional applications or services
You need a stable, long-lived application identity
The application isn't specifically an AI agent
Use managed identities when:

Providing identity to Azure resources (VMs, App Service, Functions, etc.)
You need Azure to automatically manage credentials
You don't need AI agent-specific governance features







### Identify products that use agent identities

Summarize

Turn into podcast
Unit 3/7
Several Microsoft products and services automatically create and manage agent identities throughout the agent lifecycle. Understanding which platforms support agent identities helps you plan your AI agent deployments and identity governance strategies.

Microsoft Foundry
Microsoft Foundry (formerly Azure AI Foundry) automatically creates and manages agent identities throughout the agent development lifecycle. This integration simplifies permission management while maintaining security and auditability as agents move from development to production.

Shared project identity for development
When you create your first agent in a Foundry project, the system automatically creates:

A default agent identity blueprint
A default agent identity for your project
All unpublished or in-development agents within the same project share this common identity. This design simplifies permission management because unpublished agents typically require the same access patterns and permission configurations.

Benefits of the shared identity approach:
Simplified administration: Administrators can centrally manage permissions for all in-development agents within a project
Reduced identity sprawl: Using a single identity per project prevents unnecessary identity creation during early experimentation
Developer autonomy: After the shared identity is configured, developers can independently build and test agents without repeatedly configuring new permissions
Distinct agent identity for published agents
When an agent's permissions, auditability, or lifecycle requirements diverge from the project defaults, you should upgrade to a distinct identity. Publishing an agent automatically creates:

A dedicated agent identity blueprint
A dedicated agent identity
Both are bound to the agent application resource. This distinct identity represents the agent's system authority for accessing its own resources.

Common scenarios that require distinct identities:

Agents ready for integration testing
Agents prepared for production consumption
Agents that require unique permission sets
Agents that need independent audit trails
Tool authentication in Microsoft Foundry
Agents in Microsoft Foundry access remote resources and tools by using agent identities for authentication. The authentication mechanism differs based on publication status:

Unpublished agents: Authenticate using the shared project's agent identity
Published agents: Authenticate using the unique agent identity associated with the agent application
Important
When you publish an agent, you must reassign RBAC permissions to the new agent identity for any resources that the agent needs to access. Permissions assigned to the shared identity don't automatically transfer.

Currently, the tools that support authentication with agent identity are:

Model Context Protocol (MCP): Use your agent's identity to authenticate with MCP servers that support agent identity authentication
Agent-to-Agent (A2A): Enable secure communication between agents using agent identities
Azure App Service and Azure Functions
You can configure an Azure App Service or Azure Functions app to use the Microsoft Entra agent identity platform to securely connect to resources as an agent.

To use agent identities with App Service or Functions:

Create an agent identity blueprint (typically using a user-assigned managed identity as the credential)
Configure your app to use the blueprint for creating agent identities
Create agent identities at runtime when your agent app needs to perform operations
Assign appropriate permissions to agent identities to access Azure resources
This approach is useful for:

Autonomous agents: Agents that work independently and perform actions using their own identity
Interactive agents: Agents that act as delegates of users, performing actions on behalf of that user
Microsoft Copilot Studio
Agents created in Microsoft Copilot Studio can be configured to automatically be assigned an agent identity. This feature is enabled at the environment level in the Power Platform admin center.

How it works
When you enable the "Microsoft Entra Agent Identity for Copilot Studio" setting:

Agents created in Copilot Studio are automatically assigned an agent identity
When the first agent identity is created in a Power Platform environment after enabling this setting, a Microsoft Copilot Studio agent identity blueprint is automatically created
An agent identity blueprint principal is also created, which has privileges to create agent identities and agent users in the tenant
Benefits
Automatic provisioning: No manual identity creation required
Centralized management: All Copilot Studio agent identities are visible in the Microsoft Entra admin center
Audit trail: Authentication activity is logged in Microsoft Entra ID and visible in the admin center
Lifecycle management: When you delete an agent from Copilot Studio, the associated agent identity is automatically removed from Microsoft Entra
Microsoft Teams platform
For agents in the Microsoft Teams platform, developers manually create and configure agent identity blueprints using the Developer Portal for Teams web interface.

How it works
Developers navigate to Tools > Agent Identity Blueprint in the Developer Portal
Create a new blueprint by providing a name and configuration
A unique Agent Blueprint ID is automatically generated for the blueprint
Configure the blueprint with agent type (Bot-based or API-based) and connection details
Agent identity creation
Unlike the blueprint itself, actual agent identities are created later through a separate workflow:

Publish the agent application
Request admin approval in Microsoft 365 Admin Center
Create agent instances in Teams that use the blueprint
This manual approach gives developers control over blueprint configuration while requiring administrative oversight for actual agent identity provisioning.

Microsoft Agent 365
Microsoft Agent 365 gives each AI agent its own Microsoft Entra Agent ID for identity, lifecycle, and access management.

Key features:

Automatic identity assignment: Each agent automatically receives its own agent identity
Integrated governance: Agent identities are integrated with Microsoft Entra governance features
Full lifecycle management: Identity creation, management, and deletion are handled throughout the agent lifecycle


### Summary of Automatic Provisioning

| Microsoft Product                 | Automatic Provisioning | How Agent Identities Are Used                                            |
| --------------------------------- | ---------------------- | ------------------------------------------------------------------------ |
| **Microsoft Foundry**             | Yes                    | Shared identity for unpublished agents; distinct identity when published |
| **Azure App Service / Functions** | Developer-managed      | Developers create blueprints and agent identities programmatically       |
| **Microsoft Copilot Studio**      | Yes (when enabled)     | Automatically assigned when the environment setting is enabled           |
| **Microsoft Teams**               | Developer-managed      | Developers create and manage blueprints in the Developer Portal          |
| **Microsoft Agent 365**           | Yes                    | Each agent automatically receives its own identity                       |

---

## Exam Memory Trick

### Automatic Provisioning Products

```text
Foundry
Copilot Studio
Agent 365
```

✅ Agent identities are automatically created.

---

### Developer-Managed Products

```text
Azure App Service / Functions
Microsoft Teams
```

✅ Developers create and manage the identities.

---

## Easy Table for Exams

| Product                       | Automatic?           |
| ----------------------------- | -------------------- |
| Microsoft Foundry             | ✅ Yes                |
| Copilot Studio                | ✅ Yes (when enabled) |
| Microsoft Agent 365           | ✅ Yes                |
| Azure App Service / Functions | ❌ Developer-managed  |
| Microsoft Teams               | ❌ Developer-managed  |

---

## Likely Exam Questions

### Which Microsoft products automatically provision agent identities?

✅ Microsoft Foundry
✅ Microsoft Copilot Studio (when enabled)
✅ Microsoft Agent 365

---

### Which products require developers to manage agent identities?

✅ Azure App Service / Functions
✅ Microsoft Teams

---

### In Microsoft Agent 365, what happens when a new agent is created?

✅ Each agent automatically receives its own identity.

---

### In Microsoft Copilot Studio, when are agent identities automatically assigned?

✅ When the environment setting is enabled.

---

### In Microsoft Foundry, how are identities handled?

✅ Shared identity for unpublished agents and distinct identities when published.

---

### Quick 10-Second Memory Aid

```text
Automatic:
Foundry
Copilot Studio
Agent 365

Developer Managed:
App Service
Teams
```

This distinction between **automatic provisioning** and **developer-managed provisioning** is one of the key takeaways from the Microsoft Entra Agent ID module.


**Planning considerations**
When planning your agent deployments, consider:

Which platforms will you use? Determine which Microsoft products host your agents
Identity governance requirements: Understand how agent identities are created, managed, and governed
Permission management: Plan how permissions are assigned, especially when publishing agents or moving them between environments
Audit and monitoring: Ensure you have processes to monitor agent identity activity in the Microsoft Entra admin center
Lifecycle management: Plan for how agent identities are created and deleted as agents are deployed and retired


### Navigate the admin center to view agents

Summarize

Turn into podcast
Unit 4/7
The Microsoft Entra admin center provides a centralized interface to view and manage your agent identities. This experience allows you to search, filter, sort, and take actions on agent identities across your organization.

Important
The Agent ID menu in the Microsoft Entra admin center is only visible if your tenant has Microsoft Entra Agent ID enabled through Microsoft Agent 365. This requires a Microsoft 365 Copilot license with the Frontier program enabled. If you don't see the Agent ID section, contact your administrator to verify licensing and Frontier access.

Prerequisites for viewing agent identities
To view agent identities in your Microsoft Entra tenant, you need:

A Microsoft Entra user account
To manage agent identities, you need one of these roles:

Agent ID Administrator role
Cloud Application Administrator role
You can also be the owner of the specific agent identity you want to manage.

Navigate to the agent identities list
To view agent identities in your tenant:

Sign in to the Microsoft Entra admin center
In the left-hand navigation pane, select Entra ID > Agent ID > All agent identities
This page displays a list of all agent identities in your organization, including both:

Agent identity objects: Proper agent identities created using the Agent ID platform
Agents using application service principals: Agents created before the Agent ID platform were introduced (for example, some agents from Microsoft Copilot Studio and Microsoft Foundry)
Understanding the agent identities view
The All agent identities list provides comprehensive information about each agent identity in your tenant. By default, you see several columns of information for each agent.

Default columns
The following columns are shown by default:

Column Name
Description
Name:	Display name of the agent identity. A human-friendly name that appears in various experiences. You can select a name to view full details.
Status:	Current operational state: Active or Disabled. Disabled agents can't obtain tokens and users can't access them.
Created: On	Date when the agent identity was created.
Object ID:	Unique identifier for the agent identity within the Microsoft Entra tenant.
Agent Blueprint ID:	Unique identifier for the agent identity blueprint that created this agent identity. This column is blank for agents using traditional service principals.
Owners:	Direct link to view owners and sponsors for the agent identity.


Other columns available
To see all available columns, select the Choose columns button. Extra columns include:

View Access: Direct link to the agent identity's permissions
Uses agent identity: Indicates whether the agent has an agent identity object or uses a service principal
Search for agent identities
The search box at the top of the list allows you to quickly find specific agent identities:

Enter either the name or object ID of the agent identity you want to find
The list filters in real time as you type
To search by Agent Blueprint ID:

Select the filter icon or Add filter option
Select Agent Blueprint ID from the available filters
Enter the blueprint ID to find all agent identities created from that blueprint
Filter agent identities
You can refine the agent identities list using various filter criteria:

Filter by status
Select the Status column header or add a status filter
Choose from:
Active: Agents that can currently obtain tokens and be accessed
Disabled: Agents that are blocked from obtaining tokens
Filter by creation date
Select the Created On column header or add a created-on filter
Choose a time range such as "Last 7 days," "Last 30 days," or specify a custom date range
Filter by identity type
Add the Uses agent identity filter
Select:
Yes: Show only agents using agent identity objects
No: Show only agents using traditional application service principals
This filter is useful to distinguish between modern agent identities and legacy agent implementations.

Filter by agent blueprint
Add the Agent Blueprint ID filter
Enter a specific blueprint ID
The list shows only agent identities created from that blueprint. A list of blueprint aligned agents is helpful when you want to view all instances of a particular type of agent (for example, all "Sales Assistant Agents").

Sort agent identities
Most columns support sorting to help you organize the list:

Select any column header with a sort indicator
Select once for ascending order
Select again for descending order
Sortable columns include:
Name
Status
Created On
View agent identity details
To see full details about a specific agent identity:

Select the agent identity's name from the list
The agent identity details pane opens, showing:
Overview: Name, description, logo, status, and link to parent agent identity blueprint
Owners and Sponsors: List of humans accountable for the agent
Permissions: Agent's granted permissions and Microsoft Entra roles
Audit logs: Historical activity performed by the agent
Sign-in logs: Authentication events for the agent identity
Select multiple agent identities
You can select multiple agent identities to perform bulk actions:

Check the box next to each agent identity
The toolbar shows available actions for selected agents
Actions include:
Disable: Block selected agents from obtaining tokens
Enable: Re-enable previously disabled agents
View agent identity blueprints
To view the blueprints that create agent identities:

From the All agent identities page, select View agent blueprint in the upper right of the command bar
The view switches to show agent identity blueprint principals in your tenant
The agent identity blueprint view includes similar columns:

Column Name
Description
Name	Display name of the agent identity blueprint principal
Agent identities	Number of child agent identities created by this blueprint. Select to see linked agent identities.
Status	Operational state (Active or Disabled)
Agent Blueprint ID	Unique identifier for the blueprint
Object ID	Unique identifier for the blueprint principal
Practical scenarios
Scenario 1: Find all agents of a specific type
To find all instances of a particular agent (for example, all "Contoso Sales Agents"):

Navigate to All agent identities
Add the Agent Blueprint ID filter
Enter the blueprint ID for "Contoso Sales Agent"
View all agent identities created from that blueprint
Scenario 2: Disable all agents from a specific blueprint
To disable all agents of a particular type:

Filter by the Agent Blueprint ID
Select all agent identities in the filtered list (check the box next to each)
Select Disable in the toolbar
Alternatively, you can:

Navigate to View agent blueprint
Select the specific blueprint
Disable the blueprint, which prevents all its agent identities from authenticating


Scenario 3: Review recently created agents
To see which agents were created in the last week:

Add or select the Created On filter
Choose "Last 7 days"
Review the list of newly created agent identities
Select any agent to view its details, owners, and permissions


Scenario 4: Audit agent activity
To investigate what a specific agent has been doing:

Search for the agent by name or object ID
Open the agent details by selecting the agent
Navigate to the Audit logs or Sign-in logs tabs
Review the agent's authentication events and actions performed



### Understand access and permissions

Summarize

Turn into podcast
Unit 5/7
Managing agent identities requires specific Microsoft Entra roles and understanding how permissions work in the agent identity model. This unit explains the roles required, how permission inheritance works, and key security restrictions.

Required roles for managing agent identities
There are several different types of roles that can perform operations on agent identities, from viewing to managing.

Roles for viewing agent identities
To view agent identities in the Microsoft Entra admin center:

Any Microsoft Entra user account can view the agent identities list
Roles for managing agent identities
To manage agent identities (create, update, disable, delete), you need one of the following roles:

Agent ID Administrator: Full permissions to manage agent identities, agent identity blueprints, and related configurations. Use the role for agent identity management (recommended).

Cloud Application Administrator: Can manage enterprise applications and agent identities. This role has broader application management permissions beyond just agent identities.

Owner of the agent identity: If you're designated as an owner of a specific agent identity, you can manage that agent identity without holding a tenant-wide administrator role.

Roles for creating agent identity blueprints
Creating agent identity blueprints requires different roles depending on the type of permissions being configured:

Agent ID Developer or Agent ID Administrator — to create blueprints and blueprint principals
Privileged Role Administrator — required to grant Microsoft Graph application permissions to the blueprint (used for autonomous, app-only agent scenarios)
Cloud Application Administrator or Application Administrator — required to grant Microsoft Graph delegated permissions to the blueprint (used for interactive agents acting on behalf of a user)
Roles blocked from agent identities
For security reasons, the following high-privilege directory roles can't be assigned to agent identities:

Global Administrator
Privileged Role Administrator
User Administrator
Agent identities can only be assigned lower-privileged roles. Additionally, custom roles can't be assigned to agent identities, and agent identities can't be members of role-assignable groups.

Permission inheritance model
Agent identities inherit permissions from their parent agent identity blueprints. Understanding this inheritance model is crucial for effective permission management.

How permission inheritance works
Blueprint defines permissions: The agent identity blueprint declares:

Microsoft Graph permissions (delegated and application scopes)
App roles that can be assigned
Authentication settings and optional claims
Agent identities inherit: When an agent identity is created from a blueprint, it can inherit:

Delegated permissions from the blueprint (when impersonation is used)
Common characteristics like app roles and authentication settings
Direct assignment also possible: Agent identities can also receive permissions through direct assignment, independent of blueprint inheritance

Delegated permissions inheritance
When InheritDelegatedPermissions is enabled on the agent identity service principal:

The agent identity inherits delegated permissions from its parent agent identity blueprint
Simplifying permission management in multi-instance scenarios
The inheritance applies when impersonation is used during authentication
Application permissions
Application permissions (for app-only operations) can be assigned:

Directly to the agent identity for instance-specific access requirements
To the blueprint (though agent identities don't inherit application permissions automatically)
Permission reassignment when publishing agents
A critical scenario for permission management occurs when publishing agents in platforms like Microsoft Foundry:

The challenge
During development: Unpublished agents use the shared project identity
After publishing: The agent receives a distinct agent identity with its own agent identity blueprint
Required action
When you publish an agent:

RBAC permissions don't transfer: Permissions assigned to the shared project identity don't automatically transfer to the new distinct identity
You must reassign permissions: Navigate to each Azure resource the agent accesses and assign the required RBAC role to the new agent identity
Verify access: Test that the published agent can still access all required resources
Example: Publishing a Microsoft Foundry agent
Before publishing:

Agent uses shared project identity (AgentIdentity-Project-ABC)
Shared identity has Storage Blob Data Reader role on Storage Account XYZ
After publishing:

Agent receives distinct identity (AgentIdentity-Agent-123)
Distinct identity has no permissions yet
You must assign Storage Blob Data Reader role to AgentIdentity-Agent-123 on Storage Account XYZ
Azure RBAC role assignment
Agent identities can be assigned Azure RBAC roles just like service principals:

Navigate to the Azure resource (Storage Account, Key Vault, etc.)
Select Access control (IAM)
Add role assignment
Select the appropriate role (for example, Storage Blob Data Reader)
Search for the agent identity by its name or object ID
Assign the role
[!Note] You need the object ID of the agent identity for role assignments.

Microsoft Graph permissions
Agent identities can be granted Microsoft Graph permissions to access Microsoft Entra resources:

Permissions blocked for agents
For security, certain high-risk Microsoft Graph permissions are blocked for agent identities. Examples include:

Blocked permission
Notes
Application.ReadWrite.All	Allows managing all applications
RoleManagement.ReadWrite.All	Includes full control over users, groups, roles, directory settings, and other critical operations
User.ReadWrite.All	Grants full control of all user accounts
Directory.AccessAsUser.All	Grants access to information in the directory as the signed-in user
Agents can still be granted lower-privilege permissions as appropriate. For example, if an agent needs to read a user's mailbox or OneDrive file on that user's behalf, it can request a delegated permission like Mail.Read or Files.Read. Any tenant-scoped privileges that go beyond a single user or involve administrative control are blocked.

Requesting Microsoft Graph permissions
To grant Microsoft Graph application permissions to an agent identity:

Create an app role assignment via Microsoft Graph API:


Copy
POST https://graph.microsoft.com/v1.0/servicePrincipals/<agent-identity-id>/appRoleAssignments
Use admin consent URL to direct an administrator to grant permissions:


Copy
https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/adminconsent
?client_id=<agent-identity-client-id>
&role=User.Read.All
&redirect_uri=https://entra.microsoft.com/TokenAuthorize
Owners and sponsors
The agent identity platform introduces an administrative model that separates technical administration from business accountability:

Owners
Role: Technical administrators for agents
Responsibilities: Operational and configuration tasks
Permissions: Can manage the agent identity's technical settings
Sponsors
Role: Business accountability for agents
Responsibilities: Lifecycle decisions and business justification
Permissions: Don't require technical administrative access
Purpose: Provide a human contact for security incidents and compliance
Managers (for agent users)
Role: Designated hiring manager or operational owner
Applies to: Agent users specifically (not regular agent identities)
Best practices for permission management
Use blueprint inheritance: Configure common permissions on the blueprint to simplify management across multiple agent instances

Apply least privilege: Grant only the minimum permissions required for the agent to perform its function

Document permission requirements: Maintain documentation of which permissions each agent type requires

Plan for publishing: Before publishing agents, document which permissions need to be reassigned to the new distinct identity

Regular permission audits: Periodically review agent identity permissions to ensure they're still appropriate

Use sponsors: Designate sponsors for agent identities to maintain business accountability

Monitor permission usage: Review agent activity in audit logs to verify permissions are being used appropriately

Security restrictions summary
**Security Control**                         **Restriction**

High-privilege roles:	Global Admin, Privileged Role Admin, User Admin blocked
Custom roles:	Can't be assigned to agent identities
Role-assignable groups:	Agent identities can't be members
Microsoft Graph permissions:	Several high-risk permissions blocked
Credential management:	Agent identities can't manage their own credentials

These restrictions help ensure that AI agents operate with appropriate access controls and can't gain excessive privileges that could compromise tenant security.




### Understand Microsoft Graph operations

Summarize

Turn into podcast
Unit 6/7
Microsoft Graph provides programmatic access to agent identities. You can enable automation of agent identity management, bulk operations, and integration with existing governance workflows. This unit introduces how Microsoft Graph can be used to query and manage agent identities.

What is Microsoft Graph for agent identities?
Microsoft Graph is the unified API for accessing data across Microsoft 365, Windows, and Enterprise Mobility + Security. For agent identities, Microsoft Graph provides:

API endpoints to query agent identities, agent identity blueprints, and related objects
Programmatic operations including list, read, create, update, and delete
Metadata retrieval about agent identities and their relationships
Bulk management capabilities for large-scale agent deployments
The Microsoft Entra Agent ID APIs in Microsoft Graph help you create, secure, and manage AI agent identities operating in your organization.


**Core Microsoft Graph resources for agent identities**

The Agent ID platform introduces several Microsoft Graph resource types:


This table is extremely important because it explains the **core objects of Microsoft Entra Agent ID**.

## The Big Picture

Think of creating an AI agent like creating a new employee in a company.

```text
Blueprint
    ↓
Blueprint Principal
    ↓
Agent Identity
    ↓
(Optional) Agent User
    ↓
Managed in Agent Registry
```

---

## 1. Blueprint

**Graph Resource:**
`agentIdentityBlueprint`

**API Version:**
`v1.0 (GA)`

**Purpose:**
Template defining:

* Identity type
* Permissions
* Access policies
* Authentication requirements

Think of it as:

> "Job description for future AI agents."

### Example

You create a blueprint:

```text
Customer Support Agent

Permissions:
- Read tickets
- Update tickets
- Read customer records
```

The blueprint stores these rules.

---

## 2. Blueprint Principal

**Graph Resource:**
`agentIdentityBlueprintPrincipal`

**API Version:**
`v1.0 (GA)`

**Purpose:**

Represents the blueprint after it is added to a tenant.

Think:

```text
Blueprint = Design

Blueprint Principal = Blueprint installed in your organization
```

### Example

Microsoft provides a blueprint.

Your company imports it.

The imported version becomes:

```text
agentIdentityBlueprintPrincipal
```

---

## 3. Agent Identity

**Graph Resource:**
`agentIdentity`

**API Version:**
`v1.0 (GA)`

**Purpose:**

Actual identity used by the AI agent for authentication.

This is the most important object.

Think:

```text
Employee ID card
```

The agent uses this identity to:

* Access Microsoft Graph
* Access SharePoint
* Access Teams
* Access Outlook
* Authenticate securely

### Exam Question

**Which object performs authentication?**

✅ Agent Identity

---

## 4. Agent User

**Graph Resource:**
`agentUser`

**API Version:**
`Beta`

**Purpose:**

Optional user account for scenarios requiring a user object.

Think:

```text
AI agent pretending to be a user account
```

Used when systems specifically require:

```text
User object
```

instead of

```text
Application identity
```

### Exam Question

Which component is optional?

✅ Agent User

---

## 5. Agent Registry

**Graph Resource:**
`agentRegistry`

**API Version:**
`Beta`

**Purpose:**

Central repository for managing all agents.

Think:

```text
HR database for AI agents
```

Stores:

* Registered agents
* Agent identities
* Metadata
* Management information

### Exam Question

Which component acts as a centralized repository?

✅ Agent Registry

---

# Easy Memory Trick

```text
Blueprint
↓
Defines rules

Blueprint Principal
↓
Blueprint installed

Agent Identity
↓
Authentication

Agent User
↓
Optional user account

Agent Registry
↓
Agent database
```

---

# One-Line Exam Cheat Sheet

| Component           | Remember As                  |
| ------------------- | ---------------------------- |
| Blueprint           | Agent template               |
| Blueprint Principal | Template installed in tenant |
| Agent Identity      | Authentication identity      |
| Agent User          | Optional user account        |
| Agent Registry      | Agent database/repository    |

### Fast Recall

```text
Template → Installed Template → Identity → Optional User → Registry
Blueprint → Principal → Agent Identity → Agent User → Agent Registry
```

If Microsoft asks **"Which Entra Agent ID object is used for authentication?"**, the answer is almost always:

✅ **Agent Identity (`agentIdentity`)**.




Warning
The agentUser and agentRegistry resources are only available under the Microsoft Graph /beta endpoint. Beta APIs are subject to change and are not supported for use in production applications.

Common Microsoft Graph operations
List agent identities
To retrieve all agent identities in your tenant:

http

Copy
GET /servicePrincipals/microsoft.graph.agentIdentity
This query returns a list of agent identities with their properties.

Query specific agent identity
To get details about a specific agent identity:

http

Copy
GET /servicePrincipals/{agent-identity-id}/microsoft.graph.agentIdentity
Replace {agent-identity-id} with the object ID of the agent identity.

Filter and search
Microsoft Graph supports OData query parameters for filtering and searching:

http

Copy
GET /servicePrincipals/microsoft.graph.agentIdentity?$filter=displayName eq 'Contoso Sales Agent'
Retrieve agent identity metadata
You can retrieve detailed metadata including:

Display name and description
Status (enabled/disabled)
Created date
Agent blueprint relationship
Assigned permissions and roles
Owners and sponsors
Permission requirements for Microsoft Graph operations
To query agent identities programmatically, you need appropriate Microsoft Graph permissions:

Read agent identities
There are two ways to read information about agent identities.

AgentIdentity.Read.All (application permission) or AgentIdentity.Read (delegated permission)
Alternatively, broader permissions like Directory.Read.All include agent identity read access
Manage agent identities
Similar methods exist to modify agents.

AgentIdentity.ReadWrite.All (application permission) or AgentIdentity.ReadWrite (delegated permission)
For creating blueprints: AgentIdentityBlueprint.Create
Request admin consent
Many agent identity operations require administrator consent. Tenant administrators must grant these permissions before applications can programmatically manage agent identities.

Relationship between portal operations and Graph API
Operations you perform in the Microsoft Entra admin center typically correspond to Microsoft Graph API calls:

Portal Action
Microsoft Graph API
View all agent identities	GET /servicePrincipals/microsoft.graph.agentIdentity
View agent identity details	GET /servicePrincipals/{id}/microsoft.graph.agentIdentity
Disable an agent identity	PATCH /servicePrincipals/{id} with accountEnabled: false
Retrieve agent identity permissions	GET /servicePrincipals/{id}/appRoleAssignments
List blueprints	Query for service principals of type agent identity blueprint
Understanding this mapping helps you automate manual tasks and integrate agent identity management into your existing IT workflows.





### Summary
Unit 7/7
In this module, you learned about Microsoft Entra Agent ID, a specialized identity type designed for AI agents operating in enterprise environments. You explored how agent identities differ from traditional identity types like service principals and managed identities. Then you learned agent identities address unique security and operational challenges posed by AI agents.

What is Microsoft Entra Agent ID?
Microsoft Entra Agent ID introduces two key components:

Agent identities: Special service principals that represent AI agents, using token-based authentication without passwords
Agent identity blueprints: Reusable templates that create and manage multiple agent identities, providing centralized governance
The impersonation model allows blueprints to obtain tokens on behalf of agent identities, enabling flexible authentication while maintaining clear audit trails.

How agent identities compare to other identity types
Agent identities differ from service principals and managed identities in several ways:

Credential management: Agent identities rely on parent blueprint credentials rather than managing their own
Lifecycle: Designed for ephemeral, rapidly created and destroyed agents rather than long-lived resources
Security controls: Block high-privilege roles (Global Admin, Privileged Role Admin, User Admin), preventing excessive access
Scale: Support thousands of agent instances managed through a single blueprint
Microsoft products that use agent identities
Several Microsoft platforms automatically create agent identities:

Microsoft Foundry: Shared identities for development, distinct identities when published
Microsoft Copilot Studio: Automatic identity assignment when enabled at environment level
Microsoft Agent 365: Each agent receives its own agent identity
Azure App Service/Functions and Microsoft Teams: Developer-managed blueprint creation
Managing agent identities through the admin center
The Microsoft Entra admin center provides comprehensive management capabilities:

Navigate to Entra ID > Agent ID > All agent identities
View, search, filter, and sort agent identities by various criteria
Distinguish between agent identity objects and legacy service principal-based agents
Take actions like enabling, disabling, and viewing detailed permissions
View agent identity blueprints and their child agent identities
Access requirements and permission management
Effective agent identity management requires understanding:

Required roles: Agent ID Administrator, Cloud Application Administrator, or agent owner
Permission inheritance: Agent identities inherit delegated permissions from blueprints
Publishing considerations: RBAC permissions must be manually reassigned when publishing agents
Security restrictions: High-privilege roles and certain Microsoft Graph permissions are blocked
Administrative model: Separation of technical ownership and business sponsorship
Programmatic access through Microsoft Graph
Microsoft Graph enables automation and scale:

Query agent identities and blueprints using REST APIs
Perform bulk operations across multiple agent identities
Integrate agent identity management into existing governance workflows
Automate lifecycle management, compliance reporting, and monitoring
Retrieve metadata about agent identities, permissions, and relationships
Meeting our scenario goals
Remember the scenario from the introduction: you're managing AI agent identities across multiple departments using various Microsoft platforms. With the knowledge from this module, you can now:

Distinguish AI agent operations from human operations in audit logs
Apply consistent security policies to all agents of a particular type using blueprints
Prevent agents from gaining excessive privileges through built-in security restrictions
Scale identity management through blueprints that create and manage thousands of agent instances
View and manage all agent identities centrally through the Microsoft Entra admin center
Automate agent identity operations using Microsoft Graph APIs




Learn more
What is Microsoft Entra Agent ID - https://learn.microsoft.com/en-us/entra/agent-id/identity-platform/what-is-agent-id
Agent identity and blueprint concepts - https://learn.microsoft.com/en-us/entra/agent-id/identity-platform/key-concepts
View and manage agent identities - https://learn.microsoft.com/en-us/entra/agent-id/identity-platform/agent-lists
Microsoft Graph Agent ID APIs - https://learn.microsoft.com/en-us/graph/api/resources/agentid-platform-overview
Agent identity concepts in Microsoft Foundry - https://learn.microsoft.com/en-us/azure/ai-foundry/agents/concepts/agent-identity








Excellent. This entire module can be compressed into a **one-page exam master sheet**.

# Microsoft Entra Agent ID — Ultimate Exam Notes

## Why Agent ID Exists

Problem:

```text
Traditional identities were not designed for AI agents.
```

Organizations need to:

✅ Distinguish AI actions from human actions
✅ Apply policies to many agents
✅ Prevent excessive privileges
✅ Manage thousands of short-lived agents

Solution:

```text
Microsoft Entra Agent ID
```

---

# Core Components

```text
Blueprint
    ↓
Blueprint Principal
    ↓
Agent Identity
    ↓
(Optional) Agent User
    ↓
Agent Registry
```

| Component           | Purpose                       |
| ------------------- | ----------------------------- |
| Blueprint           | Template                      |
| Blueprint Principal | Blueprint installed in tenant |
| Agent Identity      | Authentication identity       |
| Agent User          | Optional user object          |
| Agent Registry      | Agent database                |

### Most Important Exam Question

Which object authenticates?

✅ Agent Identity

---

# Agent Identity

Think:

```text
Employee ID Card
```

Characteristics:

✅ Object ID
✅ App ID

Important:

```text
Object ID = App ID
```

✅ No passwords

✅ No credentials

✅ Token-based authentication only

---

# Agent Identity Blueprint

Think:

```text
Job Description
```

Contains:

* Permissions
* App roles
* Authentication settings
* OAuth credentials

Purposes:

1. Type classification
2. Template creation
3. Credential holder
4. Management container

---

# Authentication Flow

Traditional Service Principal

```text
Credential
    ↓
Token
```

Agent Identity

```text
Blueprint Credentials
       ↓
Blueprint
       ↓
Token for Agent Identity
```

This is called:

✅ Impersonation Model

---

# Service Principal vs Agent Identity

| Aspect         | Service Principal | Agent Identity     |
| -------------- | ----------------- | ------------------ |
| Credentials    | Own credentials   | No credentials     |
| Authentication | Direct            | Through blueprint  |
| Relationship   | One-to-one        | One-to-many        |
| Permissions    | Direct            | Direct + inherited |
| Authentication | Performs itself   | Blueprint performs |

---

# Managed Identity vs Agent Identity

| Managed Identity          | Agent Identity                |
| ------------------------- | ----------------------------- |
| Azure Resources           | AI Agents                     |
| VM/App Service            | Copilot/Foundry Agents        |
| One identity per resource | Many identities per blueprint |
| Azure manages credentials | Blueprint manages credentials |

### Exam Favorite

Identity specifically designed for AI agents?

✅ Agent Identity

Identity for Azure resources?

✅ Managed Identity

---

# Security Benefits

### Distinguish AI Operations

Audit logs show:

```text
Human
Application
AI Agent
```

separately.

---

### Right-sized Access

Agents get only required permissions.

---

### Prevent Excessive Privilege

Blocked roles:

❌ Global Administrator

❌ Privileged Role Administrator

❌ User Administrator

---

# Authentication Scenarios

## Attended

Agent acts on behalf of user.

```text
User
 ↓
Agent
 ↓
Resource
```

Uses:

✅ Delegated Permissions

---

## Unattended

Agent acts independently.

```text
Agent
 ↓
Resource
```

Uses:

✅ Application Permissions

---

# Products Using Agent ID

## Automatic Provisioning

```text
Foundry
Copilot Studio
Agent 365
```

| Product        | Automatic? |
| -------------- | ---------- |
| Foundry        | Yes        |
| Copilot Studio | Yes        |
| Agent 365      | Yes        |

---

## Developer Managed

```text
App Service
Functions
Teams
```

| Product     | Automatic? |
| ----------- | ---------- |
| App Service | No         |
| Functions   | No         |
| Teams       | No         |

---

# Foundry Identity Model

### Development

```text
Shared Identity
```

All unpublished agents use:

```text
Project Identity
```

---

### Production

```text
Published Agent
       ↓
New Blueprint
       ↓
New Agent Identity
```

Important:

RBAC permissions do NOT transfer.

You must reassign them.

### Exam Question

What happens after publishing?

✅ New agent identity created

✅ RBAC permissions must be reassigned

---

# Copilot Studio

Enable:

```text
Microsoft Entra Agent Identity
```

Then:

```text
Agent Created
     ↓
Identity Automatically Created
```

---

# Teams

Developer creates:

```text
Blueprint
```

manually.

Agent identity created later.

---

# Admin Center

Navigation:

```text
Entra ID
  ↓
Agent ID
  ↓
All Agent Identities
```

---

# Default Columns

| Column             | Meaning            |
| ------------------ | ------------------ |
| Name               | Agent Name         |
| Status             | Active / Disabled  |
| Created On         | Creation date      |
| Object ID          | Unique ID          |
| Agent Blueprint ID | Parent blueprint   |
| Owners             | Responsible admins |

---

# Useful Filters

Filter by:

✅ Status

✅ Created Date

✅ Blueprint ID

✅ Uses Agent Identity

---

# Roles

## View

Any Entra User

---

## Manage

✅ Agent ID Administrator

✅ Cloud Application Administrator

✅ Owner

---

# Blueprint Creation

| Action                        | Role                          |
| ----------------------------- | ----------------------------- |
| Create Blueprint              | Agent ID Developer            |
| Create Blueprint              | Agent ID Administrator        |
| Graph Application Permissions | Privileged Role Administrator |
| Delegated Permissions         | Cloud App Admin               |

---

# Permission Inheritance

Blueprint

```text
Defines Permissions
```

↓

Agent Identity

```text
Inherits Delegated Permissions
```

Key setting:

```text
InheritDelegatedPermissions
```

---

# Publishing Agent Rule

Before Publishing:

```text
Shared Identity
```

After Publishing:

```text
Distinct Identity
```

Result:

```text
RBAC DOES NOT TRANSFER
```

Remember this.

Microsoft likes asking it.

---

# Graph Permissions Blocked

High-risk permissions:

❌ Application.ReadWrite.All

❌ RoleManagement.ReadWrite.All

❌ User.ReadWrite.All

❌ Directory.AccessAsUser.All

---

# Owners vs Sponsors

### Owner

Technical management

### Sponsor

Business accountability

Memory:

```text
Owner = IT
Sponsor = Business
```

---

# Graph Resources

| Resource                        | Purpose             |
| ------------------------------- | ------------------- |
| agentIdentityBlueprint          | Template            |
| agentIdentityBlueprintPrincipal | Installed blueprint |
| agentIdentity                   | Authentication      |
| agentUser                       | Optional user       |
| agentRegistry                   | Repository          |

---

# API Versions

| Resource            | Version |
| ------------------- | ------- |
| Blueprint           | v1.0    |
| Blueprint Principal | v1.0    |
| Agent Identity      | v1.0    |
| Agent User          | Beta    |
| Agent Registry      | Beta    |

### Exam Favorite

Which resources are Beta?

✅ agentUser

✅ agentRegistry

---

# Microsoft Graph APIs

### List Agents

```http
GET /servicePrincipals/microsoft.graph.agentIdentity
```

---

### Specific Agent

```http
GET /servicePrincipals/{id}/microsoft.graph.agentIdentity
```

---

### Search

```http
?$filter=displayName eq 'Agent'
```

---

# Required Graph Permissions

Read:

```text
AgentIdentity.Read
AgentIdentity.Read.All
```

Write:

```text
AgentIdentity.ReadWrite
AgentIdentity.ReadWrite.All
```

Blueprint Creation:

```text
AgentIdentityBlueprint.Create
```

---

# The 15 Most Likely Exam Questions

1. What is the primary authentication object?

   * ✅ Agent Identity

2. What acts as the template?

   * ✅ Blueprint

3. What represents a blueprint installed in a tenant?

   * ✅ Blueprint Principal

4. Which object is optional?

   * ✅ Agent User

5. Which object is the repository?

   * ✅ Agent Registry

6. Which resources are Beta?

   * ✅ Agent User, Agent Registry

7. What authentication model is used?

   * ✅ Impersonation Model

8. Who manages credentials?

   * ✅ Blueprint

9. Does Agent Identity have passwords?

   * ❌ No

10. Which products automatically provision identities?

    * ✅ Foundry, Copilot Studio, Agent 365

11. Which products are developer managed?

    * ✅ App Service, Functions, Teams

12. What happens when a Foundry agent is published?

    * ✅ New identity created

13. Do RBAC permissions transfer after publishing?

    * ❌ No

14. Which roles are blocked?

    * ✅ Global Admin, PRA, User Admin

15. What is the main purpose of Entra Agent ID?

    * ✅ Secure governance, authentication, and authorization of AI agents.

If you memorize just this sheet, you'll cover roughly **90–95% of the questions likely to come from the Entra Agent ID module**.


























































































































































































































