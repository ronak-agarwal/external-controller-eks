## EKS external-controller

Any component that works as operator/controller, they rely on base infra being deployed without they being managed, as the controller itself runs on EKS node-groups of the cluster. Its a generic gap where controllers within the cluster itself are responsible for keeping your cluster and workloads healthy. You can end-up nasty chicken-n-egg scenarios and race-conditions.

We potentially need something external to EKS cluster to solve some of the problems.
 * Run these controller images on Lambda, OR
 * Have some plugin which could allow deployment of these controllers on control plane, OR
 * Follow Openshift approach of having dedicated infra nodes for such controllers

### Example problems

 * Imagine having cluster-critical workload which is larger than single node and cluster-autoscaler OR Karpenter is not scheduled, so your cluster will never scale-out
 * Any controller/operator which deals with validation/mutating webhooks and have security or health related duties (Eg OPA-gatekeeper), where you will end-up exempting those core namespaces from security checks to avoid failures like Node unable to join because your aws-node CNI blocked by gatekeeper, as gatekeeper itself is unhealthy or race-condition between gateekeper & aws-node

There could be many more reasons or scenarios

### Some Options below

 * OPA Gatekeeper running externally on Lambda 
 ![architecture] (https://github.com/ronak-agarwal/external-controller-eks/blob/master/images/screenshot1.png)
 
 * Fargate Management Cluster to run CA and manage scaling of remote clusters
# ![architecture] ()
 
 * Use custom solution instead of using CA for scaling EKS nodes - SQS , Lambda, CloudWatch
# ![architecture] ()