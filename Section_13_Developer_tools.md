# Developer Tools

---

## AWS Access Keys, CLI, SDK

Users can access AWS through:
- **AWS Management Console:** Web-based interface for managing AWS resources.
- **AWS CLI:** Command-line tool to interact with AWS services. Direct access to public APIs, supports scripting and automation, open source.
- **AWS SDK:** Software Development Kit with language-specific APIs, enables developers to access and manage AWS services programmatically from within applications.

**Access and secret keys** are generated via the console, allowing users to manage their own credentials.

---

## AWS CDK (Cloud Development Kit)

- Define cloud infrastructure using familiar programming languages (TypeScript, Python, Java, C#, Go).
- Contains high-level components called **constructs**.
- CDK code is compiled into a CloudFormation template.
- Enables deployment of infrastructure and application runtime code together.

### CDK vs SAM

- **CDK:**  
  - Supports all AWS services.
  - Infrastructure defined in multiple programming languages.
  - Leverages CloudFormation for deployment.

- **SAM (Serverless Application Model):**  
  - Focused on serverless services only.
  - Templates written in JSON/YAML.
  - Also leverages CloudFormation for deployment.

---

## AWS CodeDeploy

- Automates application deployments to EC2 instances and on-premises servers.
- Requires servers/instances to be provisioned and configured with the CodeDeploy agent.
- Supports blue/green and rolling deployments.
- Integrates with CodePipeline for CI/CD.

---

## AWS CodeBuild

- Fully managed, serverless build service.
- Compiles source code, runs tests, and produces deployable packages.
- Highly available and secure.
- Pay-as-you-go pricing (charged for build time only).
- Supports custom build environments and integration with CodePipeline.

---

## AWS CodePipeline

- Orchestrates steps for continuous integration and continuous delivery (CI/CD).
- Automates code build, test, provision, and deployment.
- Fully managed and serverless.
- Integrates with CodeBuild, CodeDeploy, CloudFormation, and custom plugins.
- Enables fast delivery and rapid feedback for development teams.

---