## v0.9.5

Changes since `v0.9.4`:

## Changes by Kind

### Bug or Regression

- Fixes a bug that would result in default values not being properly set on creation for enabled integrations whose API was not available when the Kueue controller started. (#4559, @dgrove-oss)
- Helm: Fixed a bug that prometheus namespace is enforced with namespace the same as kueue-controller-manager (#4488, @kannon92)
- TAS: Fix a bug that TopolologyUngator cound not be triggered the leader change when enabled HA mode (#4657, @tenzen-y)
- Update FairSharing to be incompatible with ClusterQueue.Preemption.BorrowWithinCohort. Using these parameters together is a no-op, and will be validated against in future releases. This change fixes an edge case which triggered an infinite preemption loop when these two parameters were combined. (#4165, @gabesaba)

### Other (Cleanup or Flake)

- Publish helm charts to the Kueue staging repository `http://us-central1-docker.pkg.dev/k8s-staging-images/kueue/charts`,
  so that they can be promoted to the permanent location under `registry.k8s.io/kueue/charts`. (#4685, @mimowo)

## v0.9.4

Changes since `v0.9.3`:

## Changes by Kind

### Bug or Regression

- Add missing external types to apply configurations (#4202, @astefanutti)
- Disable the StatefulSet webhook in the kube-system and kueue-system namespaces by default.
  This aligns the default StatefulSet webhook configuration with the Pod and Deployment configurations. (#4161, @@dgrove-oss)
- Fix a bug is incorrect field path in inadmissible reasons and messages when Pod resources requests do not satisfy LimitRange constraints. (#4290, @tenzen-y)
- Fix a bug is incorrect field path in inadmissible reasons and messages when container requests exceed limits (#4246, @tenzen-y)
- Fix a bug that allowed unsupported changes to some PodSpec fields which were resulting in the StatefulSet getting stuck on Pods with schedulingGates.

  The validation blocks mutating the following Pod spec fields: `nodeSelector`, `affinity`, `tolerations`, `runtimeClassName`, `priority`, `topologySpreadConstraints`, `overhead`, `resourceClaims`, plus container (and init container) fields: `ports` and `resources.requests`.

  Mutating other fields, such as container image, command or args, remains allowed and supported. (#4154, @mbobrovskyi)
- Fix a bug that doesn't allow Kueue to delete Pods after a StatefulSet is deleted. (#4206, @mbobrovskyi)
- Fix a bug that prevented tracking some of the controller-runtime metrics in Prometheus. (#4227, @tenzen-y)
- Fix a bug truncating AdmissionCheck condition message at `1024` characters when creation of the associated ProvisioningRequest or PodTemplate fails.
  Instead, use the `32*1024` characters limit as for condition messages. (#4195, @mbobrovskyi)
- Fix the bug that prevented Kueue from updating the AdmissionCheck state in the Workload status on a ProvisioningRequest creation error. (#4118, @mbobrovskyi)
- Helm: Fix the unspecified LeaderElection Role and Rolebinding namespaces (#4386, @eric-higgins-ai)
- MultiKueue: Do not update the status of the Job on the management cluster while the Job is suspended. This is updated  for jobs represented by JobSet, Kubeflow Jobs and MPIJob. (#4085, @IrvingMg)
- Propagate the top-level setting of the `kueue.x-k8s.io/priority-class` label to the PodTemplate for
  Deployments and StatefulSets. This way the Workload Priority class is no longer ignored by the workloads. (#4036, @Abirdcfly)
- TAS: Fix a bug that unschedulable nodes (".spec.unschedulable=true") are counted as allocatable capacities (#4209, @tenzen-y)
- TAS: Fixed a bug that allows to create a JobSet with both kueue.x-k8s.io/podset-required-topology and kueue.x-k8s.io/podset-preferred-topology annotations set on the PodTemplate. (#4156, @mbobrovskyi)

### Other (Cleanup or Flake)

- Renamed Log key from "attemptCount" to "schedulingCycleCount". This key tracks how many scheduling cycles we have done since starting Kueue. (#4241, @tenzen-y)

## v0.9.3

Changes since `v0.9.2`:

## Changes by Kind

### Bug or Regression

- Disable the unnecessary Validating Admission Policy for the visibility server, and drop the associated RBAC permissions to make the server minimal. This also prevents periodic error logging on clusters above Kubernetes 1.29+. (#3977, @varshaprasad96)
- Fix building TAS assignments for workloads with multiple PodSets (eg. JobSet or kubeflow Jobs). The assignment was computed independently for the PodSets which could result in conflicts rendering the pods unschedulable by the kube-scheduler. (#3972, @kerthcet)
- Fix the bug that prevented scaling StatefulSets which aren't managed by Kueue when the "statefulset" integration is enabled. (#3999, @mbobrovskyi)

## v0.9.2

Changes since `v0.9.1`:

## Changes by Kind

### Bug or Regression

- Added validation for Deployment queue-name to fail fast (#3580, @mbobrovskyi)
- Added validation for StatefulSet queue-name to fail fast. (#3585, @mbobrovskyi)
- Fix a bug which occasionally prevented updates to the PodTemplate of the Job on the management cluster
  when starting a Job (e.g. updating nodeSelectors), when using `MultiKueueBatchJobWithManagedBy` enabled. (#3731, @IrvingMg)
- Fix dropping of reconcile requests for non-leading replica, which was resulting in workloads
  getting stuck pending after the rolling restart of Kueue. (#3613, @mimowo)
- Fix memory leak due to workload entries left in MultiKueue cache. The leak affects the 0.9.0 and 0.9.1
  releases which enable MultiKueue by default, even if MultiKueue is not explicitly used on the cluster. (#3843, @mimowo)
- Fix misleading log messages from workload_controller indicating not existing LocalQueue or
  Cluster Queue. For example "LocalQueue for workload didn't exist or not active; ignored for now"
  could also be logged the ClusterQueue does not exist. (#3832, @PBundyra)
- Fix preemption when using Hierarchical Cohorts by considering as preemption candidates workloads
  from ClusterQueues located further in the hierarchy tree than direct siblings. (#3705, @gabesaba)
- Fix scheduling of workload which does not include the toleration for the taint in ResourceFlavor's spec.nodeTaints,
  if the toleration is specified on the ResourceFlavor itself. (#3724, @PBundyra)
- Fix the bug which prevented the use of MultiKueue if there is a CRD which is not installed
  and removed from the list of enabled integrations. (#3631, @mszadkow)
- TAS: Fixed bug that doesn't allow to update cache on delete Topology. (#3655, @mbobrovskyi)
- TAS: The CQ referencing a Topology is deactivated if the topology does not exist. (#3819, @mimowo)

### Other (Cleanup or Flake)

- Replace deprecated gcr.io/kubebuilder/kube-rbac-proxy with registry.k8s.io/kubebuilder/kube-rbac-proxy. (#3749, @mbobrovskyi)

## v0.9.1

Changes since `v0.9.0`:

> [!NOTE]
> The previously anticipated feature for Topology Aware Scheduling (TAS) Rank Ordering is not part of this 
> patch release. This functionality has been deferred and will be included in an upcoming release.

## Changes by Kind

### Bug or Regression

- Change, and in some scenarios fix, the status message displayed to user when a workload doesn't fit in available capacity. (#3549, @gabesaba)
- Determine borrowing more accurately, allowing preempting workloads which fit in nominal quota to schedule faster (#3550, @gabesaba)
- Fix accounting for usage coming from TAS workloads using multiple resources. The usage was multiplied
  by the number of resources requested by a workload, which could result in under-utilization of the cluster.
  It also manifested itself in the message in the workload status which could contain negative numbers. (#3513, @mimowo)
- Fix computing the topology assignment for workloads using multiple PodSets requesting the same
  topology. In particular, it was possible for the set of topology domains in the assignment to be empty,
  and as a consequence the pods would remain gated forever as the TopologyUngater would not have
  topology assignment information. (#3524, @mimowo)
- Fix running Job when parallelism < completions, before the fix the replacement pods for the successfully
  completed Pods were not ungated. (#3561, @mimowo)
- Fix the flow of deactivation for workloads due to rejected AdmissionChecks.
  Now, all AdmissionChecks are reset back to the Pending state on eviction (and deactivation in particular),
  and so an admin can easily re-activate such a workload manually without tweaking the checks. (#3518, @KPostOffice)

## v0.9.0

Changes since `v0.8.0`:

## Urgent Upgrade Notes

### (No, really, you MUST read this before you upgrade)

- Changed the `type` of `Pending` events, emitted when a Workload can't be admitted, from `Normal` to `Warning`.

  Update tools that process this event if they depend on the event `type`. (#3264, @kebe7jun)
 - Deprecated SingleInstanceInClusterQueue and FlavorIndependent status conditions.

  the Admission check status conditions "FlavorIndependent" and "SingleInstanceInClusterQueue" are no longer supported by default.
  If you were using any of these conditions for your external AdmissionCheck you need to enable the `AdmissionCheckValidationRules` feature gate.
  For the future releases you will need to provide validation by an external controller. (#3254, @mszadkow)
 - Promote MultiKueue API and feature gate to Beta. The MultiKueue feature gate is now beta and enabled by default.

  The MultiKueue specific types are now part of the Kueue's `v1beta1` API. `v1alpha` types are no longer supported. (#3230, @trasc)
 - Promoted VisibilityOnDemand to Beta and enabled by default.

  The v1alpha1 Visibility API is deprecated and will be removed in the next release. Please use v1beta1 instead. (#3008, @mbobrovskyi)
 - Provides more details on the reasons for ClusterQueues being inactive.
  If you were watching for the reason `CheckNotFoundOrInactive` in the ClusterQueue condition, watch `AdmissionCheckNotFound` and `AdmissionCheckInactive` instead. (#3127, @trasc)
 - The QueueVisibility feature and its corresponding API was deprecated.

  The QueueVisibility feature and its corresponding API was deprecated and will be removed in the v1beta2. Please use VisibilityOnDemand (https://kueue.sigs.k8s.io/docs/tasks/manage/monitor_pending_workloads/pending_workloads_on_demand/) instead. (#3110, @mbobrovskyi)

## Upgrading steps

### 1. Backup MultiKueue Resources (skip if you are not using MultiKueue):
```
kubectl get multikueueclusters.kueue.x-k8s.io,multikueueconfigs.kueue.x-k8s.io -A -o yaml > mk.yaml
```

### 2. Update apiVersion in Backup File (skip if you are not using MultiKueue):
Replace `v1alpha1` with `v1beta1` in `mk.yaml` for all resources:
```
sed -i -e 's/v1alpha1/v1beta1/g' mk.yaml
```

### 3. Delete old CRDs:
```
kubectl delete crd multikueueclusters.kueue.x-k8s.io
kubectl delete crd multikueueconfigs.kueue.x-k8s.io
```

### 4.Install Kueue v0.9.x:
Follow the instruction [here](https://kueue.sigs.k8s.io/docs/installation/#install-a-released-version) to install.

### 5. Restore MultiKueue Resources (skip if you are not using MultiKueue):
```
kubectl apply -f mk.yaml
```

## Changes by Kind

### Feature

- Add gauge metric admission_cycle_preemption_skips that reports the number of Workloads in a ClusterQueue
  that got preemptions candidates, but had to be skipped in the last cycle. (#2919, @alculquicondor)
- Add integration for Deployment, where each Pod is treated as a separate Workload. (#2813, @vladikkuzn)
- Add integration for StatefulSet where Pods are managed by the pod-group integration. (#3001, @vladikkuzn)
- Added FlowSchema and PriorityLevelConfiguration for Visibility API. (#3043, @mbobrovskyi)
- Added a new optional `resource.transformations` section to the `Configuration` API  that enables limited customization
  of how the resource requirements of a Workload are computed from the resource requests and limits of a Job. (#3026, @dgrove-oss)
- Added a way to specify dependencies between job integrations. (#2768, @trasc)
- Best effort support for scenarios when the Job is created at the same time as prebuilt workload or momentarily before the workload. In that case an error is logged to indicate that creating a Job before prebuilt-workload is outside of the intended use. (#3255, @mbobrovskyi)
- CLI: Added EXEC TIME column on kueuectl list workload command. (#2977, @mbobrovskyi)
- CLI: Added list pods for a job command. (#2280, @Kavinraja-G)
- CLI: Use protobuf encoding for core K8s APIs in kueuectl. (#3077, @tosi3k)
- Calculate AllocatableResourceGeneration more accurately. This fixes a bug where a workload might not have the Flavors it was assigned in a previous scheduling cycle invalidated, when the resources in the Cohort had changed. This bug could occur when other ClusterQueues were deleted from the Cohort. (#2984, @gabesaba)
- Detect and enable support for job CRDs installed after Kueue starts. (#2574, @ChristianZaccaria)
- Exposed available ResourceFlavors from the ClusterQueue in the LocalQueue status. (#3143, @mbobrovskyi)
- Graduated LendingLimit to Beta and enabled by default. (#2909, @macsko)
- Graduated MultiplePreemptions to Beta and enabled by default. (#2864, @macsko)
- Helm: Support the topologySpreadConstraints and PodDisruptionBudget (#3295, @woehrl01)
- Hierarchical Cohorts, introduced with the v1alpha1 Cohorts API, allow users to group resources in an arbitrary tree structure. Additionally, quotas and limits can now be defined directly at the Cohort level. See #79 for more details. (#2693, @gabesaba)
- Included visibility-api.yaml as a part of main.yaml (#3084, @mbobrovskyi)
- Introduce the "kueue.x-k8s.io/pod-group-fast-admission" annotation to Plain Pod integration.

  If the PlainPod has the annotation and is part of the Plain PodGroup, the Kueue will admit the Plain Pod regardless of whether all PodGroup Pods are created. (#3189, @vladikkuzn)
- Introduce the new PodTemplate annotation kueue.x-k8s.io/workload, and label kueue.x-k8s.io/podset.
  The annotation and label are alpha-level and gated by the new TopologyAwareScheduling feature gate. (#3228, @mimowo)
- Label `kueue.x-k8s.io/managed` is now added to PodTemplates created via ProvisioningRequest by Kueue (#2877, @PBundyra)
- MultiKueue: Add support for  MPIJob  `spec.runPolicy.managedBy` field (#3289, @mszadkow)
- MultiKueue: Support for the Kubeflow MPIJob (#2880, @mszadkow)
- MultiKueue: Support for the Kubeflow PaddleJob (#2744, @mszadkow)
- MultiKueue: Support for the Kubeflow PyTorchJob (#2735, @mszadkow)
- MultiKueue: Support for the Kubeflow TFJob (#2626, @mszadkow)
- MultiKueue: Support for the Kubeflow XGBoostJob (#2746, @mszadkow)
- ProvisioningRequest: Record the ProvisioningRequest creation errors to event and ProvisioningRequest status. (#3056, @IrvingMg)
- ProvisioningRequestConfig API has now `RetryStrategy` field that allows users to configure retries per ProvisioningRequest class. By default retry releases allocated quota in Kueue. (#3375, @PBundyra)
- Publish images via artifact registry (#2476, @IrvingMg)
- Support Topology Aware Scheduling (TAS) in Kueue in the Alpha version, along with the new Topology API
  to specify the ordered list of node labels corresponding to the different levels of hierarchy in data-centers
  (like racks or blocks).

  Additionally, we introduce the pair of Job-level annotations: `http://kueue.x-k8s.io/podset-required-topology`
  and `kueue.x-k8s.io/podset-preferred-topology` which users can use to indicate their preference for the
  Jobs to run all their Pods within a topology domain at the indicated level. (#3235, @mimowo)
- Support for JobSet 0.6 (#3034, @kannon92)
- Support for Kubernetes 1.31 (#2402, @mbobrovskyi)
- Support the Job-level API label, called `kueue.x-k8s.io/max-exec-time-seconds`, that users
  can use to enforce the maximum execution time for their job. The execution time is only
  accumulated when the Job is running (the corresponding Workload is admitted).
  The corresponding Workload is deactivated after the time is exceeded. (#3191, @trasc)

### Documentation

- Adds installing kubectl-kueue plugin via Krew guide. (#2666, @mbobrovskyi)
- Documentation on how to use Kueue for Deployments is added (#2698, @vladikkuzn)

### Bug or Regression

- CLI: Delete the corresponding Job when deleting a Workload. (#2992, @mbobrovskyi)
- CLI: Support `-` and `.` in the resource flavor name on `create cq` (#2703, @trasc)
- Fix a bug that could delay the election of a new leader in the Kueue with multiple replicas env. (#3093, @tenzen-y)
- Fix over-admission after deleting resources from borrowing ClusterQueue. (#2873, @mbobrovskyi)
- Fix resource consumption computation for partially admitted workloads. (#3118, @trasc)
- Fix restoring parallelism on eviction for partially admitted batch/Jobs. (#3153, @trasc)
- Fix some scenarios for partial admission which are affected by wrong calculation of resources
  used by the incoming workload which is partially admitted and preempting. (#2826, @trasc)
- Fix support for kuberay 1.2.x (#2960, @mbobrovskyi)
- Fix webook validation for batch/Job to allow partial admission of a Job to use all available resources.
  It also fixes a scenario of partial re-admission when some of the Pods are already reclaimed. (#3152, @trasc)
- Helm: Fix a bug for "unclosed action error". (#2683, @mbobrovskyi)
- Prevent infinite preemption loop when PrioritySortingWithinCohort=false
  is used together with borrowWithinCohort. (#2807, @mimowo)
- Prevent job webhooks from dropping fields for newer API fields when Kueue libraries are behind the latest released CRDs. (#3132, @alculquicondor)
- RayJob's implementation of Finished() now inspects at JobDeploymentStatus (#3120, @andrewsykim)
- Support for helm charts in the us-central1-docker.pkg.dev/k8s-staging-images/charts repository (#2680, @IrvingMg)
- Update Flavor selection logic to prefer Flavors which allow reclamation of lent nominal quota, over Flavors which require preempting workloads within the ClusterQueue. This matches the behavior in the single Flavor case. (#2811, @gabesaba)
- Workload is requeued with all AdmissionChecks set to Pending if there was an AdmissionCheck in Retry state. (#3323, @PBundyra)
- Account for NumOfHosts when calculating PodSet assignments for RayJob and RayCluster (#3384, @andrewsykim)

### Other (Cleanup or Flake)

- Add a jobframework.BaseWebhook that can be used for custom job integrations (#3102, @alculquicondor)
