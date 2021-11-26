#################################################################
# Global configuration overrides.
#
# These overrides will affect all helm charts (ie. applications)
# that are listed below and are 'enabled'.
#################################################################
global:
  # Change to an unused port prefix range to prevent port conflicts
  # with other instances running within the same k8s cluster
  nodePortPrefix: 302
  nodePortPrefixExt: 304
  masterPassword: secretpassword
  # ONAP Repository
  # Uncomment the following to enable the use of a single docker
  # repository but ONLY if your repository mirrors all ONAP
  # docker images. This includes all images from dockerhub and
  # any other repository that hosts images for ONAP components.
  #repository: nexus3.onap.org:10001
 
  # readiness check - temporary repo until images migrated to nexus3
  readinessRepository: oomk8s
  # logging agent - temporary repo until images migrated to nexus3
  loggingRepository: docker.elastic.co
 
  # image pull policy
  pullPolicy: IfNotPresent
 
  # override default mount path root directory
  # referenced by persistent volumes and log files
  persistence:
    mountPath: /dockerdata-nfs
 
  # flag to enable debugging - application support required
  debugEnabled: false
 
#################################################################
# Enable/disable and configure helm charts (ie. applications)
# to customize the ONAP deployment.
#################################################################
aaf:
  enabled: true
  aaf-service:
    readiness:
      initialDelaySeconds: 150
cassandra:
  enabled: true
  replicaCount: 3
  config:
    cluster_domain: cluster.local
    heap:
      max: 1G
      min: 256M
  liveness:
    initialDelaySeconds: 60
    periodSeconds: 20
    timeoutSeconds: 10
    successThreshold: 1
    failureThreshold: 3
    # necessary to disable liveness probe when setting breakpoints
    # in debugger so K8s doesn't restart unresponsive container
    enabled: true
 
  readiness:
    initialDelaySeconds: 120
    periodSeconds: 20
    timeoutSeconds: 10
    successThreshold: 1
    failureThreshold: 3
portal:
  enabled: true
sdc:
  enabled: true
  config:
    environment:
      vnfRepoPort: 8703
  sdc-be:
    config:
      javaOptions: "-Xmx1g -Xms512m"
    liveness:
      periodSeconds: 300
      timeoutSeconds: 180
    readiness:
      periodSeconds: 300
      timeoutSeconds: 240
  sdc-fe:
    resources:
      small:
        limits:
          cpu: 1
          memory: 2Gi
        requests:
          cpu: 100m
          memory: 500Mi
