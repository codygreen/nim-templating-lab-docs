# Templating in NGINX Instance Manager

## Background

Since the inception of NGINX Instance Manager (NIM), managing NGINX configuration has been a use case of prime importance that F5 has invested development in. To date, capabilities in NIM to edit configuration have required knowledge of NGINX configuration syntax, and direct manipulation of conf files. For users who already have intimate knowledge of this, this may be trivial. However, not all organizations have staff that are experienced and comfortable in doing so. In an enterprise that has multiple personas with a stake in web application or API delivery and security, this may necessitate a different approach to aid usability and confidence in making configuration changes that are intended.

## Announcing Config Templates for NIM

As of the 2.16 (April 2024) release, NIM introduces Go templating to simplify creating and standardizing NGINX configurations. These config templates create an abstraction layer for NGINX configuration files, enabling users to provide parameters to generate a working configuration without needing a deep knowledge of NGINX syntax. These templates simplify configuring NGINX, enforce best practices for configurations, and enable self-service permissions for app development.

In this lab we will introduce you to the design on the NIM templating system, as well as walk through using it to apply configuration changes to NGINX instances as different personae.

> Note: At the time of initial release, NIM ships with a single, general-purpose Base Template. In the future, NGINX aspires to create a vetted set of templates that users can import into NIM for direct consumption, or to be used as a starting point for further customization.

## Simple Configuration & Self-Service through Templates

The templating system for NIM has the following benefits:

### Simplify Configurations

Templatized configurations make using NGINX extremely easy. Provide only the required parameters via the UI or API.

### Ensure Standards

Maintain consistency and best practices across your NGINX estate by managing crucial settings centrally.

### Empower App Dev Teams

Allow teams to manage and own the contents of their own location or server blocks.

<!-- ### Provided Common Use Cases

Load balancing and API gateway templates from F5 NGINX included. -->

### Maximum Flexibility

Customize existing templates or build your own to suit your needs.

## Config Templates Design

### Types of templates

Configuration templates come in two types:

- **Base templates**: A base template is a comprehensive set of instructions used to generate a complete NGINX configuration. It includes all the necessary directives and parameters to create a functional NGINX configuration from scratch. Essentially, it’s the foundational configuration on which your NGINX instance operates.

- **Augment templates**: Augment templates are designed to add specific features or modify existing configurations generated by the base template. They include:

  - Feature augments: These add specific functionalities such as caching, authentication, or rate limiting to the NGINX configuration.
  - Segment augments: These modify or add configuration segments like additional server blocks, location directives, or upstream definitions.

### Template resource files

Configuration templates include the following components:

- **Template files (.tmpl)**: Written in Go's templating language, these files define the NGINX configuration's structure and parameters.
  
- **JSON schema files (.json)**: These files create the rules for validating user inputs and generate dynamic web forms for data entry.

- **Auxiliary files**: Additional files required for configuration, such as JavaScript for added functionality, security certificates, or documentation (README.md). These files support the main configuration and provide necessary context or capabilities.

## Target

A target refers to the specific NGINX server instance, instance group, or staged config where a template (base or augment) is intended to be applied. It's the designated location or context within which the generated configuration will be active and operational.

There are three types of targets:

1. **Individual NGINX instance**: Targets a single server, allowing for precise configuration updates or replacements.

2. **Instance group**: A collection of NGINX instances managed as a single group. Applying a template to an instance group ensures uniform configuration across all its servers.

3. **Staged config**: A staging area for configurations before deployment, allowing for testing and validation to minimize potential disruptions upon live deployment.

## Template submission

Template submission involves applying a set of configurations (derived from base and/or augment templates) to a target. This action takes the parameters defined in the templates, generates the final NGINX configuration, and deploys it to the specified target. RBAC plays a pivotal role here, determining who can submit and modify template submissions. Template submission effectively bridges the gap between configuration design and operational use.

Key aspects of template submission include:

- **Snapshots**: Snapshots are created when templates are submitted. Snapshots capture the state of the template and its inputs at the time of submission. This includes all the settings, parameters, and the structure defined in both base and augment templates. By creating a snapshot, NGINX Instance Manager preserves a record of the exact configuration applied to a target at a specific point in time. This is crucial for auditing purposes, rollback scenarios, and understanding the evolution of a server's configuration.

- **Target application**: When submitting a template, it's important to specify the target accurately. The target is the NGINX instance, instance group, or staged config where the generated configuration will be applied. Misidentifying the target can lead to configurations being deployed to unintended environments, potentially causing disruptions.

- **Role-based access control (RBAC)**: With RBAC, administrators can limit who can create and modify template submissions based on team roles or individual responsibilities, ensuring only authorized users can change NGINX configurations.