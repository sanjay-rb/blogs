# Build your own CI/CD Pipeline for Snowflake, Airflow, DBT, Python & Other Configuration Files (Data Pipeline)

## Introduction 

> TODO: Intro Image

This page offers a detailed guide for building a complete **CI/CD pipeline** for your **data pipeline**. It includes instructions for deploying Snowflake DDLs, Airflow DAGs, DBT models, Python/PySpark scripts, and configuration files.

We often see DevOps CI/CD pipelines for application codes like Java, Python, Node, and .NET, where everything can be deployed as a single zip file or artifact. However, for this **DataOps CI/CD** pipeline, we need to apply the same CI/CD principles but with additional engineering needed to deploy each component individually. This is necessary because different technologies require **distinct deployment methods** and options.

## Table of Contents:

1. Requirements
2. Supported Technologies and Future Expansion
3. Source Code Management Strategy
4. Repository Structure
5. Code Quality Check Pipeline and Key Features
6. Code Deployment Pipeline and Key Features
7. Architecture Diagram
8. Conclusion


## Requirements

1. Basic understanding of Python (Python 3.11)
2. Basic understanding of SQL
3. Intermediate knowledge of Git
4. Familiarity with DevOps tools, particularly Azure DevOps
5. Understanding of core CI/CD concepts

## Supported Technologies and Future Expansion 

1. [Snowflake DDL](https://docs.snowflake.com/en/sql-reference/sql-ddl-summary) Deployment
2. [Airflow](https://airflow.apache.org) DAG Deployment
3. [DBT](https://www.getdbt.com/product/what-is-dbt) Models and Configuration Deployment
4. Python/[PySpark](https://spark.apache.org/docs/latest/api/python/index.html#:~:text=PySpark%20is%20the%20Python%20API,for%20interactively%20analyzing%20your%20data.) Scripts and Configuration Deployment
5. [Snowflake DML](https://docs.snowflake.com/en/sql-reference/sql-dml) Execution (Planned for Future)
6. [AWS Lambda](https://aws.amazon.com/lambda/) Deployment (Planned for Future)
7. [AWS Glue](https://docs.aws.amazon.com/glue/latest/dg/what-is-glue.html) ETL Jobs Deployment (Planned for Future) 


## Source Code Management Strategy

1. For each release, there will be a **single release branch** where the change team will make and commit their changes.
   - **Important**: There should not be more than one release branch for the same release. All members of the change team will work on this single release branch.
   - **Example**: `release/app_name_date`, where `app_name_date` is the **deployment key value**.

2. Once the release changes are complete, a pull request (PR) will be created to merge the release branch back into the main branch using **squash merging**.

3. **Tags** will be added to the squashed release commit based on the environment where it is deployed, such as:
   - `dev-app_name_date-*`
   - `runway-app_name_date-*`
   - `production-app_name_date-*`

4. These tags will help track where each commit has been deployed and identify which commit to use for the next deployment based on the deployment key value (`app_name_date`).

5. If there is any bug fix or issue with the release, the same release branch (`release/app_name_date`) can be reused to update the affected components.

> TODO: Branching Diagram

## Repository Structure

1. As we deploy various technology stacks simultaneously to different locations using different deployment methods, it is essential to maintain an **organized folder structure**.
   
2. A well-organized folder structure simplifies maintenance for the change team and ensures that our deployment pipeline correctly identifies the appropriate deployment code.

3. We have established the following folder structure for our Azure Repository (SCM) to support these requirements.

> TODO: Folder Diagram

## Code Quality Check Pipeline and Key Features

1. When a pull request (**PR**) is created, a **build validation pipeline** will automatically execute various scans and tests. These include:
   - **Lint Scan** using **Sqlfluff**
   - **Static Analysis Scan** using **SonarQube**
   - **Vulnerability Scan** using **Checkmarx One**

2. The PR will only advance to the approval stage if all scans and tests pass successfully. After approval, the changes from the new release branch will be merged into the main branch.

### Lint Scan (Sqlfluff)

1. Since our source code contains a **large number of SQL** files, including Snowflake DDL, DML, and other SQL statements executed by Python scripts, it is essential to ensure consistency and quality across these files.
2. All these `.sql` files require a standardized check to maintain code quality.
3. We searched for the best linter to address this need and found **Sqlfluff: an open-source SQL linter that enforces coding standards and corrects syntax errors across various SQL dialects**.
4. Consequently, we initiated our CI pipeline with a lint scanner by configuring an Azure YML pipeline to run Sqlfluff.

> TODO: YML Code


### Static Analysis Scan (SonarQube) 

1. In addition to SQL files, we have a **significant number of Python files**, which serve as our primary programming language for representing Airflow DAG files, Lambda scripts, and PySpark scripts.
2. Therefore, we have integrated SonarQube as our static analysis framework, which is capable of scanning Software Composition Analysis (SCA), code quality metrics, and security vulnerabilities.
3. We have incorporated SonarQube as the next stage in our YML file.

> TODO: YML Code

### Vulnerability Scan (Checkmarx One)

1. After conducting an in-depth analysis of code quality, it is crucial to focus on **code security**.
2. Although the change team may incorporate code snippets from external sources based on their research, it is our responsibility to ensure that the code they produce is secure and does not pose any security risks to end users.
3. To address this, we have chosen Checkmarx One for a comprehensive security scan that evaluates the entire repository.
4. This is the third stage in our CI pipeline.

> TODO: YML Code
> TODO: CI Pipeline Diagram

## Code Deployment Pipeline and Key Features

1. When a pull request (PR) is merged into the main branch, a new squash commit will be generated based on the release changes. This commit will serve as **the initial commit** for starting our code deployment pipeline.
2. In the continuous deployment (CD) pipeline, a **list of source tags** will be generated from the current tag of the initial trigger commit. From these source tags, we will retrieve the respective **commit hashes**. Using this commit hash, we will obtain a **list of all components** that need to be deployed to the target environment. All components will be taken from the latest version in the main branch.
3. Depending on the repository structure, we will determine the technology type. 
	- **Example**, if files are prefixed with `ddl/snowflake`, the associated DDL files will be **deployed to the Snowflake** target environment.
4. Components will be deployed to various environments, such as development, runway, or any other test environment, and eventually to the production environment. Based on the current deployment status, the main branch **commits will be tagged** with `target-app_name_date-*` for representation.

> TODO: CD Pipeline Diagram 

## Architecture Diagram

> TODO: Architecture Diagram

## Conclusion

By following this approach, you can effectively build a comprehensive DevOps solution for any Data Pipeline using various technology stacks. The key steps are:

1.  Ensure that the technology is correctly defined within the appropriate folder structure.
2.  Adhere to proper CI/CD practices.

With this, I will conclude this page. I hope the information provided has offered valuable insights to help you build your own DataOps solution for any data pipeline. 

> TODO: Thank You & Conclusion Image