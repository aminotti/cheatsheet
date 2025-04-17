# 🧠 ZFS Command Cheatsheet

---

## 🏗️ Pool Management

```bash
# Create a new pool
zpool create <pool> <device>
zpool create mypool /dev/sdX
zpool create mypool mirror /dev/sdX /dev/sdY
zpool create mypool raidz /dev/sdX /dev/sdY /dev/sdZ
zpool create mypool raidz2 /dev/sdX /dev/sdY /dev/sdZ /dev/sdW
zpool create mypool raidz3 /dev/sdX /dev/sdY /dev/sdZ /dev/sdW /dev/sdV

# List all pools
zpool list

# Destroy a pool
zpool destroy <pool>

# Add devices (e.g., mirror, raidz)
zpool add <pool> <vdev>...

# Import/export pool
zpool import
zpool export <pool>

# Scrub a pool (check data integrity)
zpool scrub <pool>

# Check pool status
zpool status
zpool status <pool>

# Get pool properties
zpool get all <pool>

# Set pool property
zpool set <property>=<value> <pool>
```

---

## 📂 Dataset & Filesystem Management

```bash
# Create a dataset (filesystem or volume)
zfs create <pool>/<dataset>

# List datasets
zfs list

# Set/get properties
zfs set <property>=<value> <dataset>
zfs get all <dataset>

# Destroy a dataset
zfs destroy <pool>/<dataset>

# Rename a dataset
zfs rename <old> <new>
```

---

## 🧷 Snapshots & Clones

```bash
# Create a snapshot
zfs snapshot <pool>/<dataset>@<snapshot>
zfs snapshot mypool/mydataset@snap1

# List snapshots
zfs list -t snapshot

# Destroy a snapshot
zfs destroy <pool>/<dataset>@<snapshot>

# Rollback to snapshot
zfs rollback <pool>/<dataset>@<snapshot>

# Clone a snapshot
zfs clone <pool>/<dataset>@<snapshot> <pool>/<new_dataset>
```

---

## 💾 Send/Receive (Backup & Restore)

```bash
# Send a snapshot to file
zfs send <pool>/<dataset>@<snapshot> > snapshot.zfs

# Receive (restore) a snapshot
zfs receive <pool>/<dataset> < snapshot.zfs

# Incremental send
zfs send -i <from-snap> <to-snap> > incr.zfs
```

---

## 🔒 Compression, Deduplication, Quotas

```bash
# Enable compression
zfs set compression=on <dataset>
zfs set compression=lz4 mypool/mydataset

# Enable deduplication (use with caution!)
zfs set dedup=on <dataset>

# Set quota (limit max space)
zfs set quota=10G <dataset>

# Set reservation (guarantees space)
zfs set reservation=5G <dataset>

zfs get quota,reservation mypool/mydataset
```

---

## 🧪 Useful Diagnostics

```bash
# Pool I/O stats
zpool iostat -v

# ZFS events log
zpool events

# History of ZFS commands
zpool history
```
## Encrypted Dataset

```bash
# Create encrypted dataset with passphrase
zfs create -o encryption=on -o keyformat=passphrase mypool/myencrypted-dataset
# Create encrypted dataset with keyfile
dd if=/dev/urandom of=/path/to/keyfile bs=32 count=1
zfs create -o encryption=on -o keyformat=raw -o keylocation=file:///path/to/keyfile mypool/myencrypted-dataset
# create encrypted dataset with keyfile or passphrase in a file
zfs create -o encryption=on -o keyformat=file -o keylocation=file:///path/to/keyfile mypool/myencrypted-dataset

# Verify
zfs get encryption,keyformat,keylocation mypool/mydataset

# Load the Encryption Key (will prompt for password)
zfs load-key mypool/encrypted-dataset

# Or use a key file
zfs load-key -L file:///path/to/keyfile mypool/encrypted-dataset

# Mount a dataset
zfs mount mypool/encrypted-dataset

# Check status
zfs get keystatus mypool/encrypted-dataset

# Lock the dataset
zfs unload-key mypool/encrypted-dataset

# Automatically mount at boot
zfs set keylocation=file:///etc/zfs/mykey tank/secure-data
zfs set canmount=on tank/secure-data
