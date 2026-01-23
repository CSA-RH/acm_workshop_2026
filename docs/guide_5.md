# Governance, Risk, and Compliance
## Creating Policies in ACM

At this point, you have completed the overview labs for Cluster Lifecycle and Application Lifecycle capabilities in RHACM. In the Cluster Lifecycle Lab, you learned how RHACM can help manage the lifecycles of your Kubernetes clusters, in that lab, you configured your RHACM instance to manage an OpenShift cluster.

In the Application Lifecycle Lab, you continued exploring RHACM functionality and learned how to deploy and configure an application. You used the cluster that you added in the first module as the target for deploying an application.

Now that you have a cluster and a deployed application, you need to make sure that they do not drift from their original configurations. This kind of drift is a serious problem, because it can happen from beginning and benevolent fixes and changes, as well as malicious activities that you might not notice but can cause significant problems. The solution that RHACM provides for this is the Governance, Risk, and Compliance, or GRC, functionality.
Review GRC Functionality

To begin, it is important to define exactly what GRC is. In RHACM, you build policies that are applied to managed clusters. These policies can do different things, which are described below, but they ultimately serve to govern the configurations of your clusters. This governance over your cluster configurations reduces risk and ensures compliance with standards defined by stakeholders, which can include security teams and operations teams

This is a complex and emerging/evolving topic, and this course is only providing an overview. Please consult the [GRC product documentation](https://docs.redhat.com/en/documentation/red_hat_advanced_cluster_management_for_kubernetes/2.15/html/governance/index) for more details on any of these policy controllers.

## Review GRC Functionality
To begin, it is important to define exactly what GRC is. In RHACM, you build policies that are applied to managed clusters. These policies can do different things, which are described below, but they ultimately serve to govern the configurations of your clusters. This governance over your cluster configurations reduces risk and ensures compliance with standards defined by stakeholders, which can include security teams and operations teams

This table describes the types of policy controllers available in RHACM:

| Use                                                                 | Policy Template type | Controller used for enforcement        |
|---------------------------------------------------------------------|----------------------|----------------------------------------|
| Deploy Kubernetes manifests (e.g. Deployment, Namespace, ConfigMap, etc.) | ConfigurationPolicy  | Configuration Policy Controller        |
| Certificate compliance or validity rules                             | CertificatePolicy    | Certificate Policy Controller          |
| Operator installation or subscription manifests (for Operator Lifecycle Manager operators only) | OperatorPolicy       | Operator Policy Controller             |
| Group policies                                                      | PolicySet            | PolicySet Controller                   |

You need to create three different resources in order to implement the policy controllers:

- Policy: The Policy defines what you actually want to check and possibly configure (with enforce). Policies include a policy-template which defines a list of objectDefinitions. The policy also determines the namespaces it is applied to, as well as the remediation actions it takes.

- Placement: Identifies a list of managed clusters that are targeted, to deploy the Policy or PolicySet.

- PlacementBinding: Connect the policy to the Placement.

### Create a Policy
We’ll go through a simple example, and create a policy in the open-cluster-management namespace.
For this, we’ll need a little setup:

#### Bind the open-cluster-management namespace with the ClusterSet global
- Navigate to Clusters and access the ClusterSets tab.

  ![Alt text](../images/policy1.png?raw=true "policy1")

- Click on the 3-dots button on the right, and then on Edit namespaces bindings and add the open-cluster-management namespace.

  ![Alt text](../images/policy2.png?raw=true "policy2")

#### Create a Policy to inform if etcd is encrypted:

- Navigate to the Governance screen and click create policy. 

  ![Alt text](../images/policy3.png?raw=true "policy3")

- Build a policy with the following configuration:

  1. Navigate to the Governance screen and click create policy.

  1. Under the Create Policy screen, enable the YAML. Copy and Paste the ETCD cryption Policy YAML we have provided below:
      - Name: policy-etcdencryption
      - Namespace: open-cluster-management
      ![Alt text](../images/policy1a.png?raw=true "policy1a")
      - press next button

  1. In Policy Templates menu screen configure:
      - remediationAction: inform
      - press on Add policy template and selet Enable etcd encryption
      ![Alt text](../images/policy2a.png?raw=true "policy2a")

      - Prune Object Behavior: None
      - Keep the "configuration objects" and the rest of configuration to the default
      ![Alt text](../images/policy3a.png?raw=true "policy3a")
      - press next button

  1. In Placement menu screen configure:
 
     - press new placement
        - keep the default name
        - select the global clusterset
        ![Alt text](../images/placement1a.png?raw=true "placement1a")

        - press on + Add label expression
          - Label: environment
          - Operator: equals any of
          - Values: development
          ![Alt text](../images/placement2a.png?raw=true "placement2a.png")

        - Leave everything else as default and click NEXT twice.
          ![Alt text](../images/placement3a.png?raw=true "placement3a.png")

        -  Press submit

#### Lets check the Policies compliance

Navigate to the governance tab and check the policies violations

- Here you find one Violation in the local-cluster, note that the local cluster have set the label environment=development, so the Placement applyed the Policy to this cluster.
  ![Alt text](../images/policy1b.png?raw=true "policy1b.png")

- Press on the Clusters -> local-cluster: this cluster has one violation against the policy policy-etcdencryption
  ![Alt text](../images/policy1c.png?raw=true "policy1c.png")
 
- Press on the policy "policy-etcdencryption"
  ![Alt text](../images/policy1d.png?raw=true "policy1d.png")
 
 

Since we created this policy as a Inform only it will not fix any of the violations, lets go ahead and fix them:

- On the top of the policy click on the Actions → Edit Policy

- Select Step 2 and change the Remediation to Enforce

- Select Step 5 review that is under Remediation is set to Enforce

- Click Submit
  ![Alt text](../images/policy1e.png?raw=true "policy1e.png")

Navigate to the Results screen, allow the remediation to complete, it may take longer (20-30 mins) to enforce the policy.








