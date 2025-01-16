# FIO Testing

## Install `fio`

```bash
dnf install fio
```

## FIO 

## Tests

Random Write Test for IOP/s

`sync; fio --randrepeat=1 --ioengine=libaio --direct=1 --name=test --filename=test --bs=4k --size=1G --readwrite=randwrite --ramp_time=4`

Random Read Test for IOP/s

`sync; fio --randrepeat=1 --ioengine=libaio --direct=1 --name=test --filename=test --bs=4k --size=1G --readwrite=randread --ramp_time=4`

Mixed Random Workload
`sync; fio --randrepeat=1 --ioengine=libaio --direct=1 --name=test --filename=test --bs=4k --size=1G --readwrite=readwrite --ramp_time=4`

Sequential Write Test for Throughput

`sync; fio --randrepeat=1 --ioengine=libaio --direct=1 --name=test --filename=test --bs=4M --size=1G --readwrite=write --ramp_time=4 `

Sequential Read Test for Throughput

sync;fio --randrepeat=1 --ioengine=libaio --direct=1 --name=4K --filename=/mnt/rbd/1G.file --bs=4K --size=1G --readwrite=write --ramp_time=4 -numjobs=1

**Simple Latency Test**
`fio --name=test-latency --filename=/dev/sdX --ioengine=libaio --direct=1 --rw=randread --bs=4k --iodepth=1 --runtime=60 --time_based`

**Simple Bandwidth Test**

`fio --name=throughput-test --filename=/dev/sdX --ioengine=libaio --direct=1 --rw=write --bs=1M --iodepth=32 --numjobs=4 --runtime=60 --time_based --group_reporting`

**Simple IOPS Test**

`fio --name=iops-test --filename=/dev/sdX --ioengine=libaio --direct=1 --rw=randread --bs=4k --iodepth=32 --numjobs=4 --runtime=60 --time_based --group_reporting`


---


## Regular tools

sync; dd if=./1G.file of=/mnt/rbd/dd-tests/1G.file bs=4K status=progress

fallocate -l 1G 1G.file && sync; dd if=./1G.file of=<device> bs=<4K, 64K, 128K, 1MB, 4M> status=progress && sync

---

## Results from some slow systems:

4K - 1073741824 bytes (1.1 GB) copied, 44.3478 s, 24.2 MB/s
64K - 1073741824 bytes (1.1 GB) copied, 25.8326 s, 41.6 MB/s
128K - 1073741824 bytes (1.1 GB) copied, 26.6365 s, 40.3 MB/s
1MB - 1073741824 bytes (1.1 GB) copied, 28.3581 s, 37.9 MB/s
4MB - 1073741824 bytes (1.1 GB) copied, 28.0496 s, 38.3 MB/s


## Examples

`git clone fio-testing`

export DIRECTORYONDEVICE=<absolute path to file on device to be tested>
fio --ioengine=libaio --invalidate=1 --ramp_time=5 --iodepth=32 --runtime=60 --time_based --direct=1 --bs=4k --size=100M --name=random-read --stonewall --rw=read --write_bw_log=./results/randomread-bw.log --write_iops_log=./results/randomread-iops.log --write_lat_log=./results/randomread-latency.log --filename=${DIRECTORYONDEVICE}/random.io
