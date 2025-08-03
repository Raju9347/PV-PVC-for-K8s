# PV-PVC-for-K8s
PV and PVC for kubernetes
PersistentVolume (PV)
A PV is a piece of storage in your Kubernetes cluster that’s either:
- Statically provisioned by an admin (e.g., NFS, iSCSI, cloud disk)
- Dynamically provisioned using a StorageClass
Key traits:
- Cluster-wide resource
- Independent of any pod
- Can be reused across pods (depending on access mode)
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-example
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: slow
  hostPath:
    path: /data
apiVersion: v1
kind: PersistentVolume
metadata:
  name: efs-pv
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: efs-sc
  csi:
    driver: efs.csi.aws.com
    volumeHandle: fs-12345678  # Replace with your actual EFS FileSystem ID

PersistentVolumeClaim (PVC)
A PVC is a request for storage by a pod. It asks for:
- Size (e.g., 5Gi)
- Access mode (e.g., ReadWriteOnce)
- StorageClass (optional)
Kubernetes matches the PVC to a suitable PV.
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: efs-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: efs-sc
  resources:
    requests:
      storage: 5Gi

 StorageClass
A StorageClass defines how volumes are dynamically provisioned. It includes:
- Provisioner (e.g., AWS EBS, GCE PD, CSI drivers)
- Parameters (e.g., type, IOPS, zone)
- Reclaim policy and volume binding mode
Admins can create multiple StorageClasses to offer different performance tiers

apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: efs-sc
provisioner: efs.csi.aws.com

