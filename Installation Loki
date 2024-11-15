![alt text](image.png)

## What is the goal?

After completing this lab, we will have consolidated all the logs generated in our Kubernetes cluster in a tidy, neat, real-time dashboard in Grafana.

### What are we going to need?

We are going to need a:

- Kubernetes cluster.
- Grafana installation.
- Grafana Loki installation.
- Promtail agent on every node of the Kubernetes cluster.

### What is Grafana?

Grafana is an analytics and interactive visualization platform. It provides a rich variety of charts, graphs, and alerts and connects to plead of supported data sources as Prometheus, time-series databases or the known RDBMs. It allows you to query, visualize, create alerts on your metrics regardless where they are stored.


## Procedure

1. Install the Loki Operator Operator:

    1. In the OpenShift Container Platform web console, click **Operators OperatorHub**.
    1. Choose **Loki Operator** from the list of available Operators, and click **Install**.
    1. Under **Installation Mode**, select **All namespaces on the cluster**.

    1. Under **Installed Namespace**, select **openshift-operators-redhat**.

        You must specify the **openshift-operators-redhat** namespace. The **openshift-operators** namespace might contain Community Operators, which are untrusted and might publish a metric with the same name as an OpenShift Container Platform metric, which would cause conflicts.

    1. Select **Enable operator recommended cluster monitoring on this namespace**.
        
         This option sets the **openshift.io/cluster-monitoring: "true"** label in the Namespace object. You must select this option to ensure that cluster monitoring scrapes the **openshift-operators-redhat** namespace.

    1. Select an **Approval Strategy**.
        - The **Automatic** strategy allows Operator Lifecycle Manager (OLM) to automatically update the Operator when a new version is available.
        - The **Manual** strategy requires a user with appropriate credentials to approve the Operator update. 
    1. Click **Install**.
    1. Verify that you installed the Loki Operator. Visit the **Operators** >  **Installed Operators** page and look for **Loki Operator**.
    1. Ensure that **Loki Operator** is listed with **Status** as **Succeeded** in all the projects. 

1. Create a **Secret** YAML file that uses the access_key_id and access_key_secret fields to specify your AWS credentials and bucketnames, endpoint and region to define the object storage location. For example: 

        apiVersion: v1
        kind: Secret
        metadata:
          name: logging-loki-s3
          namespace: openshift-logging
        stringData:
          access_key_id: AKIA2FS66EATQF75BXLP
          access_key_secret: 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
          bucketnames: s3-bucket-darwikdev
          endpoint: https://us-east-1.console.aws.amazon.com
          region: us-east-1

1. Create the **LokiStack** custom resource (CR): 
    
        apiVersion: loki.grafana.com/v1
        kind: LokiStack
        metadata:
          name: logging-loki
          namespace: openshift-logging
        spec:
          size: 1x.small
          storage:
            schemas:
            - version: v12
              effectiveDate: "2022-06-01"
            secret:
              name: logging-loki-s3
              type: s3
          storageClassName: gp2
          tenants:
            mode: openshift-logging
    
1. Apply the **LokiStack** CR: 
    
        oc apply -f logging-loki.yaml    

1. Create a **ClusterLogging** custom resource (CR): 
        
        apiVersion: logging.openshift.io/v1
        kind: ClusterLogging
        metadata:
          name: instance
          namespace: openshift-logging
        spec:
          managementState: Managed
          logStore:
            type: lokistack
            lokistack:
              name: logging-loki
          collection:
            type: vector

1. Apply the ClusterLogging CR: 

        oc apply -f cr-lokistack.yaml
