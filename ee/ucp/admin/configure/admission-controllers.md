---
title: Admission controllers
description: Learn about how admission controllers are used in docker.
keywords: cluster, psp, security
---


>{% include enterprise_label_shortform.md %}

This is the current list of admission controllers used by Docker:

### Default
- [NamespaceLifecycle](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#namespacelifecycle)
- [LimitRanger](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#limitranger)
- [ServiceAccount](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#serviceaccount)
- [PersistentVolumeLabel](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#persistentvolumelabel)
- [DefaultStorageClass](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#defaultstorageclass)
- [DefaultTolerationSeconds](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#defaulttolerationseconds)
- [NodeRestriction](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#noderestriction)
- [ResourceQuota](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#resourcequota)
- [PodNodeSelector](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#podnodeselector)
- [PodSecurityPolicy](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#podsecuritypolicy)

### Custom
- **UCPAuthorization**
This custom admission controller does several things:
    - Annotates Docker Compose-on-Kubernetes `Stack` resources with the identity
of the user performing the request so that the Docker Compose-on-Kubernetes
resource controller can manage `Stacks` with correct user authorization.
    - Detects when `ServiceAccount` resources are deleted so that they can be
correctly removed from UCP's Node scheduling authorization backend.
    - Simplifies creation of `RoleBindings` and `ClusterRoleBindings` resources by
automatically converting user, organization, and team Subject names into
their corresponding unique identifiers.
    - Prevents users from deleting the built-in `cluster-admin` `ClusterRole` or
`ClusterRoleBinding` resources.
    - Prevents under-privileged users from creating or updating `PersistintVolume`
resources with host paths.
    - Works in conjunction with the built-in `PodSecurityPolicies` admission
controller to prevent under-privileged users from creating `Pods` with
privileged options.
- **CheckImageSigning**
Enforces UCP's Docker Content Trust policy which, if enabled, requires that all
pods use container images which have been digitally signed by trusted and
authorized users which are members of one or more teams in UCP.
- **UCPNodeSelector**
Adds a `com.docker.ucp.orchestrator.kubernetes:*` toleration to pods in the
kube-system namespace and removes `com.docker.ucp.orchestrator.kubernetes`
tolerations from pods in other namespaces. This ensures that user workloads do
not run on swarm-only nodes, which UCP taints with
`com.docker.ucp.orchestrator.kubernetes:NoExecute`. It also adds a node
affinity to prevent pods from running on manager nodes depending on UCP's
settings.

**Note:** you cannot enable or disable your own admission controllers. For more information, see [Supportability of custom kubernetes flags in universal control plane](https://success.docker.com/article/supportability-of-custom-kubernetes-flags-in-universal-control-plane)

For more information about pod security policies in Docker, see [Pod security policies](/ee/ucp/kubernetes/pod-security-policies.md).
