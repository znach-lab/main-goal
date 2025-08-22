# main-goal
Bu sayfada, projenin tamamının ana hedefi yer almaktadır.


```mermaid
%%{init: {'theme':'base', 'themeVariables': {
  'primaryColor': '#5a1f0f',
  'primaryBorderColor': '#fb923c',
  'primaryTextColor': '#ffedd5',
  'lineColor': '#fb923c',
  'tertiaryColor': '#fb923c',
  'clusterBkg': '#0b1020',
  'clusterBorderColor': '#f97316',
  'fontFamily': 'Inter, ui-sans-serif, system-ui'
}}}%%

flowchart TB

  %% Bare Metal Host (2 VM içerir)
  subgraph HOST["Bare Metal Host"]
    direction LR

    %% Sidecar VM
    subgraph VM1["Sidecar VM"]
      ANS["Ansible Controller"]
    end

    %% Vagrant ile oluşturulmuş Windows VM
    subgraph VM2["Windows VM (Vagrant)"]
      direction TB
      WIN["Multiboot Windows 11 VM"]
      DEB["Multiboot Debian VM (25GB)"]
      MAC["Multiboot macOS VM"]
      OMV(("OMV-share (20GB • D:)"))
    end
  end

  %% İlişkiler
  ANS -->|Provision / Yönetim| VM2

  %% Çift yönlü bağlantılar
  WIN <-->|SMB| OMV
  DEB <-->|ZFS| OMV
  MAC <-->|SMB| OMV


```
---
## Hiyerarch

  The main goal of our project is to enable users to access their files from anywhere, with complete freedom and flexibility. Files will be stored distributed throughout your own repositories, allowing you to have full access to them wherever you are. The topology seen above is the lab environment created for the first phase. This lab environment involves a topology where the OMV disk, located on a multi-boot computer, is automatically mounted regardless of the operating system it's running on, and automatically mounted to the operating system's home folder using a suitable file sharing protocol. This allows you to easily access your OMV-shared folder from anywhere, regardless of your location. The entire structure in the task is managed by sidecar ansible. You can view the task's process diagram below:

---
## Task 1 Summary
---

```mermaid
%%{init: {'theme':'default', 'themeVariables': {
  'lineColor': '#f97316',
  'actorLineColor': '#f97316',
  'signalColor': '#f97316',
  'signalTextColor': '#ffffff'
}}}%%


sequenceDiagram
    autonumber
    participant CMD
    participant AnsibleVM
    participant OMV as "OMV VM"
    participant WIN as "WinVM Dualboot"
    participant DEB as "DebianVM Dualboot"

    CMD->>+AnsibleVM: Type "vagrant up" on command line<br/>Ansible VM has a pre-up trigger<br/>First it allocates {subnet}.10 IP for<br/>WinVM Dualboot and DebianVM Dualboot<br/>Then it saves these IP values into hosts_win.ini and hosts_lin.ini
    AnsibleVM->>+OMV: OMV VM installation is triggered by a trigger<br/>AnsibleVM offline
    OMV->>+WIN: OMV VM is installed into disk.vmdk<br/>This disk folder will be shared with Windows via rsync<br/>The second trigger starts Dualboot Windows installation
    WIN-->>+AnsibleVM: After Dualboot Windows starts it becomes controllable with Ansible<br/>Then qemu is installed or updated and WHPX features are enabled<br/>After that the Vagrantfile sends a boot trigger to AnsibleVM for remaining operations
    AnsibleVM->>+WIN: When AnsibleVM is up it runs its playbooks in sequence
    AnsibleVM-->>+WIN: shrink_disk.yml shrinks 20 GB from C: and formats as D:// (exFAT)<br/>Reason: D:// will host OMV disks<br/>exFAT is readable by Windows - Linux - macOS<br/>This provides seamless access to the OMV VM
    AnsibleVM-->>+WIN: copy_disks.yml copies OMV disks disk.vmdk and disk1.vmdk<br/>from the shared folder to the new exFAT formatted D:// drive
    AnsibleVM-->>+WIN: start_vm.yml creates two files in Windows<br/>A PS1 file with qemu startup parameters using WHPX to boot disk.vmdk and disk1.vmdk in BIOS mode<br/>A BAT file in Startup that triggers the PS1 at every Windows boot<br/>This automatically starts qemu and the OMV VM
    AnsibleVM->>+DEB: After WinVM Dualboot tasks are done a trigger shuts down WinVM<br/>Then installation continues with a custom Debian ISO with preseed<br/>During install it gets an IP via DHCP and derives the subnet<br/>Then it assigns {subnet}.10 statically so DebianVM Dualboot is manageable with Ansible
    AnsibleVM-->>+DEB: install_qemu_on_debian.yml installs qemu automatically on DebianVM Dualboot
    AnsibleVM-->>+DEB: start_vm_when_reboot_debian.yml adds qemu parameters so that on every reboot<br/>a service auto starts the OMV VM using disk.vmdk and disk1.vmdk from the mounted OMV disk<br/>Port forwarding sets OMV SSH to 2222 and HTTP to 8080
    AnsibleVM-->>+DEB: change_vda.yml attaches the WinVM Dualboot virtual disk as a second disk<br/>so DebianVM can access the OMV VM on first boot<br/>On this VM the exFAT partition appears as vdb2 but on the main VM it will be vda2<br/>To avoid issues fstab is updated from vdb2 to vda2
    AnsibleVM-->>+DEB: After all Debian tasks finish shutdown_lin.yml powers off DebianVM Dualboot
    AnsibleVM-->>+CMD: When all VMs are ready extra VM definitions are cleaned via virsh<br/>Then ./change_boot.sh makes the DebianVM Dualboot disk bootable in WinVM<br/>and sets it as the first boot device
```
---



```mermaid

timeline
    title Project Roadmap
    section Başlangıç
      14 Ağustos : 🎯 Proje başlangıç
    
    section Lab Ortamı Oluşturma
      21 Ağustos : Task1
      29 Ağustos : -

    section Eylül Görevleri
      5 Eylül    : -
    

    section Ekim Görevleri
      3 Ekim     : -
  

    section Kasım Görevleri
      7 Kasım    : -
   
Windows 11
```
