# Firecracker experiments


Just a few experiments with AWS Firecracker microVM. The repository at the moment consists of: 

  - A Centos7 rootfs that can be used to fire up a Centos7 Firecracker microVM
  - A vmlinux.bin Kernel binary file
  
### How to run a firecracker VM using the rootfs image and the kernel binary file:
  - Run the firecracker binary
  -- `firecracker --api-sock /tmp/firecracker.socket`
  - In a different terminal window, set the guest kernel(hello-vmlinux.bin): 
    ```
    curl --unix-socket /tmp/firecracker.socket -i \
        -X PUT 'http://localhost/boot-source'   \
        -H 'Accept: application/json'           \
        -H 'Content-Type: application/json'     \
    -   d '{
            "kernel_image_path": "./hello-vmlinux.bin",
            "boot_args": "console=ttyS0 reboot=k panic=1 pci=off"
        }'
    ```
- Set the guest rootfs(centos7-rootfs.ext4): 
    ```
    curl --unix-socket /tmp/firecracker.socket -i \
        -X PUT 'http://localhost/drives/rootfs' \
        -H 'Accept: application/json'           \
        -H 'Content-Type: application/json'     \
        -d '{
            "drive_id": "rootfs",
            "path_on_host": "./centos7-rootfs.ext4",
            "is_root_device": true,
            "is_read_only": false
        }'
    ```
- Start the guest machine: 
    ```
    curl --unix-socket /tmp/firecracker.socket -i \
        -X PUT 'http://localhost/actions'       \
        -H  'Accept: application/json'          \
        -H  'Content-Type: application/json'    \
        -d '{
            "action_type": "InstanceStart"
         }'`
    ```

Going back to your first shell, you should now see a serial TTY prompting you to log into the guest machine. If you used the Centos7-rootfs.ext4 image you can log into the guest using:
`username: root`
`password: toor`

### Run using the firectl command-line tool
- Run the following command using the firectl command line tool
-- ```./firectl --kernel=./hello-vmlinux.bin --root-drive=./centos7-rootfs.ext4 --kernel-opts="console=ttyS0 noapic reboot=k panic=1 pci=off nomodules rw" ```

