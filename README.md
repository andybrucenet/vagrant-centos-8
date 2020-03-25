sab-centos-8
============
Processing information for CentOS/8 support.

Initial Setup
-------------
These steps are for building the initial VM:
1. Review the `Vagrantfile` and verify it meets our needs.
1. Create the vm
    ```
    vagrant up
    ```
1. After VM initializes and updates the OS, stop the VM:
    ```
    vagrant halt
    ```
1. Add the Guest Additions ISO:
    ```
    VBoxManage storageattach sab-centos-8 \
      --storagectl IDE --port 0 --device 1 --type dvddrive \
      --medium '/Applications/VirtualBox.app/Contents/MacOS/VBoxGuestAdditions.iso'
    ```
1. Start the VM (`vagrant up`), SSH into the box (`vagrant ssh`) and mount the ISO image:
    ```
    sudo -i
    mkdir -p /mnt/cdrom
    mount /dev/cdrom /mnt/cdrom
    cd /mnt/cdrom
    sh ./VBoxLinuxAdditions.run
    ```
    When complete, shutdown the VM:
    ```
    shutdown -h -P now
    ```
1. After completion, shutdown the VM (`vagrant halt`).
1. Remove the Guest Additions ISO:
    ```
    VBoxManage storageattach sab-centos-8 \
      --storagectl IDE --port 0 --device 1 --type dvddrive \
      --medium 'none'
    ```

Update Vagrant Box
------------------
Run these steps as new base packages might need to be added to the base Vagrant box.

1. Start the VM, login, and update the VM with any changes (new packages / settings)

1. Package to a new box:
    ```
    rm -f ./$(basename $(pwd)).box
    vagrant package --output ./$(basename $(pwd)).box
    ```
1. Update the existing box registered in Vagrant by "adding" with a force option (overwrites):
    ```
   vagrant box add --force 'sab-centos/8' $(basename $(pwd)).box
    ```
1. Recreate any dependent VMs from the (now-updated) box.

